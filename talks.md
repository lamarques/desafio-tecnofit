# Conversas e Decisões - Desafio Tecnofit

## 📝 Registro de Conversas com IA

### Data: 23/09/2025

---

## Conversa 1: Análise Inicial do Desafio

**Contexto**: Início do projeto, análise do challenge.md e criação do plano de trabalho.

### Pergunta do Usuário:
> "Vamos analisar o arquivo challenge.md e vamos criar um TODO para que possamos ter um alto entendimento dessa tarefa e por fim desenvolver esse desafio."

### Análise da IA:
1. **Compreensão do Desafio**: Desenvolvimento de API para saque PIX com agendamento
2. **Tecnologias Definidas**: PHP Hyperf 3, MySQL 8, Docker
3. **Requisitos Principais**:
   - Saque imediato (horário comercial)
   - Saque agendado (fora do horário)
   - Validação de saldo
   - Sistema de notificação por email
   - Processamento via cron

### Decisões Tomadas:

#### **Decisão 1: Estruturação do Projeto**
- **O que**: Criar arquivos separados para TODO (todo.md) e conversas (talks.md)
- **Por que**: Organização clara do processo de desenvolvimento e rastreabilidade das decisões
- **Benefício**: Facilita revisão e entendimento do processo pelos avaliadores

#### **Decisão 2: Abordagem de Desenvolvimento**
- **O que**: Dividir o projeto em fases bem definidas
- **Por que**: Permite desenvolvimento incremental e controle de qualidade
- **Fases definidas**:
  1. Foundation (Análise + Setup)
  2. Implementation (Core + APIs)
  3. Enhancement (Performance + Testes)
  4. Documentation (Entrega)

#### **Decisão 3: Padrões Arquiteturais**
- **Repository Pattern**: Para abstrair acesso aos dados
- **Service Layer**: Para lógica de negócio
- **Factory Pattern**: Para diferentes tipos de saque
- **Queue System**: Para processamento assíncrono

**Justificativa**: Esses padrões garantem:
- Separação de responsabilidades
- Facilidade de testes
- Manutenibilidade
- Escalabilidade

#### **Decisão 4: Estratégia de Documentação**
- **O que**: Documentar TODAS as decisões técnicas
- **Por que**: O desafio foca no processo, não apenas no resultado
- **Como**: Registrar conversas, motivações e alternativas consideradas

---

---

## Conversa 2: Desenho da Arquitetura

**Contexto**: Criação do documento de arquitetura (architecture.md) baseado na análise do challenge.md.

### Pergunta do Usuário:
> "Certo vamos iniciar com o desenho e documento de arquitetura. Essa discovery podemos colocar em um arquivo architecture.md."

### Análise e Decisões Arquiteturais:

#### **Decisão 5: Arquitetura em Camadas (Layered Architecture)**
- **O que**: Implementar arquitetura em 4 camadas bem definidas
- **Camadas**:
  1. **Presentation** (Controllers)
  2. **Application** (Services) 
  3. **Domain** (Models & Entities)
  4. **Infrastructure** (Repositories & External)
- **Por que**: 
  - Separação clara de responsabilidades
  - Facilita testes e manutenção
  - Permite evolução independente das camadas
  - Segue princípios SOLID

#### **Decisão 6: Repository Pattern + Service Layer**
- **O que**: Padrão Repository para acesso a dados + Service Layer para lógica de negócio
- **Por que**: 
  - Abstração da camada de dados
  - Facilita testes unitários (mocking)
  - Permite mudanças na persistência sem impactar negócio
  - Centraliza lógica de negócio nos services

#### **Decisão 7: UUID como Primary Keys**
- **O que**: Usar UUIDs em vez de auto-increment integers
- **Por que**:
  - **Escalabilidade horizontal**: Sem conflitos entre instâncias
  - **Segurança**: IDs não são sequenciais/previsíveis  
  - **Distribuição**: Facilita integração com outros sistemas
  - **Performance**: Hyperf tem boa performance com UUIDs

