# Product Backlog - Sistema de Saque PIX

## 🎯 Visão do Produto

Como **Product Owner**, o objetivo é entregar um sistema de saque PIX que proporcione **valor imediato** aos usuários, com **qualidade técnica** e **escalabilidade** para o futuro.

---

## 📋 Backlog Priorizado

### 🚀 Epic 1: Fundação Técnica (Sprint 1-2)

#### **TASK-001: Setup do Ambiente Docker**
- **Tipo**: Técnica / Infraestrutura
- **Valor de Negócio**: Habilitar desenvolvimento e deployment
- **Story Points**: 5
- **Prioridade**: CRÍTICA
- **Entrega (PR)**: Docker Compose completo funcionando

**Acceptance Criteria:**
- [ ] Docker Compose com PHP Hyperf 3
- [ ] MySQL 8 configurado
- [ ] Mailhog para testes de email
- [ ] Redis para cache e filas
- [ ] Nginx como reverse proxy
- [ ] Health checks em todos os containers
- [ ] Documentação de setup no README

**Definition of Done:**
- [ ] `docker-compose up -d` funciona sem erros
- [ ] Todos os serviços acessíveis (health checks verdes)
- [ ] README atualizado com instruções
- [ ] Testado do zero em máquina limpa

---

#### **TASK-002: Estrutura Base do Projeto Hyperf**
- **Tipo**: Técnica / Fundação
- **Valor de Negócio**: Base para desenvolvimento de features
- **Story Points**: 8
- **Prioridade**: CRÍTICA
- **Entrega (PR)**: Projeto Hyperf estruturado

**Acceptance Criteria:**
- [ ] Projeto Hyperf 3 instalado e configurado
- [ ] Estrutura de pastas DDD implementada
- [ ] Dependency Injection configurado
- [ ] Middleware básico (CORS, logging)
- [ ] Health check endpoint funcionando
- [ ] Configurações de ambiente (.env)

**Definition of Done:**
- [ ] Aplicação roda em http://localhost:9501
- [ ] GET /health retorna 200
- [ ] Logs estruturados funcionando
- [ ] Testes básicos passando

---

#### **TASK-003: Database Setup e Migrations**
- **Tipo**: Técnica / Dados
- **Valor de Negócio**: Persistência confiável de dados financeiros
- **Story Points**: 5
- **Prioridade**: ALTA
- **Entrega (PR)**: Schema do banco implementado

**Acceptance Criteria:**
- [ ] Migration para tabela `account`
- [ ] Migration para tabela `account_withdraw`  
- [ ] Migration para tabela `account_withdraw_pix`
- [ ] Índices estratégicos criados
- [ ] Seeders para dados de teste
- [ ] Validação de constraints

**Definition of Done:**
- [ ] `php bin/hyperf.php migrate` executa sem erros
- [ ] Seeders populam dados de teste
- [ ] Constraints de integridade funcionando
- [ ] Rollback das migrations funciona

---

### 💼 Epic 2: Core Business - Saque Imediato (Sprint 2-3)

#### **TASK-004: Modelagem de Domínio (Models e Entities)**
- **Tipo**: Negócio / Domínio
- **Valor de Negócio**: Representar corretamente regras de negócio
- **Story Points**: 8
- **Prioridade**: ALTA
- **Entrega (PR)**: Models e Value Objects implementados

**User Story**: *Como sistema, preciso representar contas e saques de forma consistente*

**Acceptance Criteria:**
- [ ] Model `Account` com validações
- [ ] Model `AccountWithdraw` com estados
- [ ] Model `AccountWithdrawPix` para dados PIX
- [ ] Value Objects (Money, PixKey, etc.)
- [ ] Factory patterns implementados
- [ ] Relacionamentos Eloquent configurados

**Definition of Done:**
- [ ] Models cobertos por testes unitários
- [ ] Validações funcionando (saldo, PIX key)
- [ ] Factory para criação de test data
- [ ] PHPDoc completo

---

#### **TASK-005: Repository Layer (Acesso aos Dados)**
- **Tipo**: Técnica / Arquitetura
- **Valor de Negócio**: Abstração consistente de dados
- **Story Points**: 5
- **Prioridade**: ALTA
- **Entrega (PR)**: Repositories com interfaces

**Acceptance Criteria:**
- [ ] Interface `AccountRepositoryInterface`
- [ ] Interface `WithdrawRepositoryInterface`
- [ ] Implementação `AccountRepository`
- [ ] Implementação `WithdrawRepository`
- [ ] Injection configurado no container
- [ ] Queries otimizadas

**Definition of Done:**
- [ ] Todos os repositories testados
- [ ] Interfaces respeitadas
- [ ] Performance validada (< 100ms)
- [ ] Dependency Injection funcionando

---

#### **TASK-006: Service Layer - Lógica de Negócio**
- **Tipo**: Negócio / Core
- **Valor de Negócio**: Implementar regras críticas de saque
- **Story Points**: 13
- **Prioridade**: CRÍTICA
- **Entrega (PR)**: Services de negócio funcionais

