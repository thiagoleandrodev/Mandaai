# ADR-0004: Prisma + Neon Postgres

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

O domínio do Mandaaí (ver `docs/erd.md`) tem um modelo relacional com relações
claras — usuário/endereço, restaurante/seção de cardápio/produto/grupo de
opção/opção, e pedido/item de pedido/opção de item de pedido com campos de
snapshot para preservar o que o cliente efetivamente pagou. Esse modelo se
beneficia de um ORM com schema tipado e migrations versionadas, e de um banco
relacional real (não é um domínio document-oriented). Como o projeto é MVP/solo,
também queremos evitar provisionar e operar um servidor Postgres tradicional
(EC2, RDS com VPC, etc.) — preferimos algo com setup mínimo e integração de
1-clique com a plataforma de deploy do frontend.

## Decisão

Decidimos usar **Prisma** como ORM/query builder, com schema declarado em
`schema.prisma` e migrations geradas via `prisma migrate`, rodando sobre
**Neon Postgres** (Postgres serverless). A escolha do Neon aproveita a integração
nativa com a Vercel (variável de ambiente de conexão provisionada automaticamente
ao linkar o projeto).

## Consequências

- Facilita: schema Prisma serve como fonte única de verdade do modelo de dados,
  gerando client tipado em TypeScript compatível com o ERD documentado.
- Facilita: `prisma migrate dev` cobre o ciclo de vida de migrations sem precisar
  escrever SQL manual, reduzindo atrito para quem está aprendendo.
- Facilita: Neon é serverless (escala a zero, sem gerenciamento de instância) e a
  integração com Vercel elimina configuração manual de conexão em produção.
- Dificulta: Prisma abstrai bastante do SQL gerado — consultas muito específicas
  (ex: agregações complexas de relatório) podem exigir `$queryRaw`, perdendo parte
  da tipagem.
- Compromete a manter: dependência de uma plataforma serverless de terceiros
  (Neon) para o banco de produção — cold starts de conexão são um ponto de atenção
  em picos de tráfego, mitigável com connection pooling (Neon fornece pooler).

## Alternativas consideradas

- **TypeORM**: descartado por ter uma API mais verbosa (decorators de entidade,
  configuração de conexão menos direta) e uma experiência de migrations
  historicamente menos confiável que a do Prisma.
- **Drizzle**: descartado apesar de ser mais leve e "SQL-like" — para o público
  de mentoria, a geração de client tipado e a DX de `prisma studio`/`prisma
  migrate` do Prisma têm melhor curva de aprendizado que a abordagem mais manual
  do Drizzle (schema como código TS puro, sem CLI de migration tão guiada).
- **Supabase (Postgres gerenciado)**: descartado como banco principal deste ADR
  porque o pivô de arquitetura já deixou de usar o ecossistema Supabase (auth,
  storage) documentado na spec anterior — manter apenas o banco Supabase sem o
  resto do ecossistema não trazia vantagem sobre Neon, que tem integração mais
  direta com a Vercel.
- **Postgres auto-gerenciado (Docker/VPS)**: descartado por exigir operação de
  infraestrutura (backups, patches, scaling manual) incompatível com o escopo de
  MVP solo.
