# Arquitetura do Sistema - Saque PIX

## 📋 Visão Geral

Sistema de saque PIX para plataforma de conta digital, desenvolvido com PHP Hyperf 3, focado em performance, escalabilidade e observabilidade.

## 🎯 Objetivos Arquiteturais

- **Performance**: Processamento rápido de transações
- **Escalabilidade Horizontal**: Design stateless e distribuído
- **Observabilidade**: Logs estruturados e métricas
- **Segurança**: Validações robustas e proteção de dados
- **Manutenibilidade**: Código limpo e bem estruturado

## 🏗️ Arquitetura Geral

### Visão de Alto Nível

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Load Balancer │───▶│  API Gateway     │───▶│   Application   │
└─────────────────┘    └──────────────────┘    │   Instances     │
                                               └─────────────────┘
                                                        │
                                               ┌─────────────────┐
                                               │   Message       │
                                               │   Queue         │
                                               └─────────────────┘
                                                        │
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Email Service │◀───│   Cron Worker    │───▶│   Database      │
│   (Mailhog)     │    │   Instance       │    │   (MySQL 8)     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### Componentes Principais

1. **API Layer**: Controllers e Middlewares
2. **Service Layer**: Lógica de negócio
3. **Repository Layer**: Acesso aos dados
4. **Queue System**: Processamento assíncrono
5. **Cron Worker**: Processamento de agendamentos
6. **Email Service**: Notificações

## 🏛️ Arquitetura em Camadas

### 1. Presentation Layer (Controllers)
```
app/Controller/
├── AccountController.php
├── WithdrawController.php
└── HealthController.php
```

**Responsabilidades:**
- Receber requisições HTTP
- Validar entrada
- Orquestrar serviços
- Retornar respostas padronizadas

### 2. Application Layer (Services)
```
app/Service/
├── WithdrawService.php
├── BalanceService.php
├── EmailService.php
└── SchedulerService.php
```

**Responsabilidades:**
- Lógica de negócio
- Orquestração de operações
- Validações complexas
- Coordenação entre repositórios

### 3. Domain Layer (Models & Entities)
```
app/Model/
├── Account.php
├── AccountWithdraw.php
└── AccountWithdrawPix.php

app/Entity/
├── WithdrawRequest.php
├── PixData.php
└── ScheduleData.php
```

**Responsabilidades:**
- Regras de domínio
- Entidades de negócio
- Value Objects
- Domain Events

### 4. Infrastructure Layer (Repositories & External)
```
app/Repository/
├── AccountRepository.php
├── WithdrawRepository.php
└── interfaces/

app/External/
├── EmailProvider.php
└── QueueProvider.php
```

**Responsabilidades:**
- Persistência de dados
- Integrações externas
- Infraestrutura técnica

## 🔄 Fluxos Principais

### Fluxo 1: Saque Imediato

```
1. [Cliente] POST /account/{id}/balance/withdraw
2. [Controller] Valida entrada
3. [WithdrawService] Verifica saldo disponível
4. [WithdrawService] Processa saque imediato
5. [Repository] Atualiza saldo e registra transação
6. [Queue] Envia job de email
7. [Controller] Retorna confirmação
8. [EmailWorker] Processa envio de email
```

### Fluxo 2: Saque Agendado

```
1. [Cliente] POST /account/{id}/balance/withdraw (com schedule)
2. [Controller] Valida entrada e data futura
3. [WithdrawService] Registra agendamento
4. [Repository] Salva com scheduled=true
5. [Controller] Retorna confirmação de agendamento
...
6. [Cron] Executa a cada minuto
7. [SchedulerService] Busca saques pendentes
8. [WithdrawService] Processa saque
9. [Queue] Envia job de email
```

### Fluxo 3: Processamento de Falha

```
1. [SchedulerService] Identifica saldo insuficiente
2. [Repository] Marca saque com erro
3. [Repository] Registra motivo da falha
4. [Logger] Log estruturado do erro
5. [Metrics] Incrementa contador de falhas
```

## 📊 Modelo de Dados

### Entidades Principais

```sql
-- Conta do usuário
account
├── id (UUID, PK)
├── name (VARCHAR)
├── balance (DECIMAL(15,2))
├── created_at (TIMESTAMP)
├── updated_at (TIMESTAMP)
└── version (INT) -- Para controle de concorrência

-- Transação de saque
account_withdraw
├── id (UUID, PK)
├── account_id (UUID, FK)
├── method (ENUM: 'PIX')
├── amount (DECIMAL(15,2))
├── scheduled (BOOLEAN)
├── scheduled_for (DATETIME NULL)
├── done (BOOLEAN)
├── error (BOOLEAN)
├── error_reason (VARCHAR NULL)
├── processed_at (TIMESTAMP NULL)
├── created_at (TIMESTAMP)
└── updated_at (TIMESTAMP)

-- Dados específicos do PIX
account_withdraw_pix
├── account_withdraw_id (UUID, PK/FK)
├── type (ENUM: 'email')
├── key (VARCHAR)
├── created_at (TIMESTAMP)
└── updated_at (TIMESTAMP)
```

