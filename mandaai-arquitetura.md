# Mandaaí — Arquitetura de Sistema e Guia de Desenvolvimento

> **Versão:** 1.0  
> **Stack:** Next.js 14 · FastAPI · PostgreSQL · Redis · WebSocket  
> **Público-alvo:** Solo founder / pequena equipe — PMEs brasileiras

---

## 1. Visão Geral do Produto

O **Mandaaí** é um marketplace de delivery hiperlocal: conecta consumidores a lojas do bairro com entrega rápida e experiência mobile-first. O diferencial é a proximidade — "comida boa, do seu bairro, na sua porta".

### Telas mapeadas no design system

| # | Tela | Tipo |
|---|------|------|
| 01 | Home / Vitrine | Lista de lojas e categorias |
| 02 | Restaurante / Cardápio | Menu com seções e itens |
| 03 | Detalhe do Produto | Modal com customizações |
| 04 | Carrinho / Sacola | Revisão + cupom + resumo |
| 05 | Checkout | Endereço + pagamento |
| 06 | Acompanhamento | Tracking em tempo real |
| 07 | Login / Cadastro | Auth via celular + OAuth |

---

## 2. Design System — Tokens

### 2.1 Paleta de Cores

```css
:root {
  /* Primárias */
  --color-coral:   #F5402C;  /* CTA principal, destaques */
  --color-sol:     #FFC02E;  /* Clube, badges premium */
  --color-folha:   #1FA463;  /* Entrega grátis, status OK */
  --color-tinta:   #2A2320;  /* Texto base */
  --color-creme:   #FBF7F2;  /* Background páginas */

  /* Superfícies */
  --color-surface: #FFFFFF;
  --color-border:  #F0E9E1;
  --color-muted:   #A89A8A;

  /* Semânticas */
  --color-success: #1FA463;
  --color-warning: #FFC02E;
  --color-danger:  #F5402C;
}
```

### 2.2 Tipografia

| Role | Família | Peso | Uso |
|------|---------|------|-----|
| Display | Fredoka | 600–700 | Logo, títulos, preços |
| Body | DM Sans | 400–600 | Textos, botões, labels |

```html
<!-- Google Fonts import -->
<link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;500;600;700&family=DM+Sans:opsz,wght@9..40,400;9..40,500;9..40,600;9..40,700&display=swap" rel="stylesheet">
```

### 2.3 Componentes Base

```
Botões:
  - Primary:   bg coral, branco, border-radius 999px
  - Secondary: border #E3D9CD, fundo branco
  - Badge:     bg folha (grátis), bg sol (promo)

Cards:
  - Loja:      border #F0E9E1, border-radius 16px, shadow suave
  - Produto:   imagem placeholder colorida (tint por categoria)
  - Categoria: ícone-letra 64×64, border-radius 18px

Inputs:
  - Border 1.5px #E3D9CD, border-radius 12px
  - Focus: border coral

Counters (quantidade):
  - Border #E3D9CD, border-radius 999px, inline
```

---

## 3. Arquitetura de Sistema

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENTES                                  │
│   Browser (Web)              App Mobile (PWA / React Native)    │
└───────────────┬──────────────────────────────┬──────────────────┘
                │ HTTPS / WSS                  │
┌───────────────▼──────────────────────────────▼──────────────────┐
│                    CDN + API Gateway                             │
│            (Cloudflare / AWS CloudFront + API Gateway)          │
└───────────────┬──────────────────────────────┬──────────────────┘
                │                              │
    ┌───────────▼──────────┐      ┌────────────▼──────────────┐
    │   Frontend (SSR)      │      │    WebSocket Server        │
    │   Next.js 14 (App)   │      │    (tracking tempo real)   │
    │   Vercel / ECS        │      │    Node.js / Fastify       │
    └──────────────────────┘      └────────────▲──────────────┘
                                               │ Pub/Sub
┌──────────────────────────────────────────────┼──────────────────┐
│                  Backend API (REST)           │                  │
│                  FastAPI (Python)             │  Redis           │
│                  Uvicorn + Gunicorn           │  (cache, sessão, │
│                  AWS ECS / Railway            │   pub/sub)       │
└──────────────────────────────────────────────┴──────────────────┘
                │                 │                │
    ┌───────────▼──┐   ┌──────────▼──┐  ┌─────────▼──────────┐
    │  PostgreSQL   │   │   S3 /      │  │  Serviços Externos  │
    │  (Supabase)   │   │   R2 imgs   │  │  ・Stripe (pagto)  │
    │               │   │             │  │  ・Twilio / Zapi   │
    │               │   │             │  │  ・Maps API        │
    │               │   │             │  │  ・Firebase Auth   │
    └───────────────┘   └─────────────┘  └────────────────────┘
