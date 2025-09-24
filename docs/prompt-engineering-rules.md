# Prompt Engineering Rules - Sistema de Saque PIX

## 🎯 Diretrizes Gerais para IA durante Execução de Tasks

### Visão Geral
Este documento estabelece as **regras de comportamento** que a IA deve seguir rigorosamente durante a execução de cada task do projeto, garantindo **consistência**, **qualidade** e **alinhamento** com os objetivos de negócio e técnicos.

---

## 🏗️ Regras Arquiteturais Fundamentais

### R1: Aderência aos ADRs
```
SEMPRE consultar e seguir as decisões dos ADRs:
- ADR-001: Arquitetura em 4 camadas obrigatória
- ADR-002: Repository + Service Pattern sempre
- ADR-003: UUID como primary keys em TODAS as tabelas
- ADR-004: Queue system para operações assíncronas
- ADR-005: Optimistic locking para controle de concorrência
- ADR-006: Observabilidade em TODAS as implementações
```

### R2: Estrutura de Camadas Rígida
```
NUNCA misturar responsabilidades entre camadas:
1. Presentation (Controllers): Apenas validação e orquestração
2. Application (Services): Apenas lógica de negócio  
3. Domain (Models): Apenas regras de domínio
4. Infrastructure (Repositories): Apenas acesso aos dados

PROIBIDO: Controller chamar Repository diretamente
OBRIGATÓRIO: Controller → Service → Repository
```

### R3: Padrões de Nomenclatura
```
Classes:
- Controllers: {Entity}Controller (ex: WithdrawController)
- Services: {Entity}Service (ex: WithdrawService) 
- Repositories: {Entity}Repository (ex: AccountRepository)
- Models: {Entity} (ex: Account)
- Jobs: {Action}{Entity}Job (ex: SendWithdrawEmailJob)

Métodos:
- Controllers: verbo + substantivo (ex: withdraw, getBalance)
- Services: verbo descritivo (ex: processImmediate, validateBalance)
- Repositories: CRUD explícito (ex: findById, create, updateBalance)
```

---

## 💼 Regras de Negócio Críticas

### R4: Validações Financeiras Obrigatórias
```
SEMPRE implementar em TODA transação:
1. Validação de saldo suficiente ANTES de qualquer operação
2. Controle de concorrência com optimistic locking
3. Prevenção de saldo negativo (hard constraint)
4. Validação de PIX key format
5. Validação de valor mínimo/máximo

NUNCA permitir:
- Saldo negativo em qualquer cenário
- Transações sem validação prévia
- Operações sem controle de concorrência
```

### R5: Tratamento de Erros Padronizado
```
SEMPRE implementar hierarquia de exceções:
- BusinessException: Para erros de regra de negócio
- ValidationException: Para erros de validação
- InsufficientBalanceException: Para saldo insuficiente
- ConcurrencyException: Para conflitos de versioning

FORMATO de response de erro:
{
  "error": {
    "code": "INSUFFICIENT_BALANCE",
    "message": "Saldo insuficiente para realizar o saque",
    "details": { "requested": 100.50, "available": 50.25 }
  }
}
```

### R6: Logging Estruturado Obrigatório
```
SEMPRE logar em JSON estruturado:
{
  "timestamp": "ISO 8601",
  "level": "INFO|ERROR|WARNING", 
  "service": "withdraw-api",
  "operation": "immediate_withdraw",
  "account_id": "uuid",
  "correlation_id": "req_xxxx",
  "duration_ms": 123,
  "status": "success|error",
  "context": { dados específicos }
}

OBRIGATÓRIO logar:
- Início de toda operação crítica
- Sucesso com métricas de performance  
- Erros com contexto completo
- NUNCA logar dados sensíveis (senhas, tokens)
```

---

## 🔧 Regras de Implementação Técnica

### R7: Dependency Injection Sempre
```
SEMPRE usar DI container do Hyperf:
- NUNCA usar new ClassName() diretamente
- SEMPRE injetar interfaces, não implementações
- OBRIGATÓRIO registrar no container

Exemplo CORRETO:
public function __construct(
    private AccountRepositoryInterface $accountRepo,
    private WithdrawServiceInterface $withdrawService
) {}

Exemplo INCORRETO:
$accountRepo = new AccountRepository();
```

### R8: Validações de Entrada Rigorosas
```
SEMPRE usar FormRequest para validação:
- Validação de tipos
- Validação de formatos (email, UUID, decimal)
- Validação de ranges (min/max valores)
- Sanitização de dados de entrada

NUNCA confiar em dados de entrada sem validação prévia
SEMPRE retornar erros 400 com detalhes específicos
```

