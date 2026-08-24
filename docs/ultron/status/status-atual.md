# ULTRON — Status Atual e Linha do Tempo de Implementação

> **Data de início do desenvolvimento:** 2026-08-24
> **Owner:** Kauã
> **Repositório:** https://github.com/restoffkaua08-afk/ultron
> **Documentação mestra:** https://github.com/restoffkaua08-afk/documenta-oMeta

## Estado por gate (atualizado 2026-08-24)

| Gate | Descrição | Estado anterior (2026-08-21) | Estado atual (2026-08-24) | Evidência |
|---|---|---|---|---|
| U0 | Escopo e contratos | Não aberto | **Aprovado** | commit `f240df6` no repo; 76 testes, 92.71% cobertura, ruff+mypy limpos |
| U1 | Registry Ready | Não aberto | Em curso | design pronto, scaffold em andamento |
| U2 | Installation Ready | Não aberto | Planejado | — |
| U3 | Security Ready | Não aberto | Planejado | — |
| U4 | Graph Ready | Não aberto | Planejado | — |
| U5 | Zane Compatibility | Não aberto | Planejado | — |

## Linha do tempo

- **2026-08-21** — Documentação consolidada em `docs/`. ULTRON marcado como "a inventariar".
- **2026-08-24** — Repositório `restoffkaua08-afk/ultron` criado; gate **U0 aprovado**.

## O que está implementado (commit `f240df6`)

### Stack e tooling
- Python 3.12+ (testado em 3.13)
- `pyproject.toml` com hatchling, deps fixadas
- **Pydantic v2** strict para todos os modelos
- **FastAPI** + **Typer** + **structlog** como deps de runtime
- **Ruff** (lint+format), **mypy --strict** (tipos), **pytest** + **pytest-cov** + **hypothesis** (testes)
- Cobertura mínima 85%, atingida em **92.71%**

### Domínio (núcleo puro, sem I/O)
- `BaseManifest` com invariantes anti-gate (extra="forbid", frozen, publisher==id.publisher, no `approve-all`)
- 4 manifests concretos: `AgentManifest`, `SkillManifest`, `WorkflowManifest`, `PackManifest`
- Tipos canônicos: `ManifestId` (frozen dataclass, kebab-case), `UltronVersion` (semver estrito), `UltronVersionRange`, `PublisherId`
- `IntegrityInfo` (SHA-256) e `Provenance` (local/git/oci, com validação que git/oci exige `repository`)
- 7 erros tipados em hierarquia `UltronError`
- `detect_cycles()` (DFS) e `check_unique_versions()` — sem libs externas
- `Policy` (deny-by-default) com allowlist `VALID_PERMISSIONS`
- `check_manifest_permissions()` — checa requested vs. policy

### Contratos
- `ConsumerAdapter` (ABC) com 8 métodos abstratos
- `CONSUMER_PROTOCOL_VERSION = "1.0.0"`
- `CapabilityRef`, `InstallPlan` (dataclasses frozen)

### Stubs reservados (U1–U5)
- `src/ultron/api/__init__.py` — router FastAPI prefixado `/api/v1` com `/health`
- `src/ultron/audit/__init__.py` — `configure_logging` + `get_logger` via structlog
- `src/ultron/cli/__init__.py` — `ultron version`, `ultron hello` via Typer
- Diretórios vazios com `__init__.py` reservado: `registry/`, `store/`, `validation/`, `lifecycle/`, `graph/`, `security/`, `validation/`

### Testes
- 76 testes unitários em 7 arquivos (`test_base.py`, `test_cli.py`, `test_consumer.py`, `test_ids.py`, `test_manifests.py`, `test_policy.py`, `test_smoke.py`)
- 100% dos testes passam
- Marcadores pytest já definidos: `unit`, `integration`, `contract`, `security`, `gate_U0`, `gate_U1` (pronto para U1)

## O que ainda precisa ser feito (U1 → U5)

### U1 — Registry Ready
- `Registry` (SQLite + FTS5) com CRUD completo
- Busca e filtros (tipo, nome, capability, runtime, publisher, licença, risco, status)
- API HTTP via FastAPI (rotas de query, get, search, list)
- Telemetria básica via structlog
- Suíte de testes de integração
- **Portal web com design inspirado no Obsidian** (ver `design/portal-obsidian.md`)

### U2 — Installation Ready
- Resolução de dependências (semver real, lockfile)
- Package store (filesystem com hash)
- Pipeline install/activate/deactivate/remove
- Rollback e lockfile
- Adapters de referência (HTTP e in-process)

### U3 — Security Ready
- Pipeline de validação (schema → integrity → deps → policy → sandbox)
- Sandbox real (containers efêmeros, sem rede por padrão)
- Quarentena e revogação
- Auditoria completa

### U4 — Graph Ready
- Namespaces + lineage
- Grafo de dependências (NetworkX ou similar)
- Portal 3D opcional (Three.js)
- Visualização de versões e proveniência

### U5 — Zane Compatibility
- Consumer Adapter versionado
- Testes de falha offline (Zane roda sem ULTRON)
- E2E de instalação e rollback
- Prova de independência

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
| DEC-U-008 | Design UI do portal inspirado no Obsidian | `design/portal-obsidian.md` (a criar) |

## Riscos conhecidos (do registro geral do Meta)

- **R-001** — tratar doc de aceite como prova. Mitigação: este status sempre cita commit.
- **R-009** — documentação divergir do código. Mitigação: docs são atualizadas por PR.
- **R-004** — tornar ULTRON requisito. Mitigação: a regra "Zane sem ULTRON" é invariante desde o U0.
