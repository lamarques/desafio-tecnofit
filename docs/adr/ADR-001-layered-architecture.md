# ADR-001: Arquitetura em Camadas (Layered Architecture)

## Status
Aceito

## Contexto
O sistema de saque PIX precisa de uma arquitetura que permita:
- Separação clara de responsabilidades
- Facilidade de testes e manutenção
- Evolução independente de componentes
- Escalabilidade e flexibilidade

## Decisão
Implementar arquitetura em 4 camadas bem definidas:

1. **Presentation Layer** (Controllers)
   - Receber requisições HTTP
   - Validar entrada
   - Orquestrar serviços

2. **Application Layer** (Services)
   - Lógica de negócio
   - Orquestração de operações
   - Coordenação entre repositórios

3. **Domain Layer** (Models & Entities)
   - Regras de domínio
   - Entidades de negócio
   - Value Objects

4. **Infrastructure Layer** (Repositories & External)
   - Persistência de dados
   - Integrações externas
   - Infraestrutura técnica

## Consequências

### Positivas
- **Manutenibilidade**: Cada camada tem responsabilidades bem definidas
- **Testabilidade**: Fácil criação de testes unitários por camada
- **Flexibilidade**: Mudanças em uma camada não afetam outras
- **Onboarding**: Estrutura familiar para novos desenvolvedores
- **Escalabilidade**: Permite evolução para microserviços

### Negativas
- **Complexidade inicial**: Mais arquivos e estruturas
- **Overhead**: Pode ser excessivo para funcionalidades simples
- **Performance**: Possível overhead de chamadas entre camadas

## Alternativas Consideradas
- **Arquitetura MVC simples**: Descartada por falta de escalabilidade
- **Hexagonal Architecture**: Considerada, mas complexa demais para o escopo
- **Microserviços**: Descartada por complexidade operacional desnecessária

---
**Data**: 23/09/2025  
**Autor**: Rogerio Lamarques + GitHub Copilot  
**Revisores**: -
