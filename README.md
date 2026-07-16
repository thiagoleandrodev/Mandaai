# Mandaaí

Marketplace hiperlocal de delivery para pequenos e médios negócios brasileiros — *"comida boa, do seu bairro, na sua porta"*.

Projeto de escopo MVP/mentoria: o objetivo é validar a arquitetura e o fluxo de pedido de ponta a ponta (catálogo → sacola → checkout → acompanhamento), com decisões de arquitetura documentadas e rastreáveis.

## Status

Fase de documentação e design concluída; implementação de código ainda não iniciada. O repositório contém:

- Especificação de arquitetura original (`mandaai-arquitetura.md`)
- Decisões arquiteturais atualizadas em ADRs (`docs/adr/`) — ver seção [Arquitetura](#arquitetura) abaixo, pois algumas decisões substituem a spec original
- Modelo de dados (`docs/erd.md`, `docs/erd.mmd`)
- Backlog de user stories (`docs/user-stories/`)
- Design hi-fi das 7 telas do produto (`Design_hi-fi_Mandaaa_web/`)

## Arquitetura

As decisões abaixo estão registradas em `docs/adr/` no formato Michael Nygard (contexto, decisão, consequências, alternativas). Consulte o [índice de ADRs](docs/adr/README.md) para o histórico completo.

| Camada | Decisão | ADR |
|--------|---------|-----|
| Estrutura do repo | Monorepo com `apps/web` e `apps/api`, sem workspaces (Turborepo/Nx/pnpm workspaces descartados) | [0001](docs/adr/0001-monorepo-apps-sem-workspaces.md) |
| Backend | Fastify + camadas manuais (`routes → controllers → services → domain → repositories`), sem container de DI | [0002](docs/adr/0002-fastify-ddd-manual-backend.md) |
| Frontend | Next.js 15, App Router, Server Components por padrão | [0003](docs/adr/0003-nextjs-15-app-router-frontend.md) |
| Banco de dados | Prisma ORM sobre Neon Postgres (serverless) | [0004](docs/adr/0004-prisma-neon-postgres.md) |
| Modelo de domínio | DDD/Clean enxuto, um único bounded context ("ordering"), sem CQRS, sem Result pattern | [0005](docs/adr/0005-ddd-clean-enxuto-um-bounded-context.md) |
| Autenticação | Sem autenticação no MVP — tela de login é só referência visual para extensão futura | [0006](docs/adr/0006-sem-autenticacao-no-mvp.md) |
| Documentação | Centralizada em `docs/` (ERD, ADR, User Stories), versionada com o código | [0007](docs/adr/0007-documentacao-em-docs.md) |

Essas decisões substituem parcialmente a stack descrita em `mandaai-arquitetura.md` (que citava FastAPI, SQLAlchemy e Supabase) — mantido como referência histórica do desenho inicial.

## Modelo de dados

O domínio gira em torno do fluxo de pedido: `User`/`Address`, catálogo (`Restaurant`, `MenuSection`, `Product`, grupos de opção), e pedido (`Order`, `OrderItem`, `OrderItemOption`) com snapshot de preço no momento da compra. Diagrama completo em [`docs/erd.md`](docs/erd.md).

Status do pedido: `pending → confirmed → preparing → delivering → delivered`.

## Design

7 telas em alta fidelidade (desktop e mobile): Home, Restaurante/Cardápio, Detalhe do Produto, Sacola, Checkout, Acompanhamento e Login.

- Arquivo fonte: [`Design_hi-fi_Mandaaa_web/Mandaai Telas.dc.html`](<Design_hi-fi_Mandaaa_web/Mandaai Telas.dc.html>)
- Exports em PNG: [`Design_hi-fi_Mandaaa_web/screenshots/`](<Design_hi-fi_Mandaaa_web/screenshots/>)

**Tokens de design:**

- Cores: Coral `#F5402C` (CTAs), Sol `#FFC02E` (clube/premium), Folha `#1FA463` (sucesso/entrega grátis), Tinta `#2A2320` (texto), Creme `#FBF7F2` (fundo)
- Tipografia: Fredoka (display) + DM Sans (corpo)
- Border radius: botões `999px`, cards `16px`, inputs `12px`

## Backlog

10 user stories derivadas do handoff visual e do modelo de dados, em [`docs/user-stories/`](docs/user-stories/README.md) — sem critérios de aceite detalhados nem story points (escopo de refinamento posterior).

## Idioma

Produto, copy e documentação em português brasileiro (pt-BR). Identificadores de código (variáveis, funções, rotas de API) em inglês.
