# ADR-005: Versionamento Otimista para Controle de Concorrência

## Status
Aceito

## Contexto
O sistema de saque precisa lidar com concorrência na atualização do saldo da conta:
- Múltiplas requisições simultâneas de saque
- Prevenção de saldo negativo
- Performance em alta concorrência
- Consistência dos dados financeiros

Cenário problemático:
```
Thread A: Lê saldo R$ 100
Thread B: Lê saldo R$ 100  
Thread A: Saca R$ 80 → Saldo R$ 20
Thread B: Saca R$ 50 → Saldo R$ 50 (ERRO!)
```

## Decisão
Implementar **Optimistic Locking** usando campo `version` na tabela `account`:

### Estratégia:
1. Adicionar campo `version INT` na tabela `account`
2. Incrementar version a cada atualização
3. Verificar version na condição WHERE do UPDATE
4. Falhar e retry se version mudou

### Schema:
```sql
ALTER TABLE account ADD COLUMN version INT NOT NULL DEFAULT 1;

-- Índice para performance
CREATE INDEX idx_account_version ON account(id, version);
```

## Consequências

### Positivas  
- **Performance**: Sem locks de banco de dados
- **Escalabilidade**: Funciona bem em ambiente distribuído
- **Consistência**: Detecta conflitos de concorrência
- **Simplicidade**: Implementação relativamente simples
- **Non-blocking**: Não bloqueia outras transações

### Negativas
- **Retry logic**: Necessário implementar retry automático
- **Complexity**: Lógica adicional na aplicação
- **User experience**: Possível delay em cenários de alta concorrência

## Implementação

### Repository Method:
```php
class AccountRepository implements AccountRepositoryInterface
{
    public function updateBalance(string $accountId, float $newBalance, int $expectedVersion): bool
    {
        $affected = DB::update(
            'UPDATE account 
             SET balance = ?, version = version + 1, updated_at = NOW()
             WHERE id = ? AND version = ?',
            [$newBalance, $accountId, $expectedVersion]
        );
        
        return $affected > 0; // true se atualizou, false se version mudou
    }
    
    public function getBalanceWithVersion(string $accountId): ?array
    {
        return DB::selectOne(
            'SELECT balance, version FROM account WHERE id = ?',
            [$accountId]
        );
    }
}
```

### Service with Retry:
```php
class WithdrawService
{
    private const MAX_RETRIES = 3;
    
    public function processImmediate(WithdrawRequest $request): WithdrawResult
    {
        $retries = 0;
        
        while ($retries < self::MAX_RETRIES) {
            try {
                return $this->attemptWithdraw($request);
            } catch (ConcurrencyException $e) {
                $retries++;
                if ($retries >= self::MAX_RETRIES) {
                    throw new MaxRetriesExceededException();
                }
                // Pequeno delay exponencial
                usleep(pow(2, $retries) * 1000); // 2ms, 4ms, 8ms
            }
        }
    }
    
    private function attemptWithdraw(WithdrawRequest $request): WithdrawResult
    {
        // 1. Buscar saldo atual com version
        $account = $this->accountRepo->getBalanceWithVersion($request->accountId);
        
        // 2. Validar saldo suficiente
        if ($account['balance'] < $request->amount) {
            throw new InsufficientBalanceException();
        }
        
        // 3. Calcular novo saldo
        $newBalance = $account['balance'] - $request->amount;
        
        // 4. Tentar atualizar com version check
        $updated = $this->accountRepo->updateBalance(
            $request->accountId,
            $newBalance,
            $account['version']
        );
        
        if (!$updated) {
            throw new ConcurrencyException('Account was modified by another transaction');
        }
        
        // 5. Registrar transação
        return $this->recordWithdraw($request);
    }
}
```

### Metrics & Monitoring:
```php
class WithdrawService 
{
    private function logConcurrencyRetry(int $attempt): void
    {
        Log::info('Concurrency retry', [
            'attempt' => $attempt,
            'account_id' => $this->currentAccountId
        ]);
        
        // Incrementar métrica
        Metrics::increment('withdraw.concurrency_retry');
    }
}
```

## Alternativas Consideradas

### Pessimistic Locking (SELECT FOR UPDATE)
- **Prós**: Garantia absoluta sem retry
- **Contras**: Performance ruim, deadlocks possíveis
- **Veredicto**: Descartado por impacto na performance

### Database Transactions com Isolation
- **Prós**: Controle nativo do banco
- **Contras**: Menor performance, complexidade de configuração
- **Veredicto**: Descartado por preferir controle na aplicação

### Redis Distributed Lock
- **Prós**: Funciona em ambiente distribuído
- **Contras**: Infraestrutura adicional, single point of failure
- **Veredicto**: Deixado para versão futura se necessário

---
**Data**: 23/09/2025  
**Autor**: Rogerio Lamarques + GitHub Copilot  
**Revisores**: -