### R9: Performance Requirements
```
SEMPRE otimizar para performance:
- Queries de banco em < 100ms
- APIs respondendo em < 2 segundos
- Usar índices apropriados
- Evitar N+1 queries
- Lazy loading quando possível

MONITORAR sempre:
- Tempo de execução
- Memory usage  
- Query count
- Cache hit ratio (quando aplicável)
```

---

## 🧪 Regras de Qualidade e Testes

### R10: Cobertura de Testes Obrigatória
```
SEMPRE implementar testes para:
- Unit tests para TODOS os Services (100% coverage)
- Integration tests para TODOS os Controllers
- Repository tests com banco de dados real
- Testes de concorrência para operações críticas

Estrutura de teste padrão:
- Arrange: Setup do contexto
- Act: Execução da operação
- Assert: Verificação do resultado
- Cleanup: Limpeza de dados de teste
```

### R11: Code Quality Standards
```
SEMPRE seguir:
- PSR-12 para formatação
- PhpStan level 8 sem erros
- Conventional commits
- PHPDoc completo em métodos públicos

VERIFICAR antes de commit:
composer check-style
composer phpstan
composer test
```

### R12: Documentação Sincronizada
```
SEMPRE atualizar documentação quando:
- Criar nova API endpoint
- Modificar contratos existentes  
- Adicionar novas regras de negócio
- Implementar novas funcionalidades

Atualizar simultaneamente:
- README.md (se impactar setup)
- OpenAPI spec (para APIs)
- ADRs (para decisões arquiteturais)
- Comentários no código
```

---

## 🔐 Regras de Segurança

### R13: Segurança por Design
```
SEMPRE implementar:
- Rate limiting em endpoints públicos
- Validação e sanitização de TODOS os inputs
- Prepared statements (NUNCA raw SQL)
- Audit logs para operações financeiras
- Timeout em operações de rede

NUNCA expor:
- Stack traces em produção
- Informações de sistema em errors
- Dados internos em logs públicos
```

### R14: Auditoria Completa
```
SEMPRE registrar para auditoria:
- Quem fez a operação (se aplicável)
- Quando foi feita (timestamp preciso)
- O que foi alterado (before/after)
- Resultado da operação (sucesso/falha)
- Context adicional (IP, user-agent)

FORMATO de audit log:
{
  "event_type": "withdraw_processed",
  "account_id": "uuid", 
  "amount": 100.50,
  "pix_key": "user@example.com",
  "timestamp": "2025-09-24T10:30:00.123Z",
  "status": "success",
  "metadata": { context adicional }
}
```

---

## 🚀 Regras de Desenvolvimento Ágil

### R15: Task-Driven Development
```
🚫 REGRA FUNDAMENTAL: EXECUÇÃO DE UMA TASK POR VEZ
- PROIBIDO executar múltiplas tasks simultaneamente
- OBRIGATÓRIO finalizar completamente a task atual antes de iniciar próxima
- PROIBIDO misturar implementações de diferentes tasks

Para CADA task:
1. CRIAR branch específica da task a partir da main
2. LER completamente os acceptance criteria
3. PLANEJAR implementação seguindo arquitetura
4. IMPLEMENTAR incrementalmente com testes
5. VALIDAR contra Definition of Done
6. ABRIR Pull Request para main
7. FINALIZAR completamente antes de próxima task

NUNCA implementar além do escopo da task atual
NUNCA misturar código de tasks diferentes
SEMPRE validar com PO se houver dúvidas sobre escopo
SEMPRE completar 100% da task antes de iniciar outra
```

### R16: Git Flow e Branching Strategy
```
🌿 MODELO DE BRANCHING OBRIGATÓRIO:

1. BRANCH CREATION:
   - git checkout main
   - git pull origin main
   - git checkout -b feature/TASK-XXX-description
   
2. NOMENCLATURA DE BRANCH:
   - feature/TASK-001-docker-setup
   - feature/TASK-002-hyperf-structure
   - feature/TASK-003-mysql-config
   
3. COMMITS DURANTE DESENVOLVIMENTO:
   - Commits pequenos e atômicos
   - Mensagens seguindo: "feat(TASK-XXX): description"
   - Sempre com task ID na mensagem
   
4. FINALIZAÇÃO DA TASK:
   - git push origin feature/TASK-XXX-description
   - Abrir Pull Request para main
   - PR title: "TASK-XXX: Description"
   - PR template com evidências
   
5. MERGE E CLEANUP:
   - Após merge, deletar branch feature
   - git checkout main && git pull origin main
   - Pronto para próxima task

PROIBIDO:
- Trabalhar diretamente na main
- Misturar tasks na mesma branch
- Branch sem task ID
- Merge sem Pull Request
```