**User Story**: *Como cliente, quero sacar dinheiro via PIX respeitando meu saldo disponível*

**Acceptance Criteria:**
- [ ] `WithdrawService` com lógica de saque
- [ ] `BalanceService` para validações de saldo
- [ ] Validação de saldo suficiente
- [ ] Controle de concorrência (optimistic locking)
- [ ] Tratamento de erros de negócio
- [ ] Logging estruturado

**Definition of Done:**
- [ ] Todos os cenários de negócio cobertos
- [ ] Testes de concorrência passando
- [ ] Error handling completo
- [ ] Performance < 2 segundos

---

#### **TASK-007: API Endpoint - POST /account/{id}/balance/withdraw**
- **Tipo**: Negócio / Interface
- **Valor de Negócio**: Permitir que usuários façam saques
- **Story Points**: 8
- **Prioridade**: CRÍTICA
- **Entrega (PR)**: Endpoint de saque funcionando

**User Story**: *Como cliente, quero fazer um saque PIX imediato via API*

**Acceptance Criteria:**
- [ ] Controller `WithdrawController`
- [ ] Validação de entrada (FormRequest)
- [ ] Integração com `WithdrawService`
- [ ] Responses padronizadas (JSON)
- [ ] Rate limiting implementado
- [ ] Documentação OpenAPI

**Definition of Done:**
- [ ] Endpoint retorna 201 para sucesso
- [ ] Validações retornam 400 com detalhes
- [ ] Testes de integração completos
- [ ] Postman collection atualizada

---

### 📧 Epic 3: Notificações e UX (Sprint 3)

#### **TASK-008: Sistema de Email (Mailhog Integration)**
- **Tipo**: Negócio / Comunicação
- **Valor de Negócio**: Transparência para o cliente
- **Story Points**: 5
- **Prioridade**: ALTA
- **Entrega (PR)**: Emails sendo enviados

**User Story**: *Como cliente, quero receber confirmação por email do meu saque*

**Acceptance Criteria:**
- [ ] Template de email responsivo
- [ ] Integração com Mailhog
- [ ] Service `EmailService`
- [ ] Dados do saque no email (valor, PIX, data)
- [ ] Tratamento de falhas de envio

**Definition of Done:**
- [ ] Email aparece no Mailhog
- [ ] Template profissional e claro
- [ ] Dados corretos no email
- [ ] Fallback para falhas implementado

---

#### **TASK-009: Sistema de Filas (Async Processing)**
- **Tipo**: Técnica / Performance
- **Valor de Negócio**: Resposta rápida para o usuário
- **Story Points**: 8
- **Prioridade**: MÉDIA
- **Entrega (PR)**: Processing assíncrono funcionando

**Acceptance Criteria:**
- [ ] Job `SendWithdrawEmailJob`
- [ ] Configuração do Redis queue
- [ ] Worker process configurado
- [ ] Retry logic implementado
- [ ] Monitoramento de filas

**Definition of Done:**
- [ ] Emails enviados em < 30 segundos
- [ ] Worker processa jobs sem erros
- [ ] Retry funciona para falhas
- [ ] Dashboard de monitoramento

---

### ⏰ Epic 4: Agendamento (Sprint 4)

#### **TASK-010: Lógica de Agendamento**
- **Tipo**: Negócio / Feature
- **Valor de Negócio**: Conveniência 24/7 para clientes
- **Story Points**: 8
- **Prioridade**: ALTA
- **Entrega (PR)**: Agendamento funcionando na API

**User Story**: *Como cliente, quero agendar saques para horário comercial*

**Acceptance Criteria:**
- [ ] Validação de horário comercial
- [ ] Agendamento para até 7 dias
- [ ] Persistência do agendamento
- [ ] Status de saques agendados
- [ ] Validação de data futura

**Definition of Done:**
- [ ] API aceita campo `schedule`
- [ ] Validações impedem agendamentos inválidos
- [ ] Status persiste corretamente
- [ ] Testes de timezone funcionando

---

#### **TASK-011: Processador de Saques Agendados (Cron)**
- **Tipo**: Técnica / Automação
- **Valor de Negócio**: Processamento automático confiável
- **Story Points**: 8
- **Prioridade**: ALTA
- **Entrega (PR)**: Command para processar agendamentos

**Acceptance Criteria:**
- [ ] Command `ProcessScheduledWithdrawsCommand`
- [ ] Busca saques pendentes por horário
- [ ] Processa saques com validação de saldo
- [ ] Atualiza status (success/error)
- [ ] Logging detalhado

**Definition of Done:**
- [ ] Command executa sem travamentos
- [ ] Saques processados corretamente
- [ ] Falhas tratadas e logadas
- [ ] Performance adequada (< 5min para 1000 saques)

---