#### **Decisão 8: Queue System para Emails**
- **O que**: Sistema assíncrono para envio de emails
- **Por que**:
  - **Performance**: Não bloqueia resposta da API
  - **Resiliência**: Retry automático em falhas
  - **Escalabilidade**: Processamento distribuído
  - **User Experience**: Resposta imediata ao usuário

#### **Decisão 9: Versionamento Otimista para Concorrência**
- **O que**: Campo `version` na tabela `account` 
- **Por que**:
  - **Performance**: Evita locks de banco
  - **Concorrência**: Detecta conflitos de saldo
  - **Consistência**: Previne condições de corrida
  - **Escalabilidade**: Funciona bem distribuído

#### **Decisão 10: Observabilidade como Prioridade**
- **O que**: Logs estruturados, métricas e health checks desde o início
- **Por que**:
  - **Debugging**: Facilita identificação de problemas
  - **Monitoramento**: Visibility em produção
  - **Performance**: Identificação de gargalos
  - **SLA**: Tracking de disponibilidade

### Alternativas Consideradas:

#### **Alternativa 1: Microserviços vs Monolito Modular**
- **Escolhido**: Monolito modular (por enquanto)
- **Descartado**: Microserviços
- **Motivo**: Para este escopo, monolito modular oferece:
  - Desenvolvimento mais rápido
  - Deploy simplificado
  - Menor complexidade operacional
  - Facilita debugging
  - **Obs**: Arquitetura permite evolução para microserviços

#### **Alternativa 2: Auto-increment vs UUID**
- **Escolhido**: UUID
- **Descartado**: Auto-increment integers
- **Motivo**: UUID oferece melhor escalabilidade horizontal

#### **Alternativa 3: Pessimistic vs Optimistic Locking**
- **Escolhido**: Optimistic Locking (version field)
- **Descartado**: Pessimistic Locking (SELECT FOR UPDATE)
- **Motivo**: Melhor performance em alta concorrência

### Principais Componentes Definidos:

1. **API Layer**: Controllers com validação robusta
2. **Service Layer**: WithdrawService, BalanceService, EmailService
3. **Repository Layer**: Abstração do acesso aos dados
4. **Queue System**: Processamento assíncrono
5. **Cron Worker**: Processamento de agendamentos
6. **Observability**: Logs, métricas, health checks

### Fluxos Mapeados:
- **Saque Imediato**: API → Service → Repository → Queue
- **Saque Agendado**: API → Repository → Cron → Service → Queue  
- **Processamento de Falha**: Saque → Erro → Log → Metrics

---

## Conversa 3: Transformação em ADRs (Architecture Decision Records)

**Contexto**: Conversão da documentação de arquitetura para o formato padrão de ADRs.

### Pergunta do Usuário:
> "O que você acha de transformarmos essa documentação de arquitetura em ADRs?"

### Análise da IA:
Excelente ideia! ADRs são a prárica padrão da indústria para documentar decisões arquiteturais de forma estruturada e rastreável.

### Implementação Realizada:

#### **Decisão 11: Estruturação em ADRs**
- **O que**: Converter todas as decisões arquiteturais para formato ADR
- **Por que**: 
  - **Padrão da indústria**: Formato reconhecido universalmente
  - **Rastreabilidade**: Histórico claro das decisões
  - **Contexto preservado**: Motivações e alternativas documentadas
  - **Onboarding**: Novos desenvolvedores entendem o "porquê"
  - **Governança**: Processo estruturado para decisões futuras

#### **ADRs Criados**:
1. **ADR-001**: Arquitetura em Camadas (Layered Architecture)
2. **ADR-002**: Repository Pattern com Service Layer  
3. **ADR-003**: UUID como Primary Keys
4. **ADR-004**: Sistema de Filas para Processamento Assíncrono
5. **ADR-005**: Versionamento Otimista para Controle de Concorrência
6. **ADR-006**: Observabilidade como Prioridade

#### **Estrutura Organizacional**:
```
docs/adr/
├── README.md              # Índice e guia dos ADRs
├── ADR-001-layered-architecture.md
├── ADR-002-repository-service-pattern.md
├── ADR-003-uuid-primary-keys.md
├── ADR-004-queue-system.md
├── ADR-005-optimistic-locking.md
└── ADR-006-observability-first.md
```

