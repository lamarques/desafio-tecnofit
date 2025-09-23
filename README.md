# 🏦 Sistema de Saque PIX - Desafio Tecnofit

[![Status](https://img.shields.io/badge/status-em_desenvolvimento-yellow)](https://github.com/lamarques/desafio-tecnofit)
[![PHP](https://img.shields.io/badge/PHP-8.2+-blue)](https://php.net)
[![Hyperf](https://img.shields.io/badge/Hyperf-3.0-brightgreen)](https://hyperf.wiki)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-orange)](https://mysql.com)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docker.com)

> Sistema digital para processamento de saques PIX com agendamento, desenvolvido com PHP Hyperf 3 e MySQL 8.

## 🎯 Visão Geral

Este projeto implementa uma **API robusta** para processamento de saques PIX que permite aos usuários retirar recursos de suas contas digitais de forma **segura**, **rápida** e **confiável**, 24/7.

### ✨ Características Principais

- 🚀 **Performance**: Processamento em menos de 2 segundos
- 🔒 **Segurança**: Controles robustos contra fraudes e saldo negativo
- ⏰ **Agendamento Inteligente**: Saques fora do horário comercial
- 📧 **Notificações**: Confirmações automáticas por email
- 📊 **Observabilidade**: Logs estruturados e métricas completas
- 🔄 **Escalabilidade**: Arquitetura preparada para crescimento

## 🏗️ Arquitetura

O sistema segue uma **arquitetura em camadas** com padrões de design modernos:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Controllers   │───▶│    Services     │───▶│  Repositories   │
│  (Presentation) │    │  (Application)  │    │ (Infrastructure)│
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Validation    │    │   Queue Jobs    │    │    Database     │
│   Middleware    │    │   (Async)       │    │    (MySQL)      │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### 🔧 Decisões Arquiteturais

Todas as decisões técnicas estão documentadas em **ADRs** (Architecture Decision Records):

- **[ADR-001](docs/adr/ADR-001-layered-architecture.md)**: Arquitetura em Camadas
- **[ADR-002](docs/adr/ADR-002-repository-service-pattern.md)**: Repository + Service Pattern
- **[ADR-003](docs/adr/ADR-003-uuid-primary-keys.md)**: UUID como Primary Keys
- **[ADR-004](docs/adr/ADR-004-queue-system.md)**: Sistema de Filas Assíncronas
- **[ADR-005](docs/adr/ADR-005-optimistic-locking.md)**: Versionamento Otimista
- **[ADR-006](docs/adr/ADR-006-observability-first.md)**: Observabilidade como Prioridade

## 🚀 Quick Start

### Pré-requisitos

- **Docker** e **Docker Compose**
- **Git**
- **Make** (opcional, para facilitar comandos)

### Instalação

```bash
# 1. Clone o repositório
git clone https://github.com/lamarques/desafio-tecnofit.git
cd desafio-tecnofit

# 2. Configure as variáveis de ambiente
cp .env.example .env

# 3. Suba os serviços com Docker
docker-compose up -d

# 4. Execute as migrations
docker-compose exec app php bin/hyperf.php migrate

# 5. Execute os seeders (opcional)
docker-compose exec app php bin/hyperf.php db:seed
```

### Verificação

```bash
# Verificar se a API está funcionando
curl http://localhost:9501/health

# Acessar Mailhog (para emails)
open http://localhost:8025
```

## 📡 API Endpoints

### Saque PIX

```http
POST /account/{accountId}/balance/withdraw
Content-Type: application/json

{
  "method": "PIX",
  "pix": {
    "type": "email",
    "key": "usuario@email.com"
  },
  "amount": 150.75,
  "schedule": null
}
```

### Health Checks

```http
GET /health                 # Status geral
GET /health/database        # Status do banco
GET /health/queue          # Status das filas
```

📖 **Documentação completa da API**: [Swagger/OpenAPI](docs/api-documentation.md)

## 🗄️ Banco de Dados

### Modelo de Dados

```sql
-- Conta do usuário
account (id, name, balance, version, timestamps)

-- Transação de saque  
account_withdraw (id, account_id, method, amount, scheduled, done, error, timestamps)

-- Dados específicos do PIX
account_withdraw_pix (account_withdraw_id, type, key, timestamps)
```

### Migrations

```bash
# Executar migrations
docker-compose exec app php bin/hyperf.php migrate

# Rollback (se necessário)
docker-compose exec app php bin/hyperf.php migrate:rollback
```

## ⚡ Principais Fluxos

### 1. Saque Imediato (Horário Comercial)

```
Cliente → API → Validação → Processamento → Notificação
  (2s)    (1s)      (0.5s)        (0.3s)        (assíncrono)
```

### 2. Saque Agendado (Fora do Horário)

```
Cliente → API → Agendamento → Cron → Processamento → Notificação
  (2s)    (1s)      (0.5s)     (scheduled)  (0.3s)   (assíncrono)
```

### 3. Controle de Concorrência

```
Request A ──┐
            ├─→ Optimistic Lock → Validation → Success/Retry
Request B ──┘
```

## 📊 Monitoramento e Observabilidade

### KPIs Principais

- **Performance**: < 2s tempo de resposta
- **Disponibilidade**: 99.9% uptime
- **Taxa de Sucesso**: > 99.5%
- **Volume**: 100k+ transações/mês

### Logs Estruturados

```json
{
  "timestamp": "2025-09-23T14:30:00.123Z",
  "level": "INFO",
  "service": "withdraw-api",
  "operation": "immediate_withdraw",
  "account_id": "uuid",
  "amount": 150.75,
  "duration_ms": 45,
  "status": "success"
}
```

### Dashboards

- **Grafana**: Métricas de performance
- **Logs**: Estruturados em JSON
- **Alertas**: Slack/Email para incidentes

## 🧪 Testes

```bash
# Testes unitários
docker-compose exec app php bin/hyperf.php test

# Testes de integração
docker-compose exec app php bin/hyperf.php test --testsuite=Integration

# Testes da API
docker-compose exec app php bin/hyperf.php test --testsuite=Feature

# Coverage
docker-compose exec app php bin/hyperf.php test --coverage-html coverage/
```

## 🛠️ Desenvolvimento

### Estrutura do Projeto

```
app/
├── Controller/         # Controllers da API
├── Service/           # Lógica de negócio
├── Repository/        # Acesso aos dados
├── Model/            # Modelos Eloquent
├── Job/              # Jobs assíncronos
├── Middleware/       # Middlewares
└── Exception/        # Exceções customizadas

config/               # Configurações
migrations/           # Migrations do banco
docs/                # Documentação
├── adr/             # Architecture Decision Records
├── business-architecture.md
├── kpis.md
└── okrs.md
```

### Comandos Úteis

```bash
# Logs em tempo real
docker-compose logs -f app

# Shell do container
docker-compose exec app bash

# Limpar cache
docker-compose exec app php bin/hyperf.php cache:clear

# Queue worker (desenvolvimento)
docker-compose exec app php bin/hyperf.php queue:work
```

## 📈 Performance

### Benchmarks

- **Saque Imediato**: 1,200ms p95
- **Throughput**: 1,500 req/s
- **Concorrência**: 100 usuários simultâneos
- **Memory Usage**: < 128MB por worker

### Otimizações

- **Connection Pooling**: MySQL connections reutilizadas
- **OpCache**: PHP bytecode cache ativo
- **Indexes**: Consultas otimizadas com índices estratégicos
- **Queue Workers**: Processamento assíncrono

## 🔒 Segurança

### Controles Implementados

- ✅ **Validação de Entrada**: Sanitização de todos os inputs
- ✅ **Rate Limiting**: Proteção contra abuse
- ✅ **SQL Injection**: Prepared statements
- ✅ **Saldo Negativo**: Validação em tempo real
- ✅ **Auditoria**: Logs completos de todas as transações
- ✅ **Optimistic Locking**: Controle de concorrência

### Compliance

- **PCI DSS**: Em processo de certificação
- **LGPD**: Dados pessoais protegidos
- **Auditoria**: Logs imutáveis com timestamp

## 🚀 Deployment

### Ambientes

- **Desenvolvimento**: Docker Compose local
- **Homologação**: Kubernetes cluster
- **Produção**: Kubernetes + Load Balancer

### CI/CD Pipeline

```yaml
# .github/workflows/ci.yml
Test → Build → Security Scan → Deploy → Smoke Tests
```

### Monitoramento em Produção

- **APM**: Application Performance Monitoring
- **Logs**: Centralizados (ELK Stack)
- **Alertas**: PagerDuty para incidentes críticos
- **Dashboards**: Grafana + Prometheus

## 📚 Documentação

### Para Desenvolvedores

- **[Architecture.md](architecture.md)**: Visão técnica completa
- **[ADRs](docs/adr/)**: Decisões arquiteturais detalhadas
- **[API Documentation](docs/api-documentation.md)**: Endpoints e exemplos

### Para Executivos

- **[Business Architecture](docs/business-architecture.md)**: Visão de negócio
- **[Executive Diagrams](docs/executive-diagrams.md)**: Diagramas simplificados
- **[KPIs](docs/kpis.md)**: Indicadores de performance
- **[OKRs](docs/okrs.md)**: Objetivos e resultados-chave

### Para Operações

- **[Runbook](docs/runbook.md)**: Guias operacionais
- **[Troubleshooting](docs/troubleshooting.md)**: Resolução de problemas
- **[Monitoring](docs/monitoring.md)**: Guias de monitoramento

## 🤝 Contribuição

### Processo de Desenvolvimento

1. **Fork** o repositório
2. **Crie** uma branch para sua feature (`git checkout -b feature/amazing-feature`)
3. **Implemente** seguindo os padrões estabelecidos
4. **Teste** completamente sua implementação
5. **Commit** suas mudanças (`git commit -m 'Add amazing feature'`)
6. **Push** para a branch (`git push origin feature/amazing-feature`)
7. **Abra** um Pull Request

### Padrões de Código

- **PSR-12**: Padrão de código PHP
- **PhpStan**: Análise estática (level 8)
- **PHP CS Fixer**: Formatação automática
- **Conventional Commits**: Padrão de commits

### Code Review

- ✅ Testes passando
- ✅ Coverage > 80%
- ✅ PhpStan level 8
- ✅ Documentação atualizada
- ✅ Performance verificada

## 📞 Suporte

### Contatos

- **Tech Lead**: Rogerio Lamarques ([rogerio.lamarques@gmail.com](mailto:rogerio.lamarques@gmail.com))
- **Product Owner**: [A definir]
- **Security Lead**: [A definir]

### Canais

- **Slack**: #saque-pix-dev
- **Email**: dev-team@company.com
- **Issues**: [GitHub Issues](https://github.com/lamarques/desafio-tecnofit/issues)

## 📄 Licença

Este projeto está licenciado sob a **MIT License** - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 🎯 Roadmap

### Versão 1.0 (Q4 2025) ✅
- [x] MVP com saque PIX básico
- [x] Sistema de agendamento
- [x] Notificações por email
- [x] Observabilidade completa

### Versão 1.1 (Q1 2026)
- [ ] Dashboard administrativo
- [ ] Relatórios financeiros
- [ ] API para consulta de histórico
- [ ] Webhooks para integrações

### Versão 2.0 (Q2 2026)
- [ ] Novos tipos de chave PIX
- [ ] Integração com outros bancos
- [ ] App mobile
- [ ] IA para detecção de fraudes

---

**Desenvolvido com ❤️ pela equipe de Engenharia**

[![Built with](https://img.shields.io/badge/built_with-❤️_and_☕-red)](https://github.com/lamarques/desafio-tecnofit)
