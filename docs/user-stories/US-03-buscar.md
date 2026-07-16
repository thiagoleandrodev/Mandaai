# US-03: Buscar

**Como** cliente, **quero** buscar por nome de restaurante ou prato, **para** localizar rapidamente.

- Referência visual: Home — variante desktop (campo "Buscar pratos, lojas e cozinhas…" na barra de navegação) e mobile (campo "Buscar no Mandaaí…" abaixo do topo, mais o ícone "⌕ Buscar" na tabbar inferior, tela 01).
- Estados envolvidos: campo vazio (placeholder), digitando, resultados encontrados, sem resultado (não mockado, ver US-09).
- Entidades de domínio: `Restaurant.name`, `Restaurant.category`, `Product.name`. Endpoint de apoio: `GET /api/v1/restaurants` com parâmetro `q`, conforme `mandaai-arquitetura.md` seção 7.
- Escopo: entra buscar e retornar restaurantes e/ou pratos correspondentes ao termo digitado. Não entra: a tela de resultados de busca não tem um mockup próprio no handoff — `[DECISÃO PENDENTE]` sobre se os resultados reaproveitam o layout de grid/lista da Home ou têm uma tela dedicada.