#### **Template Padronizado**:
Cada ADR segue a estrutura:
- **Status**: Estado da decisão
- **Contexto**: Problema que motivou a decisão
- **Decisão**: Solução escolhida e implementação
- **Consequências**: Positivas e negativas
- **Alternativas Consideradas**: Opções avaliadas e descartadas

### Benefícios Obtidos:

1. **Documentação Estruturada**: Cada decisão tem contexto completo
2. **Rastreabilidade**: Fácil entender evolução das decisões
3. **Knowledge Sharing**: Base de conhecimento para o time
4. **Processo Definido**: Template para futuras decisões
5. **Compliance**: Atende padrões de governança de software

### Próximos Passos com ADRs:
- **Manter atualizados**: Cada nova decisão arquitetural vira ADR
- **Revisar periodicamente**: ADRs podem ser superseded
- **Usar em code reviews**: Garantir aderência às decisões
- **Onboarding**: Novos desenvolvedores leem todos os ADRs

---

## Conversa 4: Documentação Executiva e de Negócios

**Contexto**: Criação de documentação focada em negócios para apresentação em nível de diretoria.

### Pergunta do Usuário:
> "Eu preciso, também, de um desenho de arquitetura para alguma reunião em nível de diretoria. Com um papo de negócio e menos em nível técnico."

### Análise da IA:
Excelente necessidade! Documentação técnica detalhada (ADRs) é essencial para o time de desenvolvimento, mas executivos precisam de uma visão de **valor de negócio**, **ROI**, **riscos** e **impactos estratégicos**.

### Implementação Realizada:

#### **Decisão 12: Documentação Executiva Separada**
- **O que**: Criar documentação específica para stakeholders de negócio
- **Por que**: 
  - **Audiência diferente**: Executivos focam em valor, não em tecnologia
  - **Linguagem adequada**: Termos de negócio vs. técnicos
  - **Foco em ROI**: Investimento, retorno e riscos
  - **Decisão estratégica**: Justificar aprovação do projeto
  - **Compliance**: Atender governança corporativa

#### **Documentos Criados**:

1. **`business-architecture.md`** - Arquitetura de Negócios Completa
   - 📊 Resumo executivo com valor de negócio
   - 🎯 Objetivos estratégicos e métricas
   - 🏢 Visão de componentes de negócio (não técnicos)
   - 📈 Casos de uso práticos com cenários reais
   - 💼 Benefícios quantificados e vantagens competitivas
   - ⚠️ Gestão de riscos e controles
   - 💰 Análise de investimento e ROI
   - 🚀 Roadmap de implementação

2. **`executive-diagrams.md`** - Diagramas Visuais Simplificados
   - 🎯 Fluxos de negócio visuais (não-técnicos)
   - 📊 Cenários de uso ilustrados
   - 🏢 Componentes organizados por valor de negócio
   - 💰 Impacto financeiro visualizado
   - ⚡ KPIs e métricas de sucesso
   - 🛡️ Matriz de riscos vs controles
   - 📅 Timeline executivo
   - 📈 ROI projetado

#### **Destaques da Abordagem de Negócios**:

##### **Linguagem Executiva**:
- ❌ ~~"Repository Pattern com Dependency Injection"~~
- ✅ **"Sistema robusto com alta disponibilidade"**

- ❌ ~~"Queue system com Redis para performance"~~  
- ✅ **"Processamento inteligente que garante rapidez"**

- ❌ ~~"Optimistic locking para controle de concorrência"~~
- ✅ **"Controles avançados contra erros financeiros"**

##### **Métricas de Negócio**:
- **Performance**: < 2 segundos (satisfação do cliente)
- **Disponibilidade**: 99.9% (confiabilidade da marca)  
- **Capacidade**: 100.000+ transações/mês (crescimento suportado)
- **ROI**: 125% em 12 meses (retorno atrativo)

