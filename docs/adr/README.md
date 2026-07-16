# Architecture Decision Records — Mandaaí

Este diretório documenta as decisões arquiteturais do projeto Mandaaí no formato
Michael Nygard: contexto (a força que motivou a decisão), decisão (o que foi
resolvido), consequências (o que isso facilita, dificulta ou compromete) e
alternativas consideradas (o que foi descartado e por quê).

Um ADR registra uma decisão já tomada, não uma discussão em aberto. Quando uma
decisão futura substituir uma existente, o ADR antigo não é apagado: seu status
muda para `Superseded by ADR-XXXX` e um novo ADR é criado.

## Índice

| ADR | Título | Status |
|-----|--------|--------|
| [0001](0001-monorepo-apps-sem-workspaces.md) | Monorepo com pastas `apps/*` sem workspaces | Accepted |
| [0002](0002-fastify-ddd-manual-backend.md) | Fastify + DDD manual no backend | Accepted |
| [0003](0003-nextjs-15-app-router-frontend.md) | Next.js 15 App Router no frontend | Accepted |
| [0004](0004-prisma-neon-postgres.md) | Prisma + Neon Postgres | Accepted |
| [0005](0005-ddd-clean-enxuto-um-bounded-context.md) | DDD/Clean enxuto com 1 bounded context | Accepted |
| [0006](0006-sem-autenticacao-no-mvp.md) | Sem autenticação no MVP | Accepted |
| [0007](0007-documentacao-em-docs.md) | Documentação em `docs/` (ERD, ADR, User Stories) | Accepted |

## Como criar um novo ADR

1. Copie o template abaixo para um novo arquivo `docs/adr/NNNN-slug-curto.md`,
   com `NNNN` sendo o próximo número sequencial de 4 dígitos e `slug-curto` em
   kebab-case.
2. Preencha as quatro seções com frases diretas ("decidimos X porque Y").
3. Liste alternativas reais que foram avaliadas e o motivo do descarte de cada
   uma — não liste alternativas que nunca foram de fato consideradas.
4. Adicione uma linha na tabela do índice acima.
5. Se este ADR substitui um anterior, atualize o `Status` do ADR antigo para
   `Superseded by ADR-NNNN` (não edite o conteúdo histórico dele).

## Template

```markdown
# ADR-NNNN: <título>

- **Status:** Accepted | Superseded by ADR-XXXX | Deprecated
- **Data:** YYYY-MM-DD

## Contexto

Qual problema ou força levou a esta decisão?

## Decisão

O que foi decidido, em frases curtas e diretas.

## Consequências

O que isso facilita, dificulta, ou compromete a manter.

## Alternativas consideradas

Outras opções e o motivo de cada uma ter sido descartada.
```
