# ULTRON — Registro de Decisões Arquiteturais

> **Propósito:** Toda decisão de design/arquitetura do ULTRON fica registrada aqui com data, contexto, alternativas e consequências. Decisões superadas vão para `decisoes-superadas.md` (criado quando houver a primeira).

## DEC-U-001 — Stack: Python 3.12+ + Pydantic v2 + FastAPI + SQLite

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** Precisamos de uma stack que combine com o resto do Meta (Python), com tipagem forte (anti-gate de "schemas estritos") e que não puxe o consumidor para uma plataforma.
- **Decisão:** Python 3.12+ como linguagem, Pydantic v2 strict como camada de validação, FastAPI como HTTP server, SQLite + FTS5 como registry store.
- **Alternativas consideradas:**
  - Node.js + TypeScript + Zod: rejeitada por quebrar homogeneidade com Rachel/Jarvis.
  - Go: rejeitada por comunidade Meta ser Python-first.
  - Rust: rejeitada por barreira de entrada alta.
- **Consequências:**
  - Aproveita o ecossistema Pydantic (validação, serialização, JSON Schema grátis).
  - SQLite é local-first por natureza, atende o invariante.
  - FastAPI dá OpenAPI grátis (úteis para o Consumer Adapter).
- **Verificação:** U0 — todos os manifests são Pydantic v2 strict, testes verdes.

## DEC-U-002 — Schemas `extra="forbid"` e `frozen=True`

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** A spec diz "campos desconhecidos tratados como permissão" é proibição. Manifests publicados devem ser imutáveis (imutabilidade da versão publicada).
- **Decisão:** Todo `BaseModel` no ULTRON tem `model_config = ConfigDict(extra="forbid", frozen=True)`.
- **Consequências:**
  - Adicionar campo novo no schema = erro de validação em manifests antigos.
  - Para adicionar campo, criar nova `schema_version`.
  - Imutabilidade previne mutação acidental em runtime.
- **Verificação:** coberto por testes em `test_manifests.py::TestAgentManifest::test_extra_field_rejeitado`.

## DEC-U-003 — `ManifestId` como `@dataclass(frozen=True, slots=True)`

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** Pydantic não lida bem com classes custom via `arbitrary_types_allowed`. mypy reclama de `__slots__` em Pydantic models. A solução mais limpa é usar dataclass.
- **Decisão:** `ManifestId` é `@dataclass(frozen=True, slots=True, eq=True)` com `__post_init__` que valida publisher e name.
- **Alternativas consideradas:**
  - Pydantic `BaseModel` com `__get_pydantic_core_schema__` custom: rejeitada por complexidade.
  - NamedTuple: rejeitada por ergonomia (precisa de método `parse`).
- **Consequências:**
  - mypy feliz.
  - Validação ainda no `__post_init__` (anti-gate preservado).
  - Hashing e equality vêm grátis do `@dataclass(eq=True)`.
- **Verificação:** `tests/unit/test_ids.py::TestManifestId` (7 testes, 100% verde).

## DEC-U-004 — Deny-by-default via allowlist `VALID_PERMISSIONS`

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** Spec diz "manifest declara permissões solicitadas. O consumidor pode negar, reduzir ou exigir aprovação." A negação deve ser o padrão, não o pedido.
- **Decisão:** Existe `VALID_PERMISSIONS: frozenset[str]` em `core/base.py` com a lista fechada de capacidades permitidas. Adicionar uma nova exige decisão consciente e atualização da lista + testes. Toda `Permission` valida contra essa allowlist em `model_post_init`.
- **Consequências:**
  - Tentativa de permissão fora da allowlist = erro de validação.
  - Anti-gate `approve-all` e `*` são proibidos (verificados no `BaseManifest`).
  - Policy do consumer inicia em deny-all (`default_deny_policy()`).
- **Verificação:** `tests/unit/test_base.py::TestPermission::test_permissao_desconhecida_rejeitada`.

## DEC-U-005 — Consumer Adapter como ABC, protocolo versionado

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** ULTRON pode ter múltiplos consumidores (Zane, Jarvis, dev tools). Eles não devem ser acoplados a uma implementação concreta.
- **Decisão:** Interface `ConsumerAdapter` (ABC) com 8 métodos. Constante `CONSUMER_PROTOCOL_VERSION = "1.0.0"` no módulo. Breaking change = bump de versão.
- **Consequências:**
  - Cada consumer implementa a interface como quiser (HTTP, in-process, CLI).
  - Mudança no protocolo = nova versão, consumers rejeitam a antiga explicitamente.
- **Verificação:** `tests/unit/test_consumer.py` (3 testes).

## DEC-U-006 — Cobertura mínima 85% enforced

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** Spec diz "gate exige evidência reproduzível". Cobertura é uma evidência fraca (pode-se ter 100% cobrindo nada), mas abaixo de 85% indica código não testado.
- **Decisão:** `pyproject.toml` tem `fail_under = 85` no coverage. CI falha se cair.
- **Consequências:**
  - U0 atingiu 92.71% — folga confortável.
  - Forçar o hábito de testar.
- **Verificação:** U0 — 92.71% atingido, gate U0 aprovado.

## DEC-U-007 — mypy strict enforced

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** "Schemas estritos" é mais que schema — é também o sistema de tipos. `Any` vazando significa contrato opaco.
- **Decisão:** `pyproject.toml` tem `strict = true` no mypy, aplicado a `src/ultron/`. CI falha em qualquer erro.
- **Consequências:**
  - Força contratos explícitos.
  - Torna refactor mais seguro.
- **Verificação:** U0 — 0 erros mypy.

## DEC-U-008 — Design UI do portal inspirado no Obsidian

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** O portal/UI do ULTRON precisa ser denso, navegável, com graph view e command palette. Obsidian já resolveu isso para um domínio análogo (vault de notas).
- **Decisão:** Portal segue a estética do Obsidian: 3 colunas, tema dark por padrão, command palette, graph view 3D, backlinks.
- **Detalhes:** `design/portal-obsidian.md`
- **Consequências:**
  - UX familiar para usuários de Obsidian.
  - Reduz decisões de design (já está testado em escala).
  - Alinhado com o invariante "local-first, sem cloud obrigatória".

## DEC-U-009 — Frontend do portal: HTMX + Alpine.js (não React/Vue/Svelte)

- **Data:** 2026-08-24
- **Estado:** Ativa
- **Contexto:** Portal do ULTRON é majoritariamente leitura + interação leve. Frameworks SPA completos são overkill.
- **Decisão:** Backend FastAPI renderiza Jinja2 + HTMX para partials. Alpine.js para interatividade client-side leve. Pico.css como base.
- **Alternativas consideradas:**
  - React + Vite: rejeitada por build step + dependência de Node.
  - Vue 3: rejeitada pelo mesmo motivo.
  - Svelte 5: idem.
  - SSR puro (sem JS): rejeitada por perder command palette interativa.
- **Consequências:**
  - Sem build step no portal.
  - Funciona com JS desabilitado (degradação graciosa).
  - Bundle < 50KB.
- **Verificação:** U1.