### R17: Pull Request Standards
```
SEMPRE criar PR com:
- Título seguindo padrão: "feat(TASK-XXX): descrição breve"
- Descrição detalhada com context
- Checklist de Definition of Done
- Screenshots/evidências (se aplicável)
- Performance benchmarks (se relevante)

Template obrigatório:
## 🎯 Task
Link para task no backlog

## 🔄 Implementação  
- [ ] Acceptance criteria 1
- [ ] Acceptance criteria 2

## 🧪 Testes
- [ ] Unit tests
- [ ] Integration tests  
- [ ] Performance tests

## 📊 Métricas
- Response time: Xms
- Memory usage: XMB
- Test coverage: X%
```

---

## 🎯 Regras de Contexto por Epic

### Epic 1 - Fundação: Foco em Robustez
```
PRIORIZAR:
- Configuração correta sobre rapidez
- Documentação detalhada de setup
- Troubleshooting guides
- Health checks em tudo

EVITAR:
- Configurações hardcoded
- Dependências desnecessárias
- Complexidade prematura
```

### Epic 2 - Core Business: Foco em Correção
```
PRIORIZAR:
- Regras de negócio precisas
- Validações rigorosas
- Error handling completo
- Performance otimizada

EVITAR:
- Shortcuts de validação
- Lógica de negócio em controllers
- Operações sem logging
```

### Epic 3-6: Foco em Qualidade
```
PRIORIZAR:
- User experience otimizada
- Observabilidade completa
- Segurança por design
- Produção readiness

EVITAR:
- Features sem monitoramento
- Operações sem retry logic
- Deploy sem rollback strategy
```

---

## 🔍 Checklist de Verificação por Task

### Antes de Começar
- [ ] Li e entendi todos os acceptance criteria
- [ ] Consultei ADRs relevantes
- [ ] Identifiquei dependências
- [ ] Tenho ambiente configurado

### Durante Desenvolvimento  
- [ ] Seguindo estrutura de camadas
- [ ] Implementando testes simultaneamente
- [ ] Logando operações importantes
- [ ] Validando performance continuamente

### Antes do Commit
- [ ] Todos os testes passando
- [ ] Code quality checks OK
- [ ] Documentação atualizada
- [ ] Performance validada

### Antes do PR
- [ ] Definition of Done completa
- [ ] PR template preenchido
- [ ] Evidências incluídas
- [ ] Ready for review

---

## 🎨 Personas da IA por Contexto

### Como Developer AI
```
- Foco técnico e arquitetural
- Preocupação com performance e qualidade
- Implementação seguindo padrões estabelecidos
- Questionamento de decisões técnicas quando necessário
```

### Como QA AI
```
- Foco em edge cases e validações
- Preocupação com segurança e robustez
- Testes abrangentes e cenários de falha
- Documentação de bugs e melhorias
```

### Como DevOps AI
```
- Foco em operabilidade e monitoramento
- Preocupação com deploy e observabilidade
- Automação e confiabilidade
- Métricas e alertas
```

---

## 📋 Meta-Regras para a IA

### M1: Transparência Total
```
SEMPRE explicar:
- Por que uma decisão foi tomada
- Que alternativas foram consideradas
- Que riscos foram identificados
- Como a solução atende os requisitos
```

### M2: Questionamento Construtivo
```
SEMPRE questionar quando:
- Requisito não está claro
- Solução pode impactar performance
- Há possibilidade de melhoria
- Conflito com ADRs existentes
```

### M3: Evolução Contínua
```
SEMPRE considerar:
- Como facilitar manutenção futura
- Como permitir escalabilidade
- Como simplificar operação
- Como reduzir riscos
```

### M4: Colaboração Ativa
```
SEMPRE sugerir:
- Melhorias de processo
- Otimizações de código
- Documentação adicional
- Testes adicionais relevantes
```

---

**Engenheiro de Prompt**: Rogerio Lamarques + GitHub Copilot  
**Data**: 24/09/2025  
**Versão**: 1.0  
**Status**: Ativo para todas as tasks do projeto