##### **Gestão de Riscos Executiva**:
- **Saldo Negativo**: CONTROLADO ✅
- **Fraudes**: MONITORADO ✅  
- **Indisponibilidade**: MITIGADO ✅
- **Compliance**: ATENDIDO ✅

### Estrutura dos Documentos:

#### **Para Executivos (`business-architecture.md`)**:
1. Resumo Executivo (elevator pitch)
2. Objetivos Estratégicos (OKRs)
3. Casos de Uso (cenários reais)
4. Benefícios Quantificados (ROI, métricas)
5. Gestão de Riscos (compliance)
6. Investimento e Timeline (aprovação)

#### **Para Apresentações (`executive-diagrams.md`)**:
1. Fluxos visuais simples
2. Componentes por valor de negócio  
3. Métricas e KPIs visuais
4. ROI e payback ilustrados
5. Timeline de entrega
6. Recomendação clara

### Diferenciação de Audiências:

| Aspecto | **Técnica (ADRs)** | **Executiva (Business)** |
|---------|-------------------|-------------------------|
| **Foco** | Como implementar | Por que investir |
| **Linguagem** | Técnica especializada | Negócios e finanças |
| **Métricas** | Latência, throughput | ROI, satisfação cliente |
| **Riscos** | Bugs, performance | Financeiros, reputação |
| **Decisões** | Padrões arquiteturais | Aprovação de investimento |

---

## Conversa 5: KPIs e OKRs para Governança do Projeto

**Contexto**: Criação de documentos de KPIs (Key Performance Indicators) e OKRs (Objectives and Key Results) para governança e acompanhamento executivo.

### Pergunta do Usuário:
> "Certo, Você consegue, também, criar um documentos de KPI e de OKR para esse projeto?"

### Análise da IA:
Fundamental! KPIs e OKRs são essenciais para governança de projetos de alta visibilidade. Eles estabelecem **métricas objetivas**, **metas ambiciosas** e **accountability** para todos os stakeholders.

### Implementação Realizada:

#### **Decisão 13: Framework de Métricas Abrangente**
- **O que**: Criar sistema completo de KPIs + OKRs alinhados estrategicamente
- **Por que**: 
  - **Governança**: Acompanhamento objetivo do progresso
  - **Accountability**: Responsabilidades claras por métrica
  - **Alinhamento**: Conectar execução com estratégia
  - **Tomada de decisão**: Dados para decisões executivas
  - **Continuous improvement**: Base para otimizações

#### **Documentos Criados**:

### 1. **`kpis.md`** - Key Performance Indicators
**17 KPIs organizados em 5 categorias:**

#### **🎯 Performance Técnica** (4 KPIs)
- **Tempo de Resposta**: < 2s (satisfação cliente)
- **Disponibilidade**: 99.9% (confiabilidade)
- **Taxa de Sucesso**: > 99.5% (qualidade)
- **Throughput**: > 1.000 transações/min (capacidade)

#### **💼 Negócio** (4 KPIs)  
- **Volume Transações**: > 100k/mês (adoção)
- **Valor Movimentado**: > R$ 100M/mês (impacto)
- **Ticket Médio**: R$ 800-1.200 (perfil usuário)
- **Taxa Agendamento**: 15-25% (conveniência)

#### **👥 Experiência Cliente** (3 KPIs)
- **NPS**: > 70 (satisfação)
- **Tempo Notificação**: < 30s (transparência)
- **Taxa Reclamações**: < 0.1% (qualidade)

#### **🔐 Segurança** (3 KPIs)
- **Bloqueios Saldo Negativo**: 100% efetivos
- **Cobertura Logs**: 100% (compliance)
- **Detecção Anomalias**: < 5min (prevenção)

#### **💰 Financeiros** (3 KPIs)
- **Custo por Transação**: < R$ 0.50
- **ROI**: > 125% ano 1
- **Economia Operacional**: > R$ 50k/mês

### 2. **`okrs.md`** - Objectives and Key Results
**6 Objetivos estratégicos em 3 trimestres:**

#### **Q4 2025 - Lançamento** (2 Objectives)
1. **Excelência Operacional**
   - 99.5% disponibilidade
   - ≤ 3s tempo resposta
   - Zero incidentes críticos
   - 100% logs auditoria

