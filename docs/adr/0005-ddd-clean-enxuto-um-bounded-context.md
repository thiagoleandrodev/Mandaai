# ADR-0005: DDD/Clean enxuto com 1 bounded context

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

O modelo de domínio do Mandaaí (`docs/erd.md`) gira inteiramente em torno do fluxo
de pedido: usuário escolhe um restaurante, monta um pedido com produtos e opções,
finaliza no checkout e acompanha o status até a entrega
(`pending -> confirmed -> preparing -> delivering -> delivered`). Embora seja
possível enxergar sub-domínios (catálogo, pedido, pagamento, entrega) como
bounded contexts separados em um sistema maduro, para o escopo de MVP/mentoria
isso adicionaria conceitos (context maps, anti-corruption layers, eventos de
integração entre contextos) que não têm contrapartida de complexidade real no
código — tudo hoje cabe em um único fluxo coeso.

## Decisão

Decidimos modelar o domínio como **um único bounded context, "ordering"**,
contendo as entidades de catálogo (Restaurant, Product, OptionGroup/Option) e as
entidades de pedido (Order, OrderItem, OrderItemOption) sob a mesma linguagem
ubíqua. Aplicamos camadas de Clean Architecture de forma enxuta (domain, services,
repositories) mas **sem múltiplos bounded contexts, sem Result pattern
(tratamento de erro via exceptions/throws padrão) e sem CQRS** (não há separação
de modelos de leitura e escrita).

## Consequências

- Facilita: um único vocabulário e uma única pasta de domínio para navegar —
  reduz a carga cognitiva de decidir "isso pertence a qual contexto?".
- Facilita: sem CQRS, o mesmo service e o mesmo model atendem leitura e escrita,
  eliminando a necessidade de sincronizar dois modelos para o mesmo dado.
- Facilita: tratamento de erro via exceptions é o padrão já conhecido pela
  maioria de quem aprende TypeScript/Node, evitando a curva de aprendizado do
  Result/Either pattern.
- Dificulta: se o domínio crescer (ex: entidade Driver e painel do lojista da
  Phase 2, citados em `docs/erd.md`), pode ser necessário revisitar essa decisão
  e considerar a extração de um segundo bounded context (ex: "delivery").
- Compromete a manter: sem separação de contextos, o crescimento do domínio tende
  a inchar uma única pasta `domain/` — aceitável no tamanho atual do projeto.

## Alternativas consideradas

- **Múltiplos bounded contexts (catálogo, pedido, pagamento, entrega)**:
  descartado porque, no tamanho atual do domínio, a fronteira entre contextos
  seria artificial e exigiria eventos de integração e anti-corruption layers sem
  ganho real — overhead conceitual maior que o problema que resolve.
- **CQRS**: descartado porque não há hoje um caso de uso que exija modelos de
  leitura e escrita divergentes (ex: read model desnormalizado para performance)
  — introduzir CQRS agora seria complexidade especulativa.
- **Result/Either pattern para tratamento de erro**: descartado por adicionar uma
  camada de abstração funcional (tipos `Result<T, E>`, composição monádica) que
  não é o padrão idiomático mais comum em código TypeScript de nível iniciante —
  exceptions nativas cumprem o mesmo papel com menos conceitos novos.
