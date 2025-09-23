# Architecture Decision Records (ADRs)

## Índice de Decisões Arquiteturais

| ADR | Título | Status | Data |
|-----|--------|--------|------|
| [ADR-001](ADR-001-layered-architecture.md) | Arquitetura em Camadas (Layered Architecture) | ✅ Aceito | 23/09/2025 |
| [ADR-002](ADR-002-repository-service-pattern.md) | Repository Pattern com Service Layer | ✅ Aceito | 23/09/2025 |
| [ADR-003](ADR-003-uuid-primary-keys.md) | UUID como Primary Keys | ✅ Aceito | 23/09/2025 |
| [ADR-004](ADR-004-queue-system.md) | Sistema de Filas para Processamento Assíncrono | ✅ Aceito | 23/09/2025 |
| [ADR-005](ADR-005-optimistic-locking.md) | Versionamento Otimista para Controle de Concorrência | ✅ Aceito | 23/09/2025 |
| [ADR-006](ADR-006-observability-first.md) | Observabilidade como Prioridade | ✅ Aceito | 23/09/2025 |

## Resumo das Decisões

### 🏗️ **Arquitetura Geral**
- **ADR-001**: Arquitetura em 4 camadas (Presentation → Application → Domain → Infrastructure)
- **ADR-002**: Padrões Repository + Service Layer para separação de responsabilidades

### 🔧 **Decisões Técnicas**
- **ADR-003**: UUIDs como chaves primárias para escalabilidade horizontal
- **ADR-004**: Sistema de filas (Redis) para processamento assíncrono
- **ADR-005**: Versionamento otimista para controle de concorrência

### 📊 **Qualidade e Operação**
- **ADR-006**: Observabilidade como requisito de primeira classe

## Como Usar os ADRs

### Para Desenvolvedores
1. **Sempre consulte** os ADRs antes de implementar features relacionadas
2. **Siga os padrões** estabelecidos nas decisões
3. **Proponha novos ADRs** para decisões arquiteturais significativas

### Para Novos Membros do Time
1. **Leia todos os ADRs** para entender o contexto das decisões
2. **Entenda as consequências** de cada decisão
3. **Questione quando necessário** - ADRs podem ser revisados

### Template para Novos ADRs
```markdown
# ADR-XXX: [Título da Decisão]

## Status
[Proposto | Aceito | Depreciado | Superseded by ADR-XXX]

## Contexto
[Descrever o problema/necessidade que motivou a decisão]

## Decisão
[Descrever a decisão tomada e sua implementação]

## Consequências
### Positivas
- [Benefícios da decisão]

### Negativas  
- [Trade-offs e limitações]

## Alternativas Consideradas
- [Outras opções avaliadas e por que foram descartadas]

---
**Data**: [DD/MM/AAAA]
**Autor**: [Nome]
**Revisores**: [Nomes]
```

## Processo de Criação de ADRs

1. **Identificar necessidade** de decisão arquitetural
2. **Pesquisar alternativas** e trade-offs
3. **Escrever ADR** seguindo o template
4. **Revisar com time** (se aplicável)
5. **Implementar** a decisão
6. **Atualizar índice** neste arquivo

## Versionamento
- ADRs são **imutáveis** após aceitos
- Mudanças significativas requerem **novo ADR**
- ADRs antigos podem ser marcados como **"Superseded by ADR-XXX"**

---

**Projeto**: Desafio Tecnofit - Saque PIX  
**Última Atualização**: 23/09/2025  
**Total de ADRs**: 6
