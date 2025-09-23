# ADR-004: Sistema de Filas para Processamento Assíncrono

## Status
Aceito

## Contexto
O sistema precisa processar operações que não devem bloquear a resposta da API:
- Envio de emails de notificação
- Operações que podem falhar e precisam retry
- Processamento que pode ser demorado
- Melhor experiência do usuário (resposta rápida)

## Decisão
Implementar **Sistema de Filas (Queue System)** para processamento assíncrono usando:

### Tecnologia: Redis + Hyperf Queue
- **Driver**: Redis (performático e confiável)
- **Framework**: Hyperf native queue system
- **Workers**: Processos dedicados para consumir filas

### Casos de Uso:
1. **Envio de emails**: Após processamento de saque
2. **Logs assíncronos**: Para operações pesadas de log
3. **Integrações externas**: Futuras integrações com APIs

### Estrutura:
```
app/Job/
├── SendWithdrawEmailJob.php
├── LogOperationJob.php
└── interfaces/

config/autoload/
└── async_queue.php
```

## Consequências

### Positivas
- **Performance**: API responde imediatamente
- **Resiliência**: Retry automático em falhas
- **Escalabilidade**: Workers independentes podem escalar
- **Reliability**: Jobs persistidos, não se perdem
- **Monitoring**: Fácil monitoramento de filas
- **User Experience**: Resposta rápida ao usuário

### Negativas
- **Complexidade**: Infraestrutura adicional (Redis)
- **Debugging**: Mais difícil debugar problemas assíncronos
- **Eventual consistency**: Emails podem chegar com delay
- **Resource usage**: Workers consomem recursos adicionais

## Implementação

### Job Example:
```php
class SendWithdrawEmailJob extends AbstractJob
{
    public function __construct(
        private string $email,
        private float $amount,
        private string $pixKey,
        private DateTime $processedAt
    ) {}

    public function handle(): void
    {
        // Lógica de envio de email
        // Com retry automático em caso de falha
    }
}
```

### Service Integration:
```php
class WithdrawService 
{
    public function processImmediate(WithdrawRequest $request): WithdrawResult
    {
        // 1. Processar saque
        $withdraw = $this->processWithdraw($request);
        
        // 2. Enqueue email job (não bloqueia)
        $this->queue->push(new SendWithdrawEmailJob(
            $request->pixKey,
            $request->amount,
            $withdraw->pixKey,
            now()
        ));
        
        // 3. Retornar resultado imediatamente
        return new WithdrawResult($withdraw);
    }
}
```

### Configuration:
```php
// config/autoload/async_queue.php
return [
    'default' => [
        'driver' => 'redis',
        'channel' => 'queue',
        'timeout' => 2,
        'retry_seconds' => [1, 5, 10, 20],
        'handle_timeout' => 10,
        'processes' => 1,
    ],
];
```

## Monitoramento
- **Queue size**: Número de jobs pendentes  
- **Processing time**: Tempo médio de processamento
- **Failure rate**: Taxa de falhas por tipo de job
- **Worker health**: Status dos workers

## Alternativas Consideradas
- **Processamento síncrono**: Descartado por impacto na performance
- **RabbitMQ**: Mais complexo que necessário para o escopo
- **Database queue**: Menor performance que Redis
- **SQS/Cloud queues**: Adiciona dependência externa

---
**Data**: 23/09/2025  
**Autor**: Rogerio Lamarques + GitHub Copilot  
**Revisores**: -
