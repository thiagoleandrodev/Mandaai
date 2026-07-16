# US-09: Lidar com estados de erro

**Como** cliente, **quero** ver mensagem clara em restaurante fechado, item esgotado, busca sem resultado ou erro técnico.

- Referência visual: nenhuma das 7 telas do handoff (`Mandaai Telas.dc.html`) mockou estados de vazio/erro — todas mostram o caminho feliz (populado). Esta US se apoia nas telas onde cada estado ocorreria: Restaurante/Cardápio (tela 02, para restaurante fechado / item esgotado) e Home (tela 01, para busca sem resultado).
- Estados envolvidos: restaurante fechado, item esgotado, busca sem resultado, erro técnico (timeout, falha de rede, erro 5xx).
- Entidades de domínio: `Restaurant.open` (boolean), `Product.available` (boolean). Busca sem resultado e erro técnico não têm entidade própria — são estados de interface.
- Escopo: entra definir a mensagem e o comportamento de cada estado (ex: bloquear pedido em restaurante fechado, desabilitar "Adicionar" em item esgotado). `[DECISÃO PENDENTE]`: como nenhum desses estados está desenhado no handoff, o visual (ilustração, copy exata, ação de recuperação) precisa ser definido com o design antes da implementação.
