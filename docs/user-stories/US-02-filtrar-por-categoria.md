# US-02: Filtrar por categoria

**Como** cliente, **quero** filtrar restaurantes por tipo de cozinha, **para** encontrar o que estou afim.

- Referência visual: Home — variante desktop e mobile (tela 01). Fileira de chips de categoria logo abaixo do banner hero (ícone-letra 64×64 no desktop, 52×52 no mobile) — ex: Lanches, Pizza, Japonesa, Brasileira, Açaí, Doces, Saudável, Bebidas, Pastel.
- Estados envolvidos: nenhuma categoria selecionada (todas as lojas), categoria selecionada (grid filtrado), sem resultado para a categoria (não mockado, ver US-09).
- Entidades de domínio: `Restaurant.category`. Endpoint de apoio: `GET /api/v1/categories` e `GET /api/v1/restaurants` (filtro `categoria`), conforme `mandaai-arquitetura.md` seção 7.
- Escopo: entra selecionar uma categoria por vez e ver a lista de restaurantes atualizada. Não entra multi-seleção de categorias — o mockup mostra chips simples, sem indicação visual de seleção múltipla.
