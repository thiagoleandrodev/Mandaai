# US-06: Gerenciar sacola

**Como** cliente, **quero** ajustar quantidade, remover itens e ver o total, antes de finalizar.

- Referência visual: Carrinho / Sacola — variante desktop e mobile (tela 04). Lista de itens com contador de quantidade (−/+), link "Editar" por item, atalho "+ Adicionar mais itens", e painel de resumo com subtotal, taxa de entrega, taxa de serviço, desconto de cupom e total.
- Estados envolvidos: sacola com itens (populado), sacola vazia (não mockado, ver US-09), quantidade alterada (recalcula total), item removido.
- Entidades de domínio: carrinho é estado client-side (`useCartStore`, Zustand, persistido em `localStorage` sob a chave `mandaai-cart`, conforme `mandaai-arquitetura.md` seção 9) — cada item referencia `Product`. Ao confirmar o pedido, os itens da sacola viram `OrderItem` e `OrderItemOption`; os totais correspondem a `Order.subtotal_brl`, `Order.delivery_fee_brl`, `Order.service_fee_brl`, `Order.discount_brl`, `Order.total_brl`.
- Escopo: entra ajustar quantidade e remover item da sacola antes do checkout. `[DECISÃO PENDENTE]`: o mockup não tem um botão de remoção explícito (ex: ícone de lixeira) — o comportamento provável é reduzir a quantidade até zero, a confirmar com design.
