# US-05: Customizar item

**Como** cliente, **quero** escolher acompanhamentos, adicionais e adicionar observações ao pedir, **para** personalizar.

- Referência visual: Detalhe do Produto — modal desktop (sobre o cardápio) e tela mobile (tela 03). Mostra grupo obrigatório de opção única ("Ponto da carne" — radio, badge "Obrigatório") e grupo opcional de múltipla escolha ("Adicionais" — checkbox com preço extra, ex: "Bacon extra +R$ 5,00"), além do contador de quantidade e botão "Adicionar" com o preço total do item.
- Estados envolvidos: nenhuma opção obrigatória selecionada (bloqueia "Adicionar" — não mockado explicitamente), opções selecionadas (preço recalculado), quantidade ajustada.
- Entidades de domínio: `Product`, `OptionGroup` (label, required, max_select), `Option` (label, extra_price_brl), e no momento do pedido: `OrderItem.note` (observação livre, ex: "Sem cebola").
- Escopo: entra seleção de grupos de opção (obrigatórios e opcionais) e ajuste de quantidade. `[DECISÃO PENDENTE]`: o mockup da tela 03 não exibe um campo de texto livre para observação, embora o resumo da Sacola (tela 04) mostre notas como "Sem cebola, ponto da carne ao ponto" e o ERD tenha `OrderItem.note` — assumir que existe um campo de observação abaixo dos adicionais, a confirmar com design.
