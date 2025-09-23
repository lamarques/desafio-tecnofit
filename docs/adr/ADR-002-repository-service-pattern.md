# ADR-002: Repository Pattern com Service Layer

## Status
Aceito

## Contexto
O sistema precisa de uma estratégia para:
- Separar lógica de negócio do acesso aos dados
- Facilitar testes unitários
- Permitir mudanças na camada de persistência
- Centralizar regras de negócio

## Decisão
Implementar **Repository Pattern** combinado com **Service Layer**:

### Repository Pattern
- Interface abstrata para acesso aos dados
- Implementações concretas para diferentes fontes
- Isolamento da lógica de persistência

### Service Layer  
- Concentração da lógica de negócio
- Orquestração entre múltiplos repositórios
- Transações e validações complexas

### Estrutura:
```
app/Repository/
├── interfaces/
│   ├── AccountRepositoryInterface.php
│   └── WithdrawRepositoryInterface.php
├── AccountRepository.php
└── WithdrawRepository.php

app/Service/
├── WithdrawService.php
├── BalanceService.php
└── EmailService.php
```

## Consequências

### Positivas
- **Testabilidade**: Fácil mock dos repositories
- **Flexibilidade**: Mudança de banco sem impactar negócio
- **Centralização**: Lógica de negócio concentrada nos services
- **Reutilização**: Services podem ser chamados de diferentes controllers
- **Transações**: Controle transacional centralizado nos services

### Negativas
- **Boilerplate**: Mais interfaces e classes
- **Overhead**: Camada adicional de abstração
- **Learning curve**: Desenvolvedores precisam entender os padrões

## Implementação
```php
interface AccountRepositoryInterface 
{
    public function findById(string $id): ?Account;
    public function updateBalance(string $id, float $amount, int $version): bool;
}

class WithdrawService 
{
    public function __construct(
        private AccountRepositoryInterface $accountRepo,
        private WithdrawRepositoryInterface $withdrawRepo
    ) {}
    
    public function processImmediate(WithdrawRequest $request): WithdrawResult
    {
        // Lógica de negócio centralizada
    }
}
```

## Alternativas Consideradas
- **Active Record**: Descartado por misturar responsabilidades
- **Data Mapper simples**: Insuficiente para lógica complexa
- **CQRS**: Complexo demais para o escopo atual

---
**Data**: 23/09/2025  
**Autor**: Rogerio Lamarques + GitHub Copilot  
**Revisores**: -
