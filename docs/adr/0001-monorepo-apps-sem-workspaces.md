# ADR-0001: Monorepo com pastas `apps/*` sem workspaces

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

O Mandaaí é um projeto de escopo MVP/mentoria, com público-alvo de nível básico de
experiência em desenvolvimento. Precisamos de um único repositório abrigando frontend
(Next.js) e backend (Fastify), mas o objetivo pedagógico do projeto é maior que o
objetivo de escala de engenharia. Ferramentas de monorepo como Turborepo ou Nx
resolvem problemas (build cache distribuído, grafos de dependência entre pacotes,
publicação de pacotes compartilhados) que este projeto não tem — há apenas dois
apps, sem pacotes internos publicados, sem necessidade de build incremental
otimizado. Introduzir esse tooling agora adicionaria uma camada de configuração
(`turbo.json`, `pnpm-workspace.yaml`, resolução de workspace protocol) que não
ensina nada sobre o domínio do projeto (marketplace de delivery) e aumenta a
barreira de entrada para quem está aprendendo.

## Decisão

Decidimos estruturar o repositório como `apps/web` (Next.js) e `apps/api`
(Fastify), sem configurar workspaces de package manager (sem `pnpm-workspace.yaml`,
sem `package.json` raiz com `workspaces`). Cada app tem seu próprio
`package.json`, seu próprio `node_modules` e seu próprio ciclo de instalação/build,
porque nenhum código é compartilhado entre eles no MVP — não há necessidade de
resolver esse acoplamento com tooling de workspace.

## Consequências

- Facilita: onboarding simples — `cd apps/web && npm install` ou `cd apps/api && npm install`,
  sem aprender conceitos de workspace protocol, hoisting de dependências ou cache remoto.
- Facilita: cada app pode ser implantado (deploy) de forma totalmente independente,
  sem etapa de build orquestrada por uma ferramenta de monorepo.
- Dificulta: se no futuro surgir necessidade de compartilhar código (ex: tipos do
  Prisma, contratos de API) entre `apps/web` e `apps/api`, será preciso duplicar
  temporariamente ou migrar para workspaces — essa dívida é aceita conscientemente.
- Compromete a manter: duas instalações de dependências duplicadas (ex: TypeScript,
  ESLint) em vez de uma versão compartilhada — aceitável na escala atual do projeto.

## Alternativas consideradas

- **Turborepo**: descartado porque seu valor está em cache de build distribuído e
  pipelines entre múltiplos pacotes — nenhum dos dois existe aqui. Adicionaria
  `turbo.json` e conceitos (pipeline, cache, filtros) sem benefício didático.
- **Nx**: descartado pelo mesmo motivo, com custo adicional de configuração ainda
  maior (plugins, generators, project graph) — complexidade desproporcional ao
  tamanho do projeto.
- **pnpm/npm workspaces (sem Turbo/Nx)**: descartado porque, mesmo sendo mais leve,
  ainda introduz o conceito de workspace protocol (`workspace:*`) e resolução de
  dependências entre pacotes — não há pacote compartilhado no MVP que justifique isso.
- **Dois repositórios separados**: descartado porque dificulta a leitura conjunta
  do projeto para fins de mentoria/aprendizado — um único repositório com pastas
  `apps/*` já resolve a organização sem a complexidade de multi-repo (versionamento
  cruzado, CI duplicado).
