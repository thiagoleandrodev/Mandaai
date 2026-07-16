# US-01: Descobrir restaurantes

**Como** cliente, **quero** ver restaurantes próximos na Home, **para** escolher onde pedir.

- Referência visual: Home — variante desktop e mobile (tela 01). Seção "Perto de você" (grid 3 colunas no desktop, lista vertical no mobile), com seletor de endereço "Entregar em ▾" no topo.
- Estados envolvidos: carregando (placeholders `hint-placeholder-count`), populado (grid/lista de lojas), vazio (nenhuma loja na região — não mockado, ver US-09).
- Entidades de domínio: `Restaurant` (name, category, cover_url, rating, delivery_min, delivery_max, fee_brl, open, lat, lng), `Address` (usada para o "Entregar em").
- Escopo: entra a listagem de restaurantes próximos com nome, categoria, avaliação, tempo e taxa de entrega. Não entra: cálculo real de distância/geolocalização (Fase 2, conforme roadmap), nem o banner "Clube Mandaaí" (ver `[SUGESTÃO ADICIONAL]` no README).