### 📊 Epic 5: Observabilidade e Qualidade (Sprint 4-5)

#### **TASK-012: Logging Estruturado e Métricas**
- **Tipo**: Técnica / Operações
- **Valor de Negócio**: Confiabilidade e debugging
- **Story Points**: 5
- **Prioridade**: MÉDIA
- **Entrega (PR)**: Sistema de logs profissional

**Acceptance Criteria:**
- [ ] Logs JSON estruturados
- [ ] Correlation IDs implementados
- [ ] Métricas de performance
- [ ] Log rotation configurado
- [ ] Dashboards básicos

**Definition of Done:**
- [ ] Logs legíveis e estruturados
- [ ] Performance trackeada
- [ ] Correlation IDs em todas as requests
- [ ] Métricas exportadas

---

#### **TASK-013: Testes Automatizados (Unit + Integration)**
- **Tipo**: Técnica / Qualidade
- **Valor de Negócio**: Confiabilidade do sistema
- **Story Points**: 13
- **Prioridade**: ALTA
- **Entrega (PR)**: Cobertura de testes > 80%

**Acceptance Criteria:**
- [ ] Testes unitários para Services
- [ ] Testes de integração para API
- [ ] Testes de concorrência
- [ ] Factory para test data
- [ ] Coverage report gerado

**Definition of Done:**
- [ ] Coverage > 80%
- [ ] Todos os testes passando
- [ ] CI pipeline configurado
- [ ] Performance tests incluídos

---

### 🔒 Epic 6: Segurança e Produção (Sprint 5)

#### **TASK-014: Validações de Segurança**
- **Tipo**: Técnica / Segurança
- **Valor de Negócio**: Proteção contra fraudes
- **Story Points**: 5
- **Prioridade**: CRÍTICA
- **Entrega (PR)**: Controles de segurança implementados

**Acceptance Criteria:**
- [ ] Rate limiting por IP/usuário
- [ ] Validação robusta de inputs
- [ ] Sanitização de dados
- [ ] Audit logs de transações
- [ ] Proteção contra SQL injection

**Definition of Done:**
- [ ] Penetration tests básicos passando
- [ ] Rate limiting testado
- [ ] Audit logs funcionando
- [ ] Validações resistentes a bypass

---

#### **TASK-015: CI/CD Pipeline (GitHub Actions)**
- **Tipo**: Técnica / Operações  
- **Valor de Negócio**: Deploy confiável e rápido
- **Story Points**: 8
- **Prioridade**: MÉDIA
- **Entrega (PR)**: Pipeline automatizado

**Acceptance Criteria:**
- [ ] Workflow de CI configurado
- [ ] Testes automáticos no PR
- [ ] Build e push de imagens
- [ ] Deploy automatizado (staging)
- [ ] Rollback automático em falhas

**Definition of Done:**
- [ ] CI/CD executando em cada PR
- [ ] Deploy em staging automático
- [ ] Rollback testado e funcionando
- [ ] Notificações de falhas configuradas

---

## 📈 Métricas de Entrega

### Velocity Esperada
- **Sprint 1-2**: 18 story points (setup + base)
- **Sprint 3**: 21 story points (core features)  
- **Sprint 4**: 16 story points (agendamento)
- **Sprint 5**: 18 story points (qualidade + produção)

### Definition of Ready (DoR)
- [ ] User story clara com critérios de aceite
- [ ] Story points estimados pelo time
- [ ] Dependências identificadas
- [ ] Mockups/specs disponíveis (se necessário)

### Definition of Done (DoD)
- [ ] Code review aprovado
- [ ] Testes passando (CI verde)
- [ ] Documentação atualizada
- [ ] Performance validada
- [ ] Security checklist completado

## 🎯 Roadmap de Valor

### Semana 1-2: **Fundação** 
- Valor: Habilitar desenvolvimento
- Entrega: Ambiente funcionando

### Semana 3-4: **MVP**
- Valor: Saque PIX básico funcionando
- Entrega: Feature core completa

### Semana 5-6: **Enhancement**
- Valor: Experiência completa do usuário
- Entrega: Notificações + Agendamento

### Semana 7-8: **Production Ready**
- Valor: Sistema confiável em produção
- Entrega: Qualidade + Security + CI/CD

---

## 🏆 Success Metrics

### Por Epic
- **Epic 1**: Docker funcionando em < 5min setup
- **Epic 2**: API processa saque em < 2s
- **Epic 3**: Email entregue em < 30s  
- **Epic 4**: 100% saques agendados processados
- **Epic 5**: 0 bugs críticos em produção
- **Epic 6**: Deploy em < 10min, 0 downtime

### Overall
- **Time to Market**: 8 semanas
- **Quality**: 0 bugs críticos
- **Performance**: < 2s response time
- **Reliability**: 99.9% uptime

---

**Product Owner**: Rogerio Lamarques  
**Data**: 23/09/2025  
**Versão**: 1.0  
**Status**: Ready for Development
