# ADR-003: UUID como Primary Keys

## Status
Aceito

## Contexto
O sistema precisa de identificadores únicos para as entidades que atendam:
- Escalabilidade horizontal (múltiplas instâncias)
- Segurança (IDs não previsíveis)
- Integração com sistemas externos
- Performance em ambiente distribuído

## Decisão
Usar **UUID (Universally Unique Identifier)** como chave primária em todas as tabelas principais:

- `account.id` → UUID
- `account_withdraw.id` → UUID  
- `account_withdraw_pix.account_withdraw_id` → UUID

### Formato escolhido: UUID v4
- Geração aleatória
- Não depende de timestamp ou MAC address
- Máxima portabilidade

## Consequências

### Positivas
- **Escalabilidade horizontal**: Zero conflitos entre instâncias
- **Segurança**: IDs não são sequenciais/previsíveis
- **Distribuição**: Geração local sem coordenação central
- **Integração**: Padrão amplamente aceito
- **Merge de dados**: Fácil consolidação de diferentes fontes
- **Replicação**: Sem problemas de sincronização

### Negativas
- **Tamanho**: 36 caracteres vs 4-8 bytes de integer
- **Performance**: Índices ligeiramente maiores
- **Legibilidade**: Menos amigável para debugging manual
- **Ordenação**: Não fornece ordem cronológica natural

## Implementação

### MySQL Configuration:
```sql
CREATE TABLE account (
    id CHAR(36) PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    balance DECIMAL(15,2) NOT NULL DEFAULT 0.00,
    version INT NOT NULL DEFAULT 1,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### PHP Implementation:
```php
use Ramsey\Uuid\Uuid;

class Account extends Model 
{
    protected $keyType = 'string';
    public $incrementing = false;
    
    protected static function boot()
    {
        parent::boot();
        static::creating(function ($model) {
            $model->id = Uuid::uuid4()->toString();
        });
    }
}
```

## Mitigação de Problemas
- **Performance**: Índices otimizados e connection pooling
- **Storage**: Aceitável considerando os benefícios
- **Debug**: Ferramentas de admin com busca por outros campos

## Alternativas Consideradas
- **Auto-increment integers**: Descartado por problemas de escalabilidade
- **UUID v1**: Descartado por questões de privacidade (MAC address)
- **ULID**: Considerado, mas UUID é mais padrão no ecossistema PHP

---
**Data**: 23/09/2025  
**Autor**: Rogerio Lamarques + GitHub Copilot  
**Revisores**: -