```

---

## 4. Estrutura de Pastas

### 4.1 Frontend — Next.js 14 (App Router)

```
mandaai-web/
├── app/
│   ├── (auth)/
│   │   └── login/
│   │       └── page.tsx              # Tela 07 — Login/Cadastro
│   ├── (main)/
│   │   ├── layout.tsx                # Navbar + Sacola flutuante
│   │   ├── page.tsx                  # Tela 01 — Home/Vitrine
│   │   ├── loja/
│   │   │   └── [slug]/
│   │   │       └── page.tsx          # Tela 02 — Cardápio
│   │   ├── sacola/
│   │   │   └── page.tsx              # Tela 04 — Carrinho
│   │   ├── checkout/
│   │   │   └── page.tsx              # Tela 05 — Checkout
│   │   └── pedido/
│   │       └── [id]/
│   │           └── page.tsx          # Tela 06 — Tracking
│   ├── globals.css
│   └── layout.tsx
│
├── components/
│   ├── ui/                           # Átomos — design system
│   │   ├── Button.tsx
│   │   ├── Badge.tsx
│   │   ├── Input.tsx
│   │   └── Counter.tsx
│   ├── cards/                        # Moléculas
│   │   ├── RestaurantCard.tsx
│   │   ├── ProductCard.tsx
│   │   └── CategoryChip.tsx
│   ├── layout/
│   │   ├── Navbar.tsx
│   │   └── CartDrawer.tsx
│   └── modals/
│       └── ProductModal.tsx          # Tela 03 — Detalhe Produto
│
├── hooks/
│   ├── useCart.ts                    # Estado do carrinho (Zustand)
│   ├── useOrderTracking.ts           # WebSocket hook
│   └── useGeolocation.ts            # Endereço do usuário
│
├── lib/
│   ├── api.ts                        # Axios client + interceptors
│   ├── auth.ts                       # NextAuth config
│   └── constants.ts                  # Cores, endpoints
│
├── store/
│   └── cartStore.ts                  # Zustand — sacola global
│
├── types/
│   ├── restaurant.ts
│   ├── product.ts
│   ├── order.ts
│   └── user.ts
│
├── styles/
│   └── tokens.css                    # CSS Custom Properties (design tokens)
│
└── public/
    └── fonts/                        # Fredoka + DM Sans (self-hosted)
```

### 4.2 Backend — FastAPI (Python)

```
mandaai-api/
├── app/
│   ├── main.py                       # Entry point FastAPI
│   ├── config.py                     # Settings (pydantic-settings)
│   │
│   ├── api/
│   │   └── v1/
│   │       ├── router.py             # Agrega todos os routers
│   │       ├── auth.py               # POST /auth/otp, /auth/verify, /auth/oauth
│   │       ├── restaurants.py        # GET /restaurants, GET /restaurants/{slug}
│   │       ├── products.py           # GET /restaurants/{id}/products
│   │       ├── orders.py             # POST /orders, GET /orders/{id}
│   │       ├── cart.py               # POST /cart, PATCH /cart/items
│   │       ├── checkout.py           # POST /checkout
│   │       └── tracking.py           # WS /ws/orders/{id}
│   │
│   ├── models/                       # SQLAlchemy ORM
│   │   ├── user.py
│   │   ├── restaurant.py
│   │   ├── product.py
│   │   ├── order.py
│   │   └── coupon.py
│   │
│   ├── schemas/                      # Pydantic (request/response)
│   │   ├── restaurant.py
│   │   ├── product.py
│   │   ├── order.py
│   │   └── user.py
│   │
│   ├── services/                     # Regras de negócio
│   │   ├── auth_service.py           # OTP via Twilio, OAuth Google/Apple
│   │   ├── order_service.py          # Criação, validação, status
│   │   ├── payment_service.py        # Stripe integration
│   │   ├── delivery_service.py       # Cálculo de frete, tempo estimado
│   │   └── notification_service.py   # Push / WhatsApp updates
│   │
│   ├── repositories/                 # Data Access Layer
│   │   ├── restaurant_repo.py
│   │   ├── order_repo.py
│   │   └── user_repo.py
│   │
│   ├── core/
│   │   ├── database.py               # SQLAlchemy engine + session
│   │   ├── redis.py                  # Redis client
│   │   ├── security.py               # JWT, bcrypt
│   │   └── exceptions.py            # HTTP exceptions customizadas
│   │
│   └── workers/                      # Background tasks (Celery)
│       ├── celery.py
│       ├── order_worker.py           # Atualiza status do pedido
│       └── notification_worker.py    # Notificações assíncronas
│
├── alembic/                          # Migrations
├── tests/
├── Dockerfile
├── docker-compose.yml
└── requirements.txt
```

---

## 5. Modelagem do Banco de Dados

```sql
-- Usuários
CREATE TABLE users (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  phone       VARCHAR(20) UNIQUE NOT NULL,
  name        VARCHAR(100),
  email       VARCHAR(200),
  avatar_url  TEXT,
  clube_member BOOLEAN DEFAULT FALSE,
  created_at  TIMESTAMPTZ DEFAULT NOW()
);

