# User Stories — Mandaaí

Backlog de user stories derivado do handoff visual (`Design_hi-fi_Mandaaa_web/Mandaai Telas.dc.html`, 7 telas) e do modelo de dados (`docs/erd.md`), com apoio da spec de arquitetura (`mandaai-arquitetura.md`). Sem critérios de aceite detalhados nem story points — isso é escopo de mentoria/refinamento posterior.

## Índice

| # | User Story | Tela(s) de referência |
|---|------------|------------------------|
| 01 | [Descobrir restaurantes](US-01-descobrir-restaurantes.md) | Home (desktop/mobile) |
| 02 | [Filtrar por categoria](US-02-filtrar-por-categoria.md) | Home (desktop/mobile) |
| 03 | [Buscar](US-03-buscar.md) | Home (desktop/mobile) |
| 04 | [Ver cardápio](US-04-ver-cardapio.md) | Restaurante / Cardápio (desktop/mobile) |
| 05 | [Customizar item](US-05-customizar-item.md) | Detalhe do Produto (desktop/mobile) |
| 06 | [Gerenciar sacola](US-06-gerenciar-sacola.md) | Carrinho / Sacola (desktop/mobile) |
| 07 | [Aplicar cupom](US-07-aplicar-cupom.md) (opcional MVP) | Carrinho / Sacola (desktop/mobile) |
| 08 | [Finalizar e receber código](US-08-finalizar-e-receber-codigo.md) | Checkout + Acompanhamento (desktop/mobile) |
| 09 | [Lidar com estados de erro](US-09-lidar-com-estados-de-erro.md) | — (sem mockup de erro/vazio no handoff) |
| 10 | [Trocar de restaurante com sacola](US-10-trocar-de-restaurante-com-sacola.md) (opcional MVP) | — (sem mockup no handoff) |

## Template padrão

```markdown
# US-NN: Título curto

**Como** [usuário], **quero** [ação], **para** [benefício].

- Referência visual: <tela/mockup>
- Estados envolvidos: <lista>
- Entidades de domínio: <lista>
- Escopo: o que entra / o que não entra
- [DECISÃO PENDENTE]: quando a fonte for ambígua ou o handoff não cobrir o cenário
```

## [SUGESTÃO ADICIONAL]

Itens identificados ao ler o handoff e a arquitetura que não estão cobertos pelas 10 US acima:

- **Tela de Login/Cadastro (tela 07) não tem US própria.** É uma das 7 telas do handoff (celular + OTP, OAuth Google/Apple), mas nenhuma das 10 US pedidas cobre o fluxo de autenticação. Vale considerar uma US dedicada.
- **Modelo de entrega vs. retirada no balcão diverge entre o enunciado e o design.** A tela 06 (Acompanhamento) mostra um fluxo completo de entrega por motoboy (mapa, card do entregador), enquanto a US-08 pede um código "MA-XXXX + QR para apresentar no balcão" — modelo de retirada. Ver `[DECISÃO PENDENTE]` em `US-08-finalizar-e-receber-codigo.md`.
- **Clube Mandaaí** (banner na Home, tela 01, e `User.clube_member` no ERD) sugere uma US de assinatura/upsell — não coberta, e o roadmap (`mandaai-arquitetura.md` seção 13) marca isso como Fase 2.
- **Favoritar restaurante** (ícone ♡ na tela 02 e aba "Favoritos" na tabbar mobile da Home) não tem entidade no ERD nem US correspondente.
- **Histórico de pedidos** (aba "Pedidos" na tabbar mobile; endpoint `GET /api/v1/orders/me` já existe na arquitetura) não tem tela mockada nem US.
- **Gerenciar endereços salvos** (Checkout tela 05 tem "Trocar" endereço; `Address.label` no ERD suporta múltiplos endereços como "Casa"/"Trabalho"; endpoints `POST`/`GET /api/v1/users/me/addresses` já existem) — não há US para cadastrar/editar endereços.
- **Agendar entrega** (opção "Agendar" ao lado de "Padrão" no Checkout, tela 05) aparece no mockup mas não tem US nem detalhamento de comportamento.
