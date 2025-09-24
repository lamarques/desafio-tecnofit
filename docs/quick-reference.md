# Quick Reference - Prompt Engineering Rules

## 🚀 Guia de Referência Rápida

### ⚠️ REGRA FUNDAMENTAL
```
🚫 NUNCA execute múltiplas tasks simultaneamente
✅ UMA task por vez, do início ao fim completo  
✅ 100% dos AC da task atual antes de próxima
```

### Estrutura de Comando Padrão
```
TASK: [TASK-XXX] - [Título]
CONTEXT: [Breve contexto técnico]
GOAL: [Objetivo específico]
RULES: [ADRs relevantes]
OUTPUT: [Entregáveis esperados]
```

---

## 📋 Checklist Pré-Desenvolvimento

### Antes de Começar Qualquer Task
- [ ] **UMA TASK POR VEZ** - Confirmar que não há outra task em andamento
- [ ] **CRIAR BRANCH** - git checkout main && git pull && git checkout -b feature/TASK-XXX-description
- [ ] ADRs relevantes identificados
- [ ] Acceptance criteria claros
- [ ] Definition of Done definido
- [ ] Dependências mapeadas
- [ ] Persona de IA escolhida

### Durante o Desenvolvimento
- [ ] Logs estruturados sendo implementados
- [ ] Testes sendo escritos incrementalmente
- [ ] Performance sendo monitorada
- [ ] Segurança sendo validada
- [ ] Documentação sendo atualizada

### Antes de Finalizar
- [ ] Todos os AC atendidos
- [ ] Todos os testes passando
- [ ] Performance dentro do target
- [ ] Code review self-check
- [ ] DoD completo
- [ ] **PUSH E PR** - git push origin feature/TASK-XXX && abrir PR para main

---

## 🎯 ADRs por Tipo de Task

| Tipo de Task | ADRs Críticos |
|--------------|---------------|
| **Infraestrutura** | ADR-006 (Observabilidade) |
| **Backend Core** | ADR-001, ADR-002, ADR-003, ADR-005 |
| **Notificações** | ADR-004 (Queues), ADR-006 |
| **Segurança** | Todos os ADRs + Security Guidelines |
| **Qualidade** | ADR-006 + Testing Standards |

---

## ⚡ Commands de Emergência

### Git Flow Check
```
GIT STATUS: Verifique se está na branch correta feature/TASK-XXX
e se todos os commits têm task ID na mensagem.
```

### Verificação de Foco
```
FOCUS CHECK: Confirme que está executando APENAS [TASK-XXX] 
e nenhuma outra task em paralelo.
```

### Reset de Contexto
```
RESET: Você é um [Persona] executando [TASK-XXX]
Consulte /docs/prompt-templates.md para o template correto.
```

### Validação Rápida
```
VALIDATE: Verifique se a implementação atual de [TASK-XXX]
atende todos os Acceptance Criteria do backlog.
```

### Status Check
```
STATUS: Reporte progresso atual de [TASK-XXX] vs Definition of Done.
```

---

## 🌿 Git Flow Rápido

### Iniciar Task
```bash
git checkout main
git pull origin main
git checkout -b feature/TASK-XXX-short-description
```

### Durante Desenvolvimento
```bash
git add .
git commit -m "feat(TASK-XXX): description"
```

### Finalizar Task
```bash
git push origin feature/TASK-XXX-short-description
# Abrir PR no GitHub para main
# Após merge: git checkout main && git pull
```

---

## 🛠️ Personas Rápidas

- **Developer**: Implementação, testes, qualidade
- **Architect**: Decisões técnicas, patterns, design  
- **DevOps**: Infraestrutura, deploy, monitoramento
- **QA**: Testes, validação, qualidade
- **Security**: Segurança, compliance, auditoria

---

## 📊 Métricas de Sucesso

### Performance
- Response time < 2s
- Memory usage < 128MB
- DB query < 100ms

### Qualidade  
- Test coverage 100% (Services)
- PhpStan level 8
- PSR-12 compliance

### Segurança
- Input validation 100%
- Audit logs completos
- Zero vazamento de dados

---

**Uso**: Consulta rápida durante desenvolvimento  
**Atualização**: Conforme necessário
