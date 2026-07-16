# ADR-0003: Next.js 15 App Router no frontend

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

O frontend do Mandaaí precisa renderizar as 7 telas do design hi-fi (Home,
Restaurante/Cardápio, Detalhe do Produto, Sacola, Checkout, Rastreio, Login) com
boa performance de carregamento de imagens (cards de loja e produto) e um caminho
de deploy simples para um projeto solo/mentoria. A plataforma de deploy escolhida
para o MVP é Vercel-native, o que favorece frameworks que ela suporta como
first-class citizen.

## Decisão

Decidimos usar **Next.js 15 com App Router**, aproveitando `next/image` para
otimização automática de imagens (covers de restaurante, fotos de produto) e
**Server Components por padrão**, reservando Client Components apenas para partes
interativas (ex: contador de quantidade, sacola, formulário de checkout).

## Consequências

- Facilita: deploy em Vercel sem configuração adicional — build, CDN de assets e
  otimização de imagem funcionam out-of-the-box.
- Facilita: Server Components por padrão reduzem o JavaScript enviado ao cliente
  nas telas majoritariamente de leitura (Home, Cardápio), melhorando o tempo de
  carregamento inicial em conexões móveis.
- Facilita: `next/image` resolve responsividade e lazy loading das imagens de
  produto/restaurante sem código customizado.
- Dificulta: exige entender a distinção Server/Client Component, que é um conceito
  novo para quem vem de SPA tradicional — parte do custo de aprendizado aceito
  como parte do valor didático do projeto.
- Compromete a manter: App Router ainda evolui rápido entre versões do Next.js;
  atualizações futuras podem exigir ajustes de convenções de roteamento.

## Alternativas consideradas

- **Next.js 14**: descartado em favor da versão mais recente disponível (15) para
  não começar o projeto já uma versão atrás, evitando retrabalho de migração
  próximo ao lançamento.
- **Remix**: descartado porque não tem integração nativa de primeira classe com a
  plataforma de deploy escolhida (Vercel) no mesmo nível que o Next.js, e teria
  curva de aprendizado própria (loaders/actions) sem ganho claro para este escopo.
- **Pages Router (Next.js legado)**: descartado porque é o modelo antigo sendo
  substituído pelo App Router — não faz sentido ensinar o padrão em descontinuação
  para um projeto novo.
- **SPA pura (Vite + React Router)**: descartado por perder SSR/SEO e otimização
  de imagem nativa, relevantes para um marketplace onde páginas de restaurante
  podem ser indexadas por busca.
