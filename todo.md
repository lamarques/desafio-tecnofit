# TODO - Desafio Técnico Tecnofit: Saque PIX

## 🎯 Objetivo
Desenvolver uma API para processamento de saques PIX com agendamento, utilizando PHP Hyperf 3 e MySQL 8.

## 📋 Checklist de Desenvolvimento

### 1. **Análise e Planejamento**
- [x] **Desenho da Arquitetura** ✅
  - [x] Criar diagrama de arquitetura da aplicação
  - [x] Definir estrutura de microsserviços/componentes
  - [x] Mapear fluxo de dados entre componentes
  - [x] Documentar padrões arquiteturais escolhidos
  
- [ ] **Modelagem do Banco de Dados**
  - [x] Refinar estrutura das tabelas baseado nas regras de negócio
  - [ ] Criar diagrama ER visual (Entity-Relationship)
  - [x] Definir índices e relacionamentos
  - [x] Validar constraints e regras de integridade

### 2. **Setup e Estrutura do Projeto**
- [ ] **Configuração Docker**
  - [ ] Docker Compose com todos os serviços
  - [ ] Container PHP Hyperf 3
  - [ ] Container MySQL 8
  - [ ] Container Mailhog para testes de email
  - [ ] Configuração de rede entre containers
  
- [ ] **Estrutura Base do Projeto**
  - [ ] Estrutura de pastas seguindo padrões Hyperf + DDD
  - [ ] Configurações de ambiente (.env)
  - [ ] Migrations do banco de dados
  - [ ] Seeders para dados de teste

### 3. **Desenvolvimento Core**
- [ ] **Modelos e Entidades**
  - [ ] Model Account
  - [ ] Model AccountWithdraw
  - [ ] Model AccountWithdrawPix
  - [ ] Factories para criação de objetos
  
- [ ] **Camada de Dados (Repository Pattern)**
  - [ ] AccountRepository
  - [ ] AccountWithdrawRepository
  - [ ] Interface contracts
  
- [ ] **Serviços de Negócio**
  - [ ] WithdrawService (serviço principal)
  - [ ] ImmediateWithdrawService
  - [ ] ScheduledWithdrawService
  - [ ] BalanceValidationService
  - [ ] SchedulingValidationService

### 4. **API Endpoints**
- [ ] **POST /account/{accountId}/balance/withdraw**
  - [ ] Controller implementation
  - [ ] Request validation (Form Request)
  - [ ] Response formatting
  - [ ] Error handling
  - [ ] Middleware de validação
  
- [ ] **Endpoints auxiliares (se necessário)**
  - [ ] GET /account/{accountId}/balance (consulta saldo)
  - [ ] GET /account/{accountId}/withdraws (histórico)

### 5. **Sistema de Notificações**
- [ ] **Email Service**
  - [ ] Template engine para emails
  - [ ] Service de envio de emails
  - [ ] Integração com Mailhog
  - [ ] Queue para envios assíncronos
  - [ ] Templates responsivos

### 6. **Sistema de Agendamento**
- [ ] **Cron Job / Command**
  - [ ] Command para processar saques agendados
  - [ ] Lógica de verificação de horário de funcionamento
  - [ ] Tratamento de saldo insuficiente
  - [ ] Logs detalhados de execução
  - [ ] Configuração de schedule

### 7. **Aspectos Técnicos Avançados**
- [ ] **Performance**
  - [ ] Otimizações de queries SQL
  - [ ] Sistema de cache (Redis se necessário)
  - [ ] Database connection pooling
  - [ ] Índices otimizados
  
- [ ] **Observabilidade**
  - [ ] Sistema de logs estruturados
  - [ ] Métricas de performance
  - [ ] Health checks
  - [ ] Monitoring de erros
  
- [ ] **Segurança**
  - [ ] Validações robustas de entrada
  - [ ] Rate limiting
  - [ ] Sanitização de dados
  - [ ] Proteção contra SQL injection
  - [ ] HTTPS/TLS configuration
  
- [ ] **Escalabilidade**
  - [ ] Design stateless
  - [ ] Queue system para processamento assíncrono
  - [ ] Horizontal scaling preparation
  - [ ] Load balancing considerations

### 8. **Qualidade e Testes**
- [ ] **Testes Unitários**
  - [ ] Services
  - [ ] Repositories
  - [ ] Models
  - [ ] Utilities
  
- [ ] **Testes de Integração**
  - [ ] Database interactions
  - [ ] Email sending
  - [ ] Queue processing
  
- [ ] **Testes de API**
  - [ ] Endpoints testing
  - [ ] Error scenarios
  - [ ] Performance testing

### 9. **Documentação e Entrega**
- [ ] **README.md detalhado**
  - [ ] Instruções de setup e instalação
  - [ ] Decisões arquiteturais documentadas
  - [ ] Justificativas técnicas
  - [ ] Como rodar o projeto
  - [ ] Troubleshooting guide
  
- [ ] **Documentação da API**
  - [ ] OpenAPI/Swagger documentation
  - [ ] Exemplos de requests/responses
  - [ ] Error codes documentation
  
- [ ] **Documentação de Processo**
  - [ ] Arquivo de conversas com IA (talks.md)
  - [ ] Decisões tomadas e motivações
  - [ ] Lições aprendidas

## 🎯 Prioridade de Execução

### Fase 1 - Foundation (Alta Prioridade)
1. Análise e Planejamento
2. Setup e Estrutura do Projeto
3. Desenvolvimento Core

### Fase 2 - Implementation (Alta Prioridade)
4. API Endpoints
5. Sistema de Notificações
6. Sistema de Agendamento

### Fase 3 - Enhancement (Média Prioridade)
7. Aspectos Técnicos Avançados
8. Qualidade e Testes

### Fase 4 - Documentation (Alta Prioridade)
9. Documentação e Entrega

## 📝 Notas Importantes

- **Foco na Qualidade**: Cada implementação deve ser bem pensada e documentada
- **Decisões Registradas**: Todas as escolhas técnicas devem ser justificadas
- **Processo Transparente**: O caminho de desenvolvimento deve ser rastreável
- **Código Limpo**: Seguir boas práticas de Clean Code e SOLID
- **Performance**: Considerar performance desde o início, não deixar para depois

---
**Última Atualização**: 23/09/2025
**Status**: Iniciando Desenvolvimento
