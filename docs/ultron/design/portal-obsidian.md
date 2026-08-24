# ULTRON — Design do Portal (Inspiração Obsidian)

> **Status:** Proposta — será implementada no gate **U1**
> **Data:** 2026-08-24
> **Owner:** Kauã
> **Referência visual:** Obsidian (https://obsidian.md)

## Por que Obsidian

O Obsidian é a referência de UX mais bem resolvida para **navegação em grafo de conhecimento local-first**, com:

1. **Local-first** — o vault é o usuário, sem dependência de cloud.
2. **Grafo de conhecimento** — backlinks, graph view, navegação por links.
3. **Busca fuzzy poderosa** — Omnisearch é referência de mercado.
4. **Comando por teclado** — Command Palette como cidadão de primeira classe.
5. **Tema escuro nativo** — pensado para leitura longa, sem cansaço visual.
6. **Sem distrações** — densidade informacional alta, sem ornamento gratuito.

Esses princípios batem direto com o ULTRON: registry é um "vault" de capabilities, com grafo de dependências, busca poderosa e operação local.

## Princípios de design do portal ULTRON

1. **Local-first sempre** — o portal lê do registry local. Offline-first.
2. **Densidade informacional alta** — sem espaços vazios, sem "marketing fluff".
3. **Tema escuro por padrão** — com toggle para claro (não branco puro).
4. **Tipografia para devs** — JetBrains Mono em código, Inter/SF Pro em texto.
5. **Command Palette como primeiro cidadão** — `Ctrl+K` resolve quase tudo.
6. **Graph view 3D** — Three.js, mas com fallback 2D (D3 ou Sigma.js).
7. **Backlinks visíveis** — quem usa `X`? quem é usado por `X`?
8. **Status visível** — instalado / ativado / quarentena / deprecated em cores.

## Layout

Inspirado na **três colunas** do Obsidian:

```
┌─────────────────────────────────────────────────────────────┐
│  Top bar:  [logo ULTRON]  [search ⌘K]    [theme] [profile] │
├──────────────┬──────────────────────────┬───────────────────┤
│  Sidebar     │  Conteúdo principal      │  Aside direito    │
│  (file tree) │  (manifest detalhado)    │  (backlinks,      │
│              │                          │   versões,        │
│  - All       │  - Header (id, version)  │   health)         │
│  - Agents    │  - Tabs (info, deps,     │                   │
│  - Skills    │    permissions, his-     │  - Backlinks      │
│  - Workflows │    tory)                 │  - Used by        │
│  - Packs     │  - Markdown body         │  - Versions       │
│  - Tags      │  - Code (entrypoint)     │  - Provenance     │
│              │                          │                   │
└──────────────┴──────────────────────────┴───────────────────┘
```

## Stack técnico do portal

- **Backend:** FastAPI (já escolhido no U0) servindo JSON + Server-Sent Events
- **Frontend:** renderizado server-side com **HTMX** + **Alpine.js** (decisão deliberada — ver abaixo)
- **Estilo:** **Pico.css** (ou **classless CSS**) como base, customizado em CSS variables
- **Grafo 3D:** **Three.js** (carregado só quando o usuário abre a view)
- **Busca:** API própria (FTS5 do SQLite), exposta via `/api/v1/search?q=...`
- **Command Palette:** **cmdk** (estilo Raycast) em vanilla JS/TS compilado, ou implementação própria em ~150 linhas

### Por que HTMX + Alpine em vez de React/Vue/Svelte

- **Menos peso** — portal é leitura, não aplicação interativa pesada
- **Sem build step** — edita o `.html` e dá refresh
- **Server-rendered** — combina com FastAPI (Jinja2 templates)
- **Progressive enhancement** — funciona sem JS, fica melhor com JS
- **Custo de manutenção** — sem framework que muda de major a cada 6 meses

> Decisão registrada como **DEC-U-009**: HTMX + Alpine.js como stack do portal.

## Tema visual

### Paleta (CSS custom properties)

```css
:root {
  /* Dark (default) */
  --bg-primary: #1e1e1e;     /* fundo principal */
  --bg-secondary: #252525;   /* sidebar, header */
  --bg-tertiary: #2a2a2a;    /* hover, focus */
  --fg-primary: #dcddde;     /* texto principal */
  --fg-muted: #999;          /* texto secundário */
  --fg-faint: #666;          /* labels */
  --accent: #7f6df2;         /* roxo Obsidian-like */
  --accent-hover: #9b8bf5;
  --border: #333;
  --success: #4ade80;        /* instalado/ativado */
  --warning: #fbbf24;        /* quarentena */
  --danger: #f87171;         /* revogado, crítico */
  --info: #60a5fa;           /* planejado */
}

[data-theme="light"] {
  --bg-primary: #fafafa;
  --bg-secondary: #f0f0f0;
  --bg-tertiary: #e8e8e8;
  --fg-primary: #1a1a1a;
  --fg-muted: #666;
  --fg-faint: #999;
  --accent: #6d5fd2;
  --accent-hover: #5b4dc7;
  --border: #d0d0d0;
  /* cores de status iguais ou similares */
}
```

### Tipografia

- **Texto:** Inter (self-host) ou system-ui
- **Código:** JetBrains Mono (self-host) ou ui-monospace
- **Tamanhos:** 14px base, 13px em sidebar, 12px em labels

## Funcionalidades por gate

### U1 — Portal mínimo viável
- [x] Layout de 3 colunas
- [x] Tema dark + toggle
- [x] Lista de manifests (sidebar com filtro por tipo)
- [x] Página de detalhe de manifest (info + tabs)
- [x] Command Palette (Ctrl+K) com busca fuzzy
- [x] Search via FTS5
- [x] Backlinks simples (quem depende deste)

### U4 — Portal completo
- [ ] Graph view 3D (Three.js) com física
- [ ] Visualização de versões (timeline)
- [ ] Diff entre versões
- [ ] Filtros avançados (combine tipo + risk + license)
- [ ] Tags e bookmarks
- [ ] Exportar grafo (PNG, JSON, GraphML)

## Acessibilidade

- WCAG 2.1 AA mínimo
- Navegação completa por teclado
- Foco visível (outline 2px solid accent)
- Contraste mínimo 4.5:1 em texto normal
- `prefers-reduced-motion` respeitado (graph view sem animação)
- ARIA labels em controles interativos

## Performance

- **First paint** < 1s em máquina dev local
- **TTI** < 2s
- **Bundle total** < 200KB (sem Three.js, que é lazy-loaded)
- **FTS5** em arquivos < 10k manifests responde em < 10ms

## Anti-padrões a evitar

- ❌ Skeleton loaders infinitos (mostra dado real, mesmo que parcial)
- ❌ Toast notifications insistentes (uma vez só, com dismiss)
- ❌ Animações decorativas (só onde comunicam)
- ❌ Ícones sem label (sempre com texto ou aria-label)
- ❌ Tema claro forçado como default (dark é o default)
- ❌ "AI" no canto da tela oferecendo ajuda (sem prompt explícito, não aparece)
