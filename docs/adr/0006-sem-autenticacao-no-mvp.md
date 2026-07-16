# ADR-0006: Sem autenticação no MVP

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

A tela 07 (Login/Cadastro) existe no design hi-fi e a spec de arquitetura anterior
(`mandaai-arquitetura.md`) descreve auth via OTP/Twilio e OAuth. No entanto, o
foco declarado deste MVP é validar a arquitetura e o fluxo de pedido de ponta a
ponta (catálogo -> sacola -> checkout -> tracking) — não o ciclo de vida de conta
de usuário. Implementar autenticação completa (OTP, sessão, OAuth, recuperação de
acesso) antes de ter o fluxo de pedido funcionando desviaria o esforço de
aprendizado do que é o núcleo do domínio (`docs/erd.md`: Order, OrderItem,
OptionGroup etc.) para infraestrutura de identidade, que é um problema
genérico e bem documentado à parte.

## Decisão

Decidimos **não implementar autenticação no MVP**. O `User` do ERD existe como
entidade de domínio (pedidos precisam de um `user_id`), mas no MVP ele é
resolvido de forma simplificada (ex: usuário fixo/seed ou identificador de sessão
sem login real) — sem tela de login funcional, sem OTP, sem OAuth. A tela de
Login do design hi-fi permanece como referência visual para uma extensão futura,
mas fora do escopo de implementação do MVP.

## Consequências

- Facilita: todo o esforço de implementação do MVP fica concentrado no fluxo de
  pedido, que é o objetivo pedagógico e de produto central.
- Facilita: elimina dependências externas de auth (Twilio/OTP, provedor OAuth) e
  a complexidade de gestão de sessão/token no MVP.
- Dificulta: qualquer feature que dependa de identidade real (histórico de
  pedidos por usuário autenticado, Clube Mandaaí, endereços salvos por conta)
  fica bloqueada até a extensão pós-MVP que adicionar autenticação.
- Compromete a manter: o `user_id` simplificado usado no MVP precisará ser
  substituído por um usuário autenticado real na extensão futura — é um ponto de
  retrabalho já esperado e aceito.

## Alternativas consideradas

- **Auth completo desde o início (OTP via Twilio + OAuth, como na spec
  anterior)**: descartado porque adiaria a entrega do fluxo de pedido, que é o
  valor central do MVP, em favor de uma feature de infraestrutura genérica que
  pode ser adicionada depois sem redesenhar o domínio de pedido.
- **Auth simplificada com sessão local (ex: cookie/JWT sem provedor externo)**:
  considerada como meio-termo, mas descartada para o MVP porque ainda exigiria
  fluxo de cadastro/login funcional na UI (tela 07), o que não é necessário para
  validar o fluxo de pedido ponta a ponta.
