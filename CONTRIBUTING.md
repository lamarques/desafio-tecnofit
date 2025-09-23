# Contribuindo para o Sistema de Saque PIX

Obrigado por considerar contribuir para este projeto! Este documento contém diretrizes para contribuições.

## 🚀 Como Contribuir

### 1. Fork e Clone
```bash
# Fork o repositório no GitHub
# Depois clone seu fork
git clone https://github.com/SEU_USERNAME/desafio-tecnofit.git
cd desafio-tecnofit
```

### 2. Configure o Ambiente
```bash
# Configure o upstream
git remote add upstream https://github.com/lamarques/desafio-tecnofit.git

# Instale as dependências
docker-compose up -d
docker-compose exec app composer install
```

### 3. Crie uma Branch
```bash
# Sempre crie uma branch a partir da main
git checkout main
git pull upstream main
git checkout -b feature/minha-nova-feature
```

## 📝 Padrões de Desenvolvimento

### Commits
Usamos **Conventional Commits**:

```
feat: adiciona endpoint para consulta de histórico
fix: corrige validação de saldo negativo
docs: atualiza README com novas instruções
test: adiciona testes para WithdrawService
refactor: melhora performance do repository
```

### Código PHP
- **PSR-12**: Padrão de código
- **PhpStan Level 8**: Análise estática
- **PHP CS Fixer**: Formatação automática

```bash
# Verificar padrões
docker-compose exec app composer check-style
docker-compose exec app composer phpstan

# Corrigir formatação
docker-compose exec app composer fix-style
```

### Testes
- **Coverage mínimo**: 80%
- **Testes unitários**: Para services e repositories
- **Testes de integração**: Para endpoints da API

```bash
# Executar todos os testes
docker-compose exec app composer test

# Apenas testes unitários
docker-compose exec app composer test:unit

# Com coverage
docker-compose exec app composer test:coverage
```

## 🏗️ Arquitetura

### Estrutura de Camadas
```
Controllers (Presentation) → Services (Application) → Repositories (Infrastructure)
```

### Padrões Obrigatórios
- **Repository Pattern**: Para acesso aos dados
- **Service Layer**: Para lógica de negócio
- **Dependency Injection**: Via Hyperf container
- **Optimistic Locking**: Para controle de concorrência

### ADRs
Consulte os [ADRs](docs/adr/) antes de fazer mudanças arquiteturais significativas.

## 🧪 Processo de Review

### Checklist do PR
- [ ] Testes passando (CI verde)
- [ ] Coverage ≥ 80%
- [ ] PhpStan level 8 sem erros
- [ ] Documentação atualizada
- [ ] Performance verificada
- [ ] Segurança validada

### Template de PR
```markdown
## 🎯 Objetivo
Breve descrição da mudança

## 🔄 Mudanças
- [ ] Feature A
- [ ] Bug fix B
- [ ] Documentação C

## 🧪 Testes
- [ ] Testes unitários
- [ ] Testes de integração
- [ ] Testes manuais

## 📊 Performance
- Impacto na performance: Nenhum/Positivo/Negativo
- Benchmarks (se aplicável)

## 🔒 Segurança
- Mudanças relacionadas à segurança
- Validações implementadas
```

## 🐛 Reportando Bugs

### Template de Issue
1. **Descrição**: O que aconteceu?
2. **Reprodução**: Passos para reproduzir
3. **Esperado**: O que deveria acontecer?
4. **Ambiente**: Versão, OS, etc.
5. **Logs**: Logs relevantes (sem dados sensíveis)

### Severidade
- **🔴 Crítico**: Sistema fora do ar, perda de dados
- **🟡 Alto**: Funcionalidade quebrada
- **🔵 Médio**: Comportamento inesperado
- **🟢 Baixo**: Melhoria ou ajuste menor

## 💡 Sugerindo Features

### Processo
1. **Issue**: Crie uma issue descrevendo a feature
2. **Discussão**: Aguarde feedback da equipe
3. **Aprovação**: Aguarde aprovação antes de implementar
4. **Implementação**: Siga o processo de desenvolvimento

### Template
```markdown
## 🎯 Problema
Qual problema esta feature resolve?

## 💡 Solução Proposta
Como você sugere resolver?

## 🔄 Alternativas
Outras opções consideradas

## 📊 Impacto
- Usuários afetados
- Complexidade técnica
- Riscos envolvidos
```

## 📚 Recursos

### Documentação
- [Architecture.md](architecture.md): Visão técnica
- [ADRs](docs/adr/): Decisões arquiteturais
- [KPIs](docs/kpis.md): Métricas importantes

### Ferramentas
- **Hyperf**: Framework PHP
- **MySQL**: Banco de dados
- **Docker**: Containerização
- **PhpStan**: Análise estática
- **PHPUnit**: Testes

### Contatos
- **Slack**: #saque-pix-dev
- **Email**: dev-team@company.com
- **Issues**: GitHub Issues

## 🎉 Reconhecimento

Contribuidores são reconhecidos de várias formas:
- Nome no README
- Badge de contribuidor
- Menção em releases
- Feedback público

## ❓ Dúvidas

Não hesite em:
- Abrir uma issue para discussão
- Perguntar no Slack
- Entrar em contato diretamente

**Obrigado por contribuir! 🚀**