2. **Adoção Inicial**
   - 50k+ saques/mês
   - NPS ≥ 60
   - 98% taxa sucesso
   - ≤ 0.2% reclamações

#### **Q1 2026 - Crescimento** (2 Objectives)
3. **Escalar Volume**
   - 150k+ saques/mês
   - ≤ 1.5s tempo resposta  
   - R$ 0.40 custo/transação
   - 20% uso agendamento

4. **Maximizar ROI**
   - 75% ROI acumulado
   - 60% redução custos
   - 90% automação
   - R$ 150M/mês volume

#### **Q2 2026 - Excelência** (2 Objectives)
5. **Padrão Indústria**
   - 99.95% disponibilidade
   - NPS ≥ 80
   - ≤ 2min detecção anomalias
   - PCI DSS certificação

6. **Liderança Mercado**
   - 300k+ saques/mês
   - #1 velocidade mercado
   - 200% ROI cumulativo
   - Case study publicado

### **Framework de Governança**:

#### **Dashboards Executivos**:
- **Tempo real**: KPIs críticos com alertas
- **Semanal**: Progresso OKRs para teams
- **Mensal**: Consolidado para gerência
- **Trimestral**: Estratégico para diretoria

#### **Processo de Revisão**:
- **Daily**: Monitoramento automático
- **Weekly**: Check-ins de progresso
- **Monthly**: Análise de tendências
- **Quarterly**: Avaliação e planejamento

#### **Responsabilidades Definidas**:
- **Product Owner**: KPIs de negócio + OKR Champion
- **Tech Lead**: KPIs técnicos
- **Security Lead**: KPIs de segurança
- **Finance**: KPIs financeiros

### **Alinhamento Estratégico**:

#### **Cascateamento Organizacional**:
```
COMPANY OKRs
    ↓
PRODUCT OKRs (Este projeto)
    ↓  
TEAM OKRs
    ↓
INDIVIDUAL OKRs
```

#### **Conexão com Valor de Negócio**:
- **Cada KPI** conecta com objetivo de negócio
- **Cada OKR** alinhado com estratégia da empresa
- **Métricas balanceadas**: Técnicas + Negócio + Cliente
- **Ambição apropriada**: Metas desafiadoras mas atingíveis

### **Diferencial da Abordagem**:

#### **Métricas Inteligentes**:
- ❌ ~~"Bugs encontrados"~~ → ✅ **"Taxa de sucesso das transações"**
- ❌ ~~"Uptime do servidor"~~ → ✅ **"Disponibilidade percebida pelo cliente"**
- ❌ ~~"Velocidade da API"~~ → ✅ **"Tempo de resposta da jornada completa"**

#### **Balanceamento Estratégico**:
- **30% Técnico**: Performance, disponibilidade, segurança
- **40% Negócio**: Volume, ROI, crescimento
- **30% Cliente**: Satisfação, experiência, conveniência

---

## Conversa 6: Inicialização do Repositório Git

**Contexto**: Configuração do repositório Git e preparação para envio ao GitHub.

### Pergunta do Usuário:
> "Bom agora vamo iniciar o repositório para em seguida enviar para o github"
> "o repositório é git remote add origin git@github.com:lamarques/desafio-tecnofit.git"
> "o nome correto é Rogerio Lamarques e o email é rogerio.lamarques@gmail.com"

### Implementação Realizada:

#### **Decisão 14: Setup Completo do Repositório**
- **O que**: Configurar repositório Git com todos os arquivos de projeto padrão
- **Por que**: 
  - **Versionamento**: Controlar evolução do código
  - **Colaboração**: Facilitar trabalho em equipe
  - **Backup**: Garantir segurança dos arquivos
  - **CI/CD**: Base para pipelines automatizados
  - **Open Source**: Disponibilizar para avaliação

#### **Arquivos Criados para o Repositório**:

1. **`README.md`** - Documentação principal completa
   - Badges de status e tecnologias
   - Quick start e instalação
   - Documentação da API
   - Arquitetura e fluxos
   - Monitoramento e observabilidade
   - Roadmap e contribuição

