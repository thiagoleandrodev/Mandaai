# ADR-0002: Fastify + DDD manual no backend

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

O backend do Mandaaí precisa expor uma API REST para o fluxo de pedido (catálogo de
restaurantes, cardápio, carrinho, checkout, tracking). Queremos camadas explícitas
e visíveis — `routes` -> `controllers` -> `services` -> `domain` -> `repositories` —
para que quem está aprendendo consiga seguir o caminho de uma requisição sem
precisar entender decorators, container de DI ou convenções implícitas de um
framework opinativo. Também é um requisito que o backend seja amigável a deploy
serverless (ex: Vercel Functions, AWS Lambda) ou a runtimes leves, já que o MVP não
justifica um servidor sempre ligado com infraestrutura pesada.

## Decisão

Decidimos usar **Fastify** como framework HTTP, com uma separação em camadas feita
manualmente (sem framework de DI): rotas registram schemas e chamam controllers,
controllers chamam services (regra de negócio), services usam entidades de domínio
simples e repositories (acesso a dados via Prisma). A injeção de dependências é
feita por composição manual (factory functions / construtores explícitos), sem
container de DI (nada de decorators `@Injectable`).

## Consequências

- Facilita: baixa curva de aprendizado — o fluxo de uma requisição é 100%
  rastreável lendo o código, sem "mágica" de reflection ou decorators.
- Facilita: Fastify tem baixo overhead e boot rápido, favorecendo cold starts em
  ambientes serverless.
- Facilita: schemas de validação nativos do Fastify (JSON Schema) documentam o
  contrato de cada rota sem depender de biblioteca extra.
- Dificulta: sem DI automática, adicionar uma nova dependência em profundidade
  (ex: um novo repository usado por vários services) exige atualizar manualmente
  cada ponto de composição — aceitável na escala atual (poucos services).
- Compromete a manter: não há convenção de framework "forçando" a estrutura —
  a disciplina de manter as camadas separadas depende de revisão de código, não
  de enforcement automático do framework.

## Alternativas consideradas

- **Express**: descartado por falta de validação de schema nativa e por padrões de
  middleware menos estruturados — exigiria mais bibliotecas extras (ex: Joi/Zod +
  wrapper de erro) para chegar ao nível de contrato que o Fastify já oferece.
- **NestJS**: descartado porque seu modelo de módulos, decorators e DI automática
  (padrão Angular-like) é justamente a "mágica" que queremos evitar nesse estágio
  didático — aprender Nest é aprender um framework opinativo antes de aprender o
  domínio do projeto.
- **DI com container (ex: InversifyJS, tsyringe)**: descartado porque adiciona um
  conceito extra (bindings, tokens, decorators de injeção) sem necessidade real —
  o número de dependências do MVP é pequeno o suficiente para composição manual.