-- Endereços
CREATE TABLE addresses (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id     UUID REFERENCES users(id) ON DELETE CASCADE,
  label       VARCHAR(50),          -- "Casa", "Trabalho"
  street      TEXT NOT NULL,
  number      VARCHAR(10),
  complement  VARCHAR(100),
  neighborhood VARCHAR(100),
  city        VARCHAR(100),
  lat         DECIMAL(9,6),
  lng         DECIMAL(9,6)
);

-- Restaurantes
CREATE TABLE restaurants (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  slug        VARCHAR(100) UNIQUE NOT NULL,
  name        VARCHAR(150) NOT NULL,
  category    VARCHAR(50),
  cover_url   TEXT,
  logo_url    TEXT,
  rating      DECIMAL(2,1),
  delivery_min INT,                  -- minutos
  delivery_max INT,
  fee_brl     DECIMAL(10,2),         -- NULL = grátis
  open        BOOLEAN DEFAULT TRUE,
  lat         DECIMAL(9,6),
  lng         DECIMAL(9,6)
);

-- Categorias do cardápio
CREATE TABLE menu_sections (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  restaurant_id UUID REFERENCES restaurants(id) ON DELETE CASCADE,
  name          VARCHAR(100) NOT NULL,
  sort_order    INT DEFAULT 0
);

-- Produtos
CREATE TABLE products (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  restaurant_id UUID REFERENCES restaurants(id) ON DELETE CASCADE,
  section_id    UUID REFERENCES menu_sections(id),
  name          VARCHAR(150) NOT NULL,
  description   TEXT,
  price_brl     DECIMAL(10,2) NOT NULL,
  image_url     TEXT,
  available     BOOLEAN DEFAULT TRUE
);

-- Customizações (grupos)
CREATE TABLE option_groups (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  product_id  UUID REFERENCES products(id) ON DELETE CASCADE,
  label       VARCHAR(100) NOT NULL,  -- "Ponto da carne"
  required    BOOLEAN DEFAULT FALSE,
  max_select  INT DEFAULT 1
);

-- Opções
CREATE TABLE options (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  group_id        UUID REFERENCES option_groups(id) ON DELETE CASCADE,
  label           VARCHAR(100) NOT NULL,
  extra_price_brl DECIMAL(10,2) DEFAULT 0
);

-- Pedidos
CREATE TABLE orders (
  id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id         UUID REFERENCES users(id),
  restaurant_id   UUID REFERENCES restaurants(id),
  address_id      UUID REFERENCES addresses(id),
  status          VARCHAR(30) DEFAULT 'pending',
  -- pending → confirmed → preparing → delivering → delivered
  subtotal_brl    DECIMAL(10,2),
  fee_brl         DECIMAL(10,2),
  discount_brl    DECIMAL(10,2) DEFAULT 0,
  total_brl       DECIMAL(10,2),
  coupon_code     VARCHAR(30),
  payment_method  VARCHAR(30),
  payment_id      TEXT,              -- Stripe payment intent ID
  created_at      TIMESTAMPTZ DEFAULT NOW(),
  updated_at      TIMESTAMPTZ DEFAULT NOW()
);

-- Itens do pedido
CREATE TABLE order_items (
  id          UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  order_id    UUID REFERENCES orders(id) ON DELETE CASCADE,
  product_id  UUID REFERENCES products(id),
  name        VARCHAR(150),          -- snapshot no momento do pedido
  qty         INT NOT NULL,
  unit_price  DECIMAL(10,2),
  note        TEXT                   -- "Sem cebola"
);

-- Cupons
CREATE TABLE coupons (
  id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  code          VARCHAR(30) UNIQUE NOT NULL,
  discount_type VARCHAR(10),         -- 'flat' | 'percent'
  value         DECIMAL(10,2),
  min_order_brl DECIMAL(10,2),
  uses_max      INT,
  uses_current  INT DEFAULT 0,
  expires_at    TIMESTAMPTZ
);
```

---

## 6. Fluxo das Telas e Navegação

```
[Login / Cadastro]
      │
      ▼ (autenticado)