2. **`.gitignore`** - Exclusões apropriadas
   - Arquivos de configuração sensíveis (.env)
   - Cache e logs (storage/, runtime/)
   - Dependências (vendor/, node_modules/)
   - IDE e OS específicos

3. **`LICENSE`** - MIT License
   - Permissões claras de uso
   - Copyright: Rogerio Lamarques

4. **`CONTRIBUTING.md`** - Guidelines de contribuição
   - Processo de fork e PR
   - Padrões de código (PSR-12, PhpStan)
   - Conventional Commits
   - Templates de PR e issues

#### **Configurações Git**:
```bash
# Configurações globais aplicadas
git config --global user.name "Rogerio Lamarques"
git config --global user.email "rogerio.lamarques@gmail.com"

# Repositório remoto configurado
git remote add origin git@github.com:lamarques/desafio-tecnofit.git
```

#### **Primeiro Commit Estruturado**:
**Commit**: `feat: documentação completa do projeto`
- **19 arquivos** criados
- **3.745 linhas** de documentação
- **6 ADRs** detalhados
- **Framework completo** de KPIs/OKRs
- **Documentação executiva** para diretoria

#### **Estrutura Final do Repositório**:
```
desafio-tecnofit/
├── .gitignore              # Exclusões Git
├── CONTRIBUTING.md         # Guidelines contribuição
├── LICENSE                 # MIT License
├── README.md              # Documentação principal
├── architecture.md        # Arquitetura técnica
├── challenge.md          # Especificação original
├── talks.md              # Registro conversas IA
├── todo.md               # Checklist desenvolvimento
└── docs/
    ├── adr/              # Architecture Decision Records
    │   ├── README.md
    │   ├── ADR-001-layered-architecture.md
    │   ├── ADR-002-repository-service-pattern.md
    │   ├── ADR-003-uuid-primary-keys.md
    │   ├── ADR-004-queue-system.md
    │   ├── ADR-005-optimistic-locking.md
    │   └── ADR-006-observability-first.md
    ├── business-architecture.md  # Visão executiva
    ├── executive-diagrams.md    # Diagramas simplificados
    ├── kpis.md                  # Key Performance Indicators
    └── okrs.md                  # Objectives & Key Results
```

### Próximo Passo: Push para GitHub
```bash
# Comando para enviar para GitHub (quando pronto)
git push -u origin main
```

---

## Próximas Conversas

### Tópicos para Próximas Discussões:
1. ✅ **Desenho da Arquitetura**: ~~Como estruturar os componentes?~~
2. ✅ **ADRs**: ~~Estruturação das decisões arquiteturais~~
3. ✅ **Documentação Executiva**: ~~Visão de negócios para diretoria~~
4. ✅ **KPIs e OKRs**: ~~Framework de métricas e governança~~
5. ✅ **Repositório Git**: ~~Inicialização e configuração~~
6. **Push para GitHub**: Enviar primeira versão
7. **Setup Docker**: Configuração completa do ambiente de desenvolvimento
8. **Estrutura de Pastas**: Organização dos arquivos seguindo DDD
9. **Modelagem Detalhada do Banco**: Refinar constraints e relacionamentos
10. **Padrões de Código**: Definir convenções e interfaces

---

**Observações Importantes**:
- Manter foco na qualidade sobre velocidade
- Documentar alternativas consideradas, mesmo as descartadas
- Explicar o "porquê" de cada decisão técnica
- Considerar cenários de erro e edge cases desde o início
- **ADRs são imutáveis**: Mudanças requerem novos ADRs
- **Documentação em camadas**: Técnica (ADRs) + Negócios (Business) + Governança (KPIs/OKRs)
- **Métricas orientam decisões**: Todos os KPIs/OKRs devem gerar ação
- **Repositório estruturado**: Pronto para colaboração e CI/CD
- **Arquitetura permite evolução**: Monolito → Microserviços quando necessário

---
**Última Atualização**: 23/09/2025 - Repositório Git configurado e primeiro commit realizado