### Índices Estratégicos

```sql
-- Para busca de saques agendados pendentes
CREATE INDEX idx_scheduled_pending 
ON account_withdraw (scheduled, done, scheduled_for);

-- Para consulta de histórico por conta
CREATE INDEX idx_account_withdraws 
ON account_withdraw (account_id, created_at);

-- Para controle de concorrência no saldo
CREATE INDEX idx_account_balance 
ON account (id, version);
```

## 🔧 Decisões Arquiteturais

### 1. **Pattern: Repository + Service Layer**
**Decisão**: Implementar Repository Pattern com Service Layer
**Motivação**: 
- Separação clara entre lógica de negócio e acesso a dados
- Facilita testes unitários
- Permite mudanças na camada de dados sem impactar negócio

### 2. **Queue System para Emails**
**Decisão**: Usar sistema de filas para envio de emails
**Motivação**:
- Performance: Não bloquear a resposta da API
- Resilência: Retry automático em caso de falha
- Escalabilidade: Processamento distribuído

### 3. **Hyperf Framework**
**Decisão**: Usar Hyperf 3 como framework base
**Motivação**:
- Performance superior (coroutines)
- Built-in support para queues
- Dependency Injection robusto
- Middleware system flexível

### 4. **UUID como Primary Key**
**Decisão**: Usar UUIDs em vez de auto-increment
**Motivação**:
- Escalabilidade horizontal (sem conflitos)
- Segurança (IDs não sequenciais)
- Integração com sistemas externos facilitada

### 5. **Versionamento Otimista**
**Decisão**: Implementar version field na tabela account
**Motivação**:
- Controle de concorrência sem locks
- Performance superior em alta concorrência
- Detecção de conflitos de saldo

## 🚀 Estratégias de Performance

### 1. **Database Optimization**
- Índices estratégicos para consultas frequentes
- Connection pooling configurado
- Read replicas para consultas (futuro)
- Query optimization com EXPLAIN

### 2. **Application Level**
- Hyperf coroutines para concorrência
- Cache de configurações
- Lazy loading de relacionamentos
- Batch processing para operações em massa

### 3. **Infrastructure**
- Docker multi-stage builds
- PHP OpCache habilitado
- MySQL query cache
- Load balancing preparado

## 📈 Observabilidade

### 1. **Logging Estruturado**
```json
{
  "timestamp": "2025-09-23T10:30:00Z",
  "level": "INFO",
  "service": "withdraw-api",
  "operation": "immediate_withdraw",
  "account_id": "uuid",
  "amount": 150.75,
  "duration_ms": 45,
  "status": "success"
}
```

### 2. **Métricas Chave**
- Tempo de resposta por endpoint
- Taxa de sucesso/erro de saques
- Volume de transações por período
- Utilização de filas
- Tempo de processamento de emails

### 3. **Health Checks**
- `/health` - Status geral da aplicação
- `/health/database` - Conectividade do banco
- `/health/queue` - Status das filas
- `/health/email` - Conectividade do email service

## 🔒 Segurança

### 1. **Validação de Entrada**
- Form Requests com validação robusta
- Sanitização de dados de entrada
- Rate limiting por IP/usuário
- CORS configurado adequadamente

### 2. **Proteção de Dados**
- Prepared statements (proteção SQL injection)
- Hashing de dados sensíveis quando necessário
- Logs sem informações sensíveis
- Encryption em trânsito (HTTPS)

### 3. **Controle de Acesso**
- Middleware de autenticação (futuro)
- Autorização por recurso
- Audit log de operações críticas

## 📦 Estrutura de Containers

### Docker Compose Services

```yaml
services:
  app:          # Hyperf Application
  nginx:        # Reverse Proxy
  mysql:        # Database
  mailhog:      # Email Testing
  redis:        # Queue & Cache (se necessário)
  cron:         # Scheduled Tasks
```

### Considerações para Produção

1. **Multi-stage builds** para otimização de imagem
2. **Health checks** em todos os containers
3. **Volume persistence** para dados críticos
4. **Network isolation** entre serviços
5. **Resource limits** configurados

## 🎯 Próximos Passos

1. **Implementar estrutura base** do projeto Hyperf
2. **Configurar Docker Compose** com todos os serviços
3. **Criar migrations** das tabelas
4. **Implementar models** e repositories
5. **Desenvolver services** de negócio
6. **Criar controllers** e rotas da API
7. **Implementar sistema de filas**
8. **Desenvolver cron worker**
9. **Configurar sistema de logs**
10. **Implementar testes**

---

## 📝 Considerações Futuras

### Melhorias Potenciais
- Implement CQRS para separar leituras/escritas
- Event Sourcing para auditoria completa
- GraphQL para flexibilidade de consultas
- Microserviços para maior escalabilidade
- Circuit Breaker para resiliência

### Monitoramento Avançado
- APM (Application Performance Monitoring)
- Distributed tracing
- Real-time dashboards
- Alerting automático

---

**Data de Criação**: 23/09/2025  
**Versão**: 1.0  
**Status**: Em Desenvolvimento
