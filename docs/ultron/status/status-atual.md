# ULTRON — Status Atual e Linha do Tempo de Implementação

> **Data de início do desenvolvimento:** 2026-08-24
> **Owner:** Kauã
> **Repositório:** https://github.com/restoffkaua08-afk/ultron
> **Documentação mestra:** https://github.com/restoffkaua08-afk/documenta-oMeta

## Estado por gate

| Gate | Descrição | Estado | Evidência |
|---|---|---|---|
| U0 | Escopo e contratos | **Aprovado** | commit `f240df6`; 76 testes, 92.71% cobertura, ruff+mypy limpos |
| U1 | Registry Ready | **Aprovado** | commit `10ff301`; 105 testes, 86.40% cobertura, portal Obsidian entregue |
| U2 | Installation Ready | Não aberto | — |
| U3 | Security Ready | Não aberto | — |
| U4 | Graph Ready | Não aberto | — |
| U5 | Zane Compatibility | Não aberto | — |

## Linha do tempo

- **2026-08-21** — Documentação consolidada em `docs/`. ULTRON marcado como "a inventariar".
- **2026-08-24** — Repositório `restoffkaua08-afk/ultron` criado; gate **U0 aprovado**.
- **2026-08-24** — Gate **U1 aprovado**. Registry SQLite+FTS5, API HTTP e portal Obsidian entregues e publicados.

## O que está implementado

### U0 (commit `f240df6`) — núcleo

- Python 3.12+ (testado em 3.13)
- `pyproject.toml` com hatchling, deps fixadas
- **Pydantic v2** strict para todos os modelos
- **FastAPI** + **Typer** + **structlog** como deps de runtime
- **Ruff** (lint+format), **mypy --strict** (tipos), **pytest** + **pytest-cov** + **hypothesis** (testes)
- Cobertura mínima 85%, atingida em **92.71%**

**Domínio (núcleo puro, sem I/O):**
- `BaseManifest` com invariantes anti-gate (extra="forbid", frozen, publisher==id.publisher, no `approve-all`)
- 4 manifests concretos: `AgentManifest`, `SkillManifest`, `WorkflowManifest`, `PackManifest`
- Tipos canônicos: `ManifestId` (frozen dataclass, kebab-case), `UltronVersion` (semver estrito), `UltronVersionRange`, `PublisherId`
- `IntegrityInfo` (SHA-256) e `Provenance` (local/git/oci, com validação que git/oci exige `repository`)
- 7 erros tipados em hierarquia `UltronError`
- `detect_cycles()` (DFS) e `check_unique_versions()` — sem libs externas
- `Policy` (deny-by-default) com allowlist `VALID_PERMISSIONS`
- `check_manifest_permissions()` — checa requested vs. policy

**Contratos:**
- `ConsumerAdapter` (ABC) com 8 métodos abstratos
- `CONSUMER_PROTOCOL_VERSION = "1.0.0"`
- `CapabilityRef`, `InstallPlan` (dataclasses frozen)

### U1 (commit `10ff301`) — Registry + Portal + HTTP

**Registry (`src/ultron/registry/`):**
- Backend SQLite (WAL, foreign keys, hash de payload)
- Tabela `manifests` com colunas id, version, kind, publisher, name, description, license, risk, schema_version, payload_json, payload_hash, published_at
- Tabela virtual FTS5 (`manifests_fts`) — busca textual + por capability; tokenizer `unicode61 remove_diacritics 2` (acentos-insensível)
- Tabela `migrations` (versionada, idempotente)
- Tabela `audit` append-only com `event_id`, `occurred_at`, `actor`, `action`, `target_id`, `target_version`, `payload_hash`, `correlation_id`
- API assíncrona via `aiosqlite`
- Operações: `publish`, `get`, `list_all`, `search`, `delete`, `count`, `stats`, `recent_audit`
- SHA-256 determinístico do payload garante imutabilidade (re-publicar mesma `id+version` falha com `VersionConflictError`)
- Modos: standalone (`Registry.open()` async CM) ou FastAPI lifespan (`start`/`close`)

**API HTTP (`src/ultron/api/`):**
- `app = create_app()` singleton, lifespan gerencia registry
- `GET /api/v1/health` — status + path + count
- `GET /api/v1/manifests?kind=&limit=&offset=` — lista paginada
- `GET /api/v1/manifests/search?q=&kind=&capability=&publisher=&license=&risk=` — FTS5 + filtros estruturados
- `GET /api/v1/manifests/{id}` ou `/api/v1/manifests/{id}@{ver}` — detalhe
- `GET /api/v1/stats` — agregados
- `GET /api/v1/audit/recent` — eventos
- Tudo versionado em `/api/v1/*`

