# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Mandaaí** is a hyperlocal delivery marketplace targeting Brazilian SMBs — "comida boa, do seu bairro, na sua porta". The project is currently in the **design/planning phase**: architecture documentation and high-fidelity UI designs exist, but application code has not been implemented yet.

## Current Repository Contents

- `mandaai-arquitetura.md` — Full system architecture spec: database schema (PostgreSQL), API endpoints, folder structure for both frontend and backend, auth flows, and development roadmap.
- `Design hi-fi Mandaaí web/` — High-fidelity design comps as a `.dc.html` Design Component file (7 screens: Home, Restaurant/Menu, Product Detail, Cart, Checkout, Tracking, Login). Includes desktop and mobile variants. `support.js` is the dc-runtime renderer — do not edit.
- `Design hi-fi Mandaaí web/screenshots/` — PNG exports of key screens.

## Planned Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14 (App Router), TypeScript, Tailwind CSS + CSS custom properties, Zustand (cart state), SWR (data fetching), Framer Motion, NextAuth.js |
| Backend | FastAPI (Python 3.12), SQLAlchemy 2 + Alembic, Pydantic v2, Redis, Celery |
| Database | PostgreSQL (Supabase) |
| Payments | Stripe (PCI-DSS via Elements) |
| Auth | OTP via Twilio (primary), OAuth Google/Apple (secondary) |
| Real-time | WebSocket via FastAPI for order tracking, Redis pub/sub |
| Infra | Vercel (frontend), Railway/Render (backend), Cloudflare R2 (images) |

## Design System Tokens

All UI work must follow these tokens (defined in `mandaai-arquitetura.md` section 2):

- **Primary colors**: Coral `#F5402C` (CTAs), Sol `#FFC02E` (premium/clube), Folha `#1FA463` (success/free delivery), Tinta `#2A2320` (text), Creme `#FBF7F2` (page bg)
- **Typography**: Fredoka (display: logo, titles, prices), DM Sans (body: UI text, buttons)
- **Border radius**: Buttons `999px` (pill), Cards `16px`, Inputs `12px`, Category icons `18px`
- **Border color**: `#F0E9E1` (cards), `#E3D9CD` (inputs/counters)

## Architecture Notes

- Frontend and backend are separate codebases (`mandaai-web/` and `mandaai-api/`).
- Backend follows service-repository pattern: `api/v1/` (routers) -> `services/` (business logic) -> `repositories/` (data access).
- Cart state is client-side (Zustand with localStorage persistence), keyed to `mandaai-cart`.
- Order tracking uses WebSocket at `/ws/orders/{order_id}` with Redis pub/sub for status broadcasts.
- All API routes are versioned under `/api/v1/`.
- Order status flow: `pending -> confirmed -> preparing -> delivering -> delivered`.
- Currency is BRL; all money fields use `DECIMAL(10,2)`.

## Language

The product, UI copy, and documentation are in Brazilian Portuguese (pt-BR). Code identifiers (variables, functions, API paths) are in English.
