# ADR-0007: Documentação em `docs/` (ERD, ADR, User Stories)

- **Status:** Accepted
- **Data:** 2026-07-01

## Contexto

O projeto já acumula mais de um tipo de artefato de documentação com propósitos
distintos: modelo de dados (`docs/erd.md` + `docs/erd.mmd`), decisões
arquiteturais (este diretório de ADRs) e, potencialmente, user stories derivadas
do design hi-fi. Sem um lugar padronizado, esses artefatos tendem a se espalhar
entre comentários de código, descrições de PR ou documentos soltos na raiz do
repositório — dificultando que alguém entrando no projeto depois encontre o
"porquê" de uma decisão já tomada.

## Decisão

Decidimos centralizar toda a documentação de processo e arquitetura do projeto
em `docs/`, com sub-áreas por tipo de artefato: `docs/erd.md`/`docs/erd.mmd` para
o modelo de dados, `docs/adr/` para decisões arquiteturais (formato Michael
Nygard, numeração de 4 dígitos) e um diretório equivalente para user stories
quando forem geradas a partir do design hi-fi. Cada tipo de documento vive em
Markdown versionado junto com o código, no mesmo repositório e no mesmo fluxo de
PR do código que ele documenta.

## Consequências

- Facilita: qualquer pessoa (incluindo quem está aprendendo) sabe onde procurar
  o "porquê" de uma decisão sem precisar perguntar ou vasculhar histórico de
  commits.
- Facilita: documentação versionada junto com o código — mudanças de arquitetura
  podem incluir um novo ADR no mesmo PR que implementa a mudança.
- Facilita: `docs/adr/README.md` com índice permite navegação rápida por todas as
  decisões já tomadas, sem precisar abrir cada arquivo.
- Dificulta: exige disciplina de criar um novo ADR (ou atualizar o ERD/US) sempre
  que uma decisão relevante for tomada — sem isso, a documentação fica
  desatualizada em relação ao código.
- Compromete a manter: mais um tipo de arquivo para manter sincronizado conforme
  o projeto evolui, mas o custo é baixo comparado ao ganho de rastreabilidade.

## Alternativas consideradas

- **Documentação inline no código (comentários/docstrings)**: descartado porque
  decisões arquiteturais (ex: por que Fastify sem DI, por que sem autenticação no
  MVP) não têm um único ponto no código onde inserir o comentário — são decisões
  que atravessam múltiplos arquivos e camadas.
- **Wiki externo (ex: Notion, Confluence)**: descartado porque desacopla a
  documentação do controle de versão do código — decisões podem divergir do
  estado real do repositório sem que ninguém perceba, e exige uma ferramenta
  externa ao invés de git para histórico e revisão via PR.
- **Documento único monolítico (como `mandaai-arquitetura.md`)**: descartado como
  padrão daqui para frente porque um único arquivo grande mistura visão geral,
  decisões pontuais e estrutura de pastas — dificulta encontrar uma decisão
  específica e revisar mudanças pequenas via diff de PR. `docs/adr/` resolve isso
  fragmentando em um arquivo por decisão.
