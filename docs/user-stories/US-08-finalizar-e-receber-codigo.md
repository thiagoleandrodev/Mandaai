# US-08: Finalizar e receber código

**Como** cliente, **quero** confirmar o pedido e receber um código MA-XXXX + QR, **para** apresentar no balcão.

- Referência visual: Checkout — desktop e mobile (tela 05), botão "Confirmar pedido" com o total; Acompanhamento do pedido — desktop e mobile (tela 06), que exibe "Pedido #4821 · Tonho Lanches" no topo.
- Estados envolvidos: pedido sendo confirmado (loading no botão), pedido confirmado (código gerado), falha no pagamento (não mockado, ver US-09).
- Entidades de domínio: `Order` (code, status, payment_method, payment_id, subtotal_brl, delivery_fee_brl, service_fee_brl, discount_brl, total_brl), `OrderItem`, `OrderItemOption`.
- Escopo: entra a confirmação do pedido e a exibição de um código de identificação para o cliente. `[DECISÃO PENDENTE]`: há uma divergência entre o pedido desta US e o handoff visual. O ERD (`docs/erd.md`) define `Order.code` no formato sequencial legível "#4821" (não "MA-XXXX"), e a tela 06 de Acompanhamento mostra um fluxo de **entrega por motoboy** (mapa, timeline de status, card do entregador "Carlos") — não um fluxo de **retirada no balcão** com QR code, que não aparece em nenhuma das 7 telas do handoff. Antes de implementar, confirmar com o design/produto: (a) o formato final do código (`#4821` vs `MA-XXXX`), (b) se o MVP é só entrega (conforme tela 06) ou se também haverá retirada no balcão com QR (não coberta pelo handoff atual).
