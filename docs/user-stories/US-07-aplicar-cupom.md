# US-07: Aplicar cupom

**Como** cliente, **quero** usar um código de cupom, **para** receber desconto. (opcional no MVP)

- Referência visual: Carrinho / Sacola — variante desktop e mobile (tela 04). Banner amarelo "Cupom BAIRRO10 aplicado · −R$ 10,00 no seu pedido" com opção "Remover" (desktop); resumo de valores mostra a linha "Cupom" com o desconto.
- Estados envolvidos: sem cupom (estado padrão), cupom aplicado (banner + desconto refletido no resumo), cupom inválido/expirado (não mockado, ver US-09), cupom removido.
- Entidades de domínio: `Coupon` (code, discount_type, value, min_order_brl, uses_max, uses_current, expires_at), `Order.coupon_code` (referência textual, sem FK — cupons podem expirar sem invalidar pedidos já feitos) e `Order.discount_brl`.
- Escopo: entra aplicar e remover um cupom antes do checkout. `[DECISÃO PENDENTE]`: o mockup só mostra o estado "cupom já aplicado" — não há campo de input para digitar um novo código; assumir um campo de texto + botão "Aplicar" antes do banner de sucesso. Endpoint de apoio: `POST /api/v1/coupons/validate` (`mandaai-arquitetura.md` seção 7). Conforme o roadmap (seção 13), cupons e promoções são Fase 2 — por isso esta US é opcional no MVP.
