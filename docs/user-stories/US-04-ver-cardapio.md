# US-04: Ver cardápio

**Como** cliente, **quero** ver o cardápio de um restaurante com seções e fotos, **para** escolher um item.

- Referência visual: Restaurante / Cardápio — variante desktop e mobile (tela 02). Capa do restaurante, card com nome/avaliação/tempo/taxa, abas de seção ("Mais pedidos", "Lanches", "Acompanhamentos", "Bebidas", "Sobremesas") e cards de produto com foto, descrição e preço. No desktop há uma sacola lateral fixa ("Sua sacola"); no mobile, uma barra fixa no rodapé ("Ver sacola · N itens").
- Estados envolvidos: carregando (placeholders), populado (seções com produtos), restaurante fechado (não mockado, ver US-09), produto esgotado (não mockado, ver US-09).
- Entidades de domínio: `Restaurant`, `MenuSection` (name, sort_order), `Product` (name, description, price_brl, image_url, available).
- Escopo: entra a navegação entre seções do cardápio e a visualização de cada produto. Não entra a customização do item em si — isso é tratado em US-05.