[Home / Vitrine] ──────────────────────────────┐
  │                                            │
  │ seleciona categoria                        │ banner "Clube"
  ▼                                            ▼
[Restaurante / Cardápio]               [Clube Mandaaí]
  │
  │ clica produto
  ▼
[Modal — Detalhe do Produto]
  │
  │ "Adicionar" → atualiza Sacola (Zustand)
  ▼
[Carrinho / Sacola]  ◄─── Navbar badge (qtd itens)
  │
  │ "Ir para o checkout"
  ▼
[Checkout]
  ├── Endereço de entrega
  ├── Método de pagamento (Stripe)
  └── "Fazer pedido"
        │
        ▼ (POST /orders → 201)
[Acompanhamento em Tempo Real]
  ├── WebSocket /ws/orders/{id}
  ├── Timeline de status
  └── Card do entregador
```

---

## 7. API — Endpoints Principais

### Autenticação

```
POST   /api/v1/auth/otp              Envia código SMS
POST   /api/v1/auth/verify           Valida OTP → retorna JWT
POST   /api/v1/auth/oauth/google     OAuth via Google
POST   /api/v1/auth/oauth/apple      OAuth via Apple
POST   /api/v1/auth/refresh          Refresh token
```

### Vitrine / Restaurantes

```
GET    /api/v1/restaurants           Lista (filtros: lat, lng, categoria, q)
GET    /api/v1/restaurants/{slug}    Detalhe + info de entrega
GET    /api/v1/restaurants/{id}/menu Seções + produtos
GET    /api/v1/categories            Lista de categorias
```

### Pedidos

```
POST   /api/v1/orders                Cria pedido
GET    /api/v1/orders/{id}           Status do pedido
GET    /api/v1/orders/me             Histórico do usuário
WS     /ws/orders/{id}               Stream de updates (tracking)
```

### Checkout / Pagamento

```
POST   /api/v1/checkout/intent       Cria Stripe PaymentIntent
POST   /api/v1/checkout/confirm      Confirma pagamento → cria pedido
POST   /api/v1/coupons/validate      Valida cupom + retorna desconto
```

### Usuário

```
GET    /api/v1/users/me              Perfil
PATCH  /api/v1/users/me              Atualiza nome, avatar
POST   /api/v1/users/me/addresses    Adiciona endereço
GET    /api/v1/users/me/addresses    Lista endereços
```

---

## 8. Tracking em Tempo Real (WebSocket)

```python
# backend: app/api/v1/tracking.py
@router.websocket("/ws/orders/{order_id}")
async def order_tracking(websocket: WebSocket, order_id: str):
    await manager.connect(websocket, room=order_id)
    try:
        while True:
            # Aguarda mensagens do cliente (heartbeat)
            await websocket.receive_text()
    except WebSocketDisconnect:
        manager.disconnect(websocket, room=order_id)
```

```typescript
// frontend: hooks/useOrderTracking.ts
export function useOrderTracking(orderId: string) {
  const [status, setStatus] = useState<OrderStatus | null>(null);

  useEffect(() => {
    const ws = new WebSocket(`${WS_URL}/ws/orders/${orderId}`);
    ws.onmessage = (e) => setStatus(JSON.parse(e.data));
    return () => ws.close();
  }, [orderId]);

  return status;
}
```

**Payload de status:**
```json
{
  "order_id": "uuid",
  "status": "delivering",
  "label": "Saiu para entrega",
  "eta_minutes": 15,
  "driver": {
    "name": "Carlos",
    "vehicle": "Honda CG",
    "plate": "ABC-1D23",
    "lat": -19.927, "lng": -43.942
  }
}
```

---

## 9. Gerenciamento de Estado (Frontend)

```typescript
// store/cartStore.ts  (Zustand)
interface CartStore {
  restaurantId: string | null;
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (itemId: string) => void;
  updateQty: (itemId: string, qty: number) => void;
  clearCart: () => void;
  total: () => number;
}

export const useCartStore = create<CartStore>()(
  persist(
    (set, get) => ({
      restaurantId: null,
      items: [],
      addItem: (item) => set((s) => ({
        restaurantId: item.restaurantId,
        items: [...s.items, item]
      })),
      // ...
      total: () => get().items.reduce((acc, i) => acc + i.price * i.qty, 0),
    }),
    { name: 'mandaai-cart' }
  )
);
```

---

## 10. Autenticação

### Fluxo OTP (principal — mobile-first)

```
1. Usuário digita celular (+55 11 9...)
2. POST /auth/otp  → Twilio envia SMS com código 6 dígitos
3. POST /auth/verify { phone, code }
   ├── Código correto → JWT (access 1h) + Refresh token (30d)
   └── Usuário novo → cria registro automaticamente
