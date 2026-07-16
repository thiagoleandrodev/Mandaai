# Mandaaí — Entity Relationship Diagram

```mermaid
erDiagram

    %% ── Usuários e endereços ──────────────────────────────

    User {
        uuid id PK
        varchar phone "UNIQUE NOT NULL — login principal"
        varchar name
        varchar email
        text avatar_url
        boolean clube_member "assinante Clube Mandaaí"
        timestamptz created_at
    }

    Address {
        uuid id PK
        uuid user_id FK
        varchar label "Casa, Trabalho…"
        text street
        varchar number
        varchar complement
        varchar neighborhood
        varchar city
        decimal lat
        decimal lng
    }

    User ||--o{ Address : "possui"

    %% ── Catálogo ──────────────────────────────────────────

    Restaurant {
        uuid id PK
        varchar slug "UNIQUE — URL amigável"
        varchar name
        varchar category
        text cover_url
        text logo_url
        decimal rating
        int delivery_min "minutos"
        int delivery_max "minutos"
        decimal fee_brl "NULL = grátis"
        boolean open
        decimal lat
        decimal lng
    }

    MenuSection {
        uuid id PK
        uuid restaurant_id FK
        varchar name
        int sort_order
    }

    Product {
        uuid id PK
        uuid restaurant_id FK
        uuid section_id FK
        varchar name
        text description
        decimal price_brl
        text image_url
        boolean available
    }

    OptionGroup {
        uuid id PK
        uuid product_id FK
        varchar label "ex: Ponto da carne"
        boolean required
        int max_select
    }

    Option {
        uuid id PK
        uuid group_id FK
        varchar label "ex: Ao ponto"
        decimal extra_price_brl
    }

    Restaurant ||--o{ MenuSection : "organiza"
    MenuSection ||--o{ Product : "contém"
    Restaurant ||--o{ Product : "vende"
    Product ||--o{ OptionGroup : "define"
    OptionGroup ||--o{ Option : "oferece"

    %% ── Pedidos ───────────────────────────────────────────

    Order {
        uuid id PK
        varchar code "código legível ex: #4821"
        uuid user_id FK
        uuid restaurant_id FK
        uuid address_id FK
        varchar status "pending|confirmed|preparing|delivering|delivered"
        decimal subtotal_brl
        decimal delivery_fee_brl
        decimal service_fee_brl "taxa de serviço (tela Sacola)"
        decimal discount_brl
        decimal total_brl
        varchar coupon_code "ref textual ao cupom aplicado"
        varchar payment_method "pix|credit_card|cash"
        text payment_id "Stripe PaymentIntent ID"
        timestamptz created_at
        timestamptz updated_at
    }

    OrderItem {
        uuid id PK
        uuid order_id FK
        uuid product_id FK
        varchar name "snapshot — nome do produto na hora do pedido"
        int qty
        decimal unit_price "snapshot — preço unitário na hora do pedido"
        text note "observação livre: Sem cebola"
    }

    OrderItemOption {
        uuid id PK
        uuid order_item_id FK
        uuid option_id FK
        varchar label "snapshot — ex: Bacon extra"
        decimal extra_price_brl "snapshot — preço do adicional"
    }

    User ||--o{ Order : "realiza"
    Restaurant ||--o{ Order : "recebe"
    Address ||--o{ Order : "destino"
    Order ||--o{ OrderItem : "contém"
    OrderItem ||--o{ OrderItemOption : "personaliza"
    Product ||--o{ OrderItem : "origina"
    Option ||--o{ OrderItemOption : "referencia"

    %% ── Cupons ────────────────────────────────────────────

    Coupon {
        uuid id PK
        varchar code "UNIQUE — ex: BAIRRO10"
        varchar discount_type "flat | percent"
        decimal value
        decimal min_order_brl
        int uses_max
        int uses_current
        timestamptz expires_at
    }
```

## Decisões de modelagem

### Snapshots em OrderItem e OrderItemOption

`OrderItem.name`, `OrderItem.unit_price`, `OrderItemOption.label` e `OrderItemOption.extra_price_brl`
são **cópias congeladas** no instante em que o pedido é criado. O restaurante pode alterar
preços ou renomear pratos a qualquer momento — o pedido precisa refletir o que o cliente
realmente pagou. As FKs `product_id` e `option_id` continuam existindo para rastreabilidade,
mas o sistema nunca deve usar o preço vivo do produto para exibir um pedido passado.

### OrderItemOption (junction table)

A tela 03 (Detalhe do Produto) mostra seleções de opções — "Ao ponto" (obrigatório),
"Bacon extra +R$ 5,00" (opcional) — que afetam o preço final do item. Serializar isso
no campo `note` impediria cálculo de preço e auditoria. A junction table
`OrderItemOption` captura cada opção escolhida com seu preço snapshot.

### `code` em Order

A tela 06 (Tracking) exibe "Pedido #4821" — um identificador sequencial legível para o
cliente e o restaurante. UUIDs são péssimos para comunicação verbal ("qual o número do
pedido?"), então `code` é um campo separado, gerado no momento da criação do pedido.

### `service_fee_brl` em Order

O resumo da sacola (tela 04) mostra "Taxa de serviço R$ 1,99" como linha distinta da
"Taxa de entrega". O schema original tinha apenas `fee_brl` — aqui separamos em
`delivery_fee_brl` e `service_fee_brl` para que o breakdown do pagamento corresponda
exatamente ao que o cliente vê.

### Coupon → Order (sem FK rígida)

`Order.coupon_code` é uma referência textual, não uma FK. Cupons podem expirar ou ser
removidos sem invalidar pedidos históricos. O desconto já está materializado em
`discount_brl`.

### Entidade Driver (Phase 2)

A tela de tracking mostra um card do entregador (nome, veículo, placa, coordenadas).
Essa entidade não está no ERD do MVP — será adicionada na Phase 2 junto com o
WebSocket de tracking em tempo real e o painel do lojista.