**Portal HTML (`src/ultron/portal/`):**
- Stack: FastAPI + Jinja2 + CSS custom + vanilla JS + Cytoscape (graph). Sem React, sem build step, sem JS pesado
- 3-column layout (sidebar + content) estilo Obsidian
- Dark theme default (paleta `#1e1e1e` bg / `#7f6df2` accent), toggle para light
- Tipografia Inter + JetBrains Mono
- Páginas: `/` (dashboard), `/browse` (lista + busca + filtros), `/manifest/{id}` (detalhe com dependências + backlinks + payload JSON), `/graph` (Cytoscape), `/audit` (tabela append-only)
- Command palette `⌘K` com live search via `/api/v1/manifests/search`
- Theme toggle persistido em localStorage
- Backlinks: manifestos que dependem do atual (grafo reverso)
- 6 templates Jinja2 + 2 JS files + 1 CSS

**Testes (`tests/integration/`):**
- 29 novos testes: 20 do Registry (CRUD, FTS5, audit, imutabilidade, versionamento, diacríticos, paginação, count, stats) + 9 da API (health, OpenAPI, todas as páginas renderizam, endpoints JSON, 404)
- Marcador `integration` separa de unit, usa `pytest-asyncio`
- Fixtures com `tmp_path` para DB isolado por teste

**Métricas finais:**
- 105 testes passam
- 86.40% cobertura (acima do mínimo 85%)
- mypy strict: 0 erros em 14 arquivos
- ruff: All checks passed!

## O que ainda precisa ser feito (U2 → U5)

### U2 — Installation Ready
- Resolução de dependências (semver real, lockfile) usando lib `packaging`
- Package store (filesystem com hash, arquivo TAR/ZIP/PKG)
- Pipeline `install → activate → deactivate → remove`
- Rollback e lockfile por environment
- Adapters de referência (HTTP e in-process)
- Anti-gates: install NÃO roda código arbitrário; activate NÃO concede permissão; registry offline NÃO quebra consumer

### U3 — Security Ready
- Pipeline de validação: schema → integrity → dependencies → policy → sandbox
- Sandbox real (containers efêmeros, sem rede por padrão)
- Quarentena e revogação (status REGISTRY → `REVOKED`)
- Auditoria completa (toda decisão fica no log append-only)
- Threat model publicado

### U4 — Graph Ready
- Namespaces + lineage
- Grafo de dependências formal (NetworkX ou similar — Cytoscape ja da conta para visual)
- Portal opcional com Sigma.js/Three.js para grafos densos
- Visualização de versões e proveniência

### U5 — Zane Compatibility
- Consumer Adapter versionado (extensão do atual)
- Testes de falha offline (Zane roda SEM ULTRON — invariante!)
- E2E de instalação e rollback com Zane mockado
- Prova formal de independência

## Decisões arquiteturais registradas

| ID | Decisão | Onde |
|---|---|---|
| DEC-U-001 | Stack: Python 3.12+ + Pydantic v2 + FastAPI + SQLite/uv | pyproject.toml, este doc |
| DEC-U-002 | Schemas Pydantic v2 strict, `extra="forbid"`, `frozen=True` | `core/base.py` |
| DEC-U-003 | `ManifestId` como `@dataclass(frozen=True, slots=True)` (não Pydantic) | `core/ids.py` |
| DEC-U-004 | Deny-by-default via allowlist `VALID_PERMISSIONS` | `core/base.py`, `policy/__init__.py` |
| DEC-U-005 | Consumer Adapter como ABC, protocolo versionado | `consumer/__init__.py` |
| DEC-U-006 | Cobertura mínima 85% enforced em `pyproject.toml` | `pyproject.toml` |
| DEC-U-007 | mypy strict enforced em `pyproject.toml` | `pyproject.toml` |
| DEC-U-008 | Design UI do portal inspirado no Obsidian | `design/portal-obsidian.md` |
| DEC-U-009 | Portal stack: HTMX-free + Jinja2 + vanilla JS + CSS (não React/Vue/Svelte) | `design/portal-obsidian.md`, `portal/` |

## Riscos conhecidos (do registro geral do Meta)

- **R-001** — tratar doc de aceite como prova. Mitigação: este status sempre cita commit.
- **R-009** — documentação divergir do código. Mitigação: docs são atualizadas por PR.
- **R-004** — tornar ULTRON requisito. Mitigação: a regra "Zane sem ULTRON" é invariante desde o U0.
- **R-NEW-1** — Portal renderiza Cytoscape via CDN → falha offline. Mitigação: fallback HTML se JS não carregar (já tratado em `graph.html`).