```

### OAuth (secundário — Google/Apple)

```
Frontend → NextAuth → Google/Apple → Callback
→ POST /auth/oauth/google { id_token }
→ Backend valida token → retorna JWT
```

---

## 11. Segurança e Boas Práticas

| Camada | Medida |
|--------|--------|
| Auth | JWT com rotação de refresh token |
| API | Rate limiting por IP (Redis sliding window) |
| Dados | Parametrized queries — sem SQL injection |
| Uploads | Validação de tipo/tamanho, scan de vírus (S3) |
| Pagamentos | PCI-DSS via Stripe Elements (dados não chegam ao backend) |
| LGPD | Consentimento explícito, exportação e exclusão de dados |

---

## 12. Stack Completa — Resumo

### Frontend
| Tecnologia | Uso |
|-----------|-----|
| Next.js 14 (App Router) | SSR + rotas |
| TypeScript | Tipagem |
| Tailwind CSS + CSS Variables | Estilo + design tokens |
| Zustand | Estado global (carrinho) |
| SWR | Data fetching + cache |
| Framer Motion | Animações (modal produto, transições) |
| NextAuth.js | Autenticação |

### Backend
| Tecnologia | Uso |
|-----------|-----|
| FastAPI (Python 3.12) | API REST + WebSocket |
| SQLAlchemy 2 + Alembic | ORM + migrations |
| Pydantic v2 | Validação de dados |
| Redis | Cache, sessões, pub/sub |
| Celery + Redis | Workers assíncronos |

### Infraestrutura
| Tecnologia | Uso |
|-----------|-----|
| PostgreSQL (Supabase) | Banco principal |
| Cloudflare R2 | Armazenamento de imagens |
| Stripe | Pagamentos |
| Twilio | SMS OTP |
| Google Maps API | Geolocalização e roteamento |
| Railway / Render | Deploy backend (MVP) |
| Vercel | Deploy frontend |

---

## 13. Roadmap de Desenvolvimento

### Fase 1 — MVP (semanas 1–6)
- [ ] Setup do projeto (Next.js + FastAPI + Supabase)
- [ ] Design tokens CSS + componentes base
- [ ] Auth via OTP (Twilio)
- [ ] Home com lista de restaurantes
- [ ] Cardápio + modal de produto
- [ ] Carrinho (Zustand)
- [ ] Checkout básico (Stripe)
- [ ] Tela de acompanhamento (polling, sem WS)

### Fase 2 — Produto (semanas 7–12)
- [ ] WebSocket para tracking real-time
- [ ] Geolocalização + filtro por bairro
- [ ] Clube Mandaaí (subscription Stripe)
- [ ] Cupons e promoções
- [ ] Push notifications (PWA)
- [ ] Painel do lojista (admin básico)

### Fase 3 — Escala (semanas 13+)
- [ ] App React Native (Expo)
- [ ] Sistema de avaliações
- [ ] Histórico de pedidos
- [ ] Analytics (Mixpanel / PostHog)
- [ ] Multi-cidade

---

## 14. Configuração do Ambiente Local

```bash
# Clone
git clone https://github.com/seu-user/mandaai-web
git clone https://github.com/seu-user/mandaai-api

# Frontend
cd mandaai-web
npm install
cp .env.example .env.local
npm run dev                    # http://localhost:3000

# Backend
cd mandaai-api
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
docker-compose up -d           # PostgreSQL + Redis locais
uvicorn app.main:app --reload  # http://localhost:8000
alembic upgrade head           # Roda migrations
```

### Variáveis de ambiente (`.env.local`)

```env
# Frontend
NEXT_PUBLIC_API_URL=http://localhost:8000
NEXT_PUBLIC_WS_URL=ws://localhost:8000
NEXTAUTH_SECRET=your-secret
NEXTAUTH_URL=http://localhost:3000
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
NEXT_PUBLIC_STRIPE_KEY=pk_test_...
NEXT_PUBLIC_MAPS_KEY=

# Backend (.env)
DATABASE_URL=postgresql://user:pass@localhost:5432/mandaai
REDIS_URL=redis://localhost:6379
JWT_SECRET=your-jwt-secret
STRIPE_SECRET_KEY=sk_test_...
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_PHONE=+1...
```

---

*Documento gerado com base no design system Mandaaí — Identidade Visual + 7 telas (Desktop e Mobile).*
