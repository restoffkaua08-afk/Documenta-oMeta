# ULTRON — Necessidades Adicionais de Design e Arquitetura

> **Data:** 2026-08-24
> **Propósito:** Capturar requisitos que não estão na spec original mas emergiram durante a implementação do U0 e o planejamento do U1+.

## Análise: o que falta além da spec para o ULTRON ficar completo

A spec em `docs/ultron/arquitetura/` cobre o **mínimo arquitetural**. Mas para o ULTRON virar um produto usável (e não só uma spec bonita), precisa de mais. Esta lista captura o que **descobrimos** ao implementar o U0 e o que **antecipamos** que vai faltar.

### 1. Versionamento de schema (já parcialmente resolvido)

- **O que tem:** `BaseManifest.schema_version: UltronVersion` (default `"1.0.0"`)
- **O que falta:**
  - Migração automática de manifests antigos quando o schema evolui
  - Política de deprecação (avisar quando alguém publica um manifest v1 após v2 existir)
  - Endpoint que devolve "schema versions supported" no registry
- **Onde entra:** U1 (busca), U2 (instalação), e o manifesto de migração em si

### 2. Resolução de semver (parcial)

- **O que tem:** regex estrita de `UltronVersion` e regex leve de `UltronVersionRange`
- **O que falta:**
  - Resolver `>=1.0.0,<2.0.0` contra um conjunto de versões publicadas
  - Lidar com `^`, `~`, `*`, `x` em ranges
  - Lockfile determinístico
- **Onde entra:** U2 (instalação)
- **Lib sugerida:** `packaging` (stdlib-like do pip) — já é battle-tested

### 3. Validação de entrypoint (parcial)

- **O que tem:** `AgentManifest` exige formato `package.module:Symbol`
- **O que falta:**
  - Validar que o módulo é importável (sem executar o agent)
  - Validar que o Symbol existe no módulo
  - Cache de import paths para evitar re-checar toda vez
- **Onde entra:** U3 (security — verificação estática)

### 4. Storage físico (não existe ainda)

- **O que precisa:**
  - Onde ficam os artefatos binários das capabilities? (skill scripts, agent wheels, etc.)
  - Política de retenção (sempre manter últimas N versões?)
  - Deduplicação por hash
- **Onde entra:** U2 (package store)
- **Decisão sugerida:** `~/.ultron/store/<sha256[0:2]>/<sha256>` no filesystem local

### 5. Identidade do consumer (não existe)

- **O que precisa:**
  - Cada consumer (Zane, Jarvis, dev tools) precisa de um **consumer ID** estável
  - Esse ID vai no audit log e no lockfile (para evitar duas instâncias mexerem no mesmo state)
  - O consumer ID deve ser local (UUID v4 gerado na primeira execução) — não precisa de servidor central
- **Onde entra:** U2 (adapter de referência)

### 6. Auditoria reproduzível (não existe)

- **O que precisa:**
  - Cada operação no registry (publish, install, activate, deactivate, remove) gera um evento estruturado
  - Eventos têm `event_id`, `correlation_id`, `occurred_at`, `actor`, `payload` (do envelope mínimo de `integracao/contratos-entre-projetos.md`)
  - Logs **não** contêm payload bruto (apenas hash do payload) — pra evitar vazar dados sensíveis
- **Onde entra:** U2 (lifecycle) + U3 (security)
- **Onde persiste:** `~/.ultron/audit.log.jsonl` (append-only, line-delimited JSON)

### 7. Sincronização opcional (planejada para muito depois)

- **O que precisa:**
  - Spec já fala em "modo cloud opcional" no Zane
  - Para o ULTRON especificamente, sync = publicar em um **remote** (git/OCI) e baixar de lá
  - Conflito de versão entre local e remote precisa de política explícita
- **Onde entra:** depois de U5 (pós-MVP)
- **Decisão sugerida:** U0–U5 são local-only. Sync fica para o roadmap pós-Meta.

### 8. Migração de dados de registry (não existe)

- **O que precisa:**
  - Quando o schema do registry evoluir (U0 → U1 → U2...), o SQLite local precisa ser migrado
  - Usar **Alembic** ou migration scripts manuais versionados
- **Onde entra:** U1 (primeira migration) — usar **Alembic** desde o início

### 9. Observabilidade do próprio registry (não existe)

- **O que precisa:**
  - Endpoint `/api/v1/health` (já existe) deve devolver:
    - Status do SQLite (read/write)
    - Tamanho do registry (count de manifests)
    - Última migration aplicada
  - Endpoint `/api/v1/metrics` (Prometheus) — para quando o Zane monitorar
- **Onde entra:** U1 (health), U4 (metrics)

### 10. CLI amigável (parcial)

- **O que tem:** `ultron version`, `ultron hello`
- **O que falta:**
  - `ultron search <query>` (usa FTS5)
  - `ultron get <id>` (mostra detalhes)
  - `ultron install <id>[@version]` (U2)
  - `ultron activate <id>` (U2)
  - `ultron list` (lista instalados, U2)
  - `ultron graph` (gera PNG do grafo, U4)
  - `ultron schema` (exporta JSON Schema dos manifests, U1)
- **Onde entra:** progressivamente em U1–U4

### 11. Internacionalização (não existe)

- **O que precisa:**
  - Mensagens de erro em PT/EN (ou pelo menos EN por padrão + escape hatch para tradução)
  - Docs em PT e EN (começar com PT — dono fala PT)
- **Onde entra:** U3 (mensagens tipadas facilitam) + paralelo em docs
- **Decisão:** começar PT-only, com placeholders `_()` para i18n futuro

### 12. Plugin system para validadores (planejado)

- **O que precisa:**
  - A spec fala em "Validation Service" — mas não diz como adicionar validadores customizados
  - Precisamos de um **entry point** Python: `ultron.validators` (group)
  - Um consumidor pode adicionar `validator X` sem mexer no core
- **Onde entra:** U3 (security)
- **API:** `register_validator(manifest_kind: str, fn: Callable[[Manifest], list[Issue]])`

## Resumo de onde cada item entra

| Item | Gate | Esforço | Bloqueador? |
|---|---|---|---|
| Versionamento de schema | U1, U2 | M | Não |
| Resolução de semver | U2 | M | Sim para U2 |
| Validação de entrypoint | U3 | M | Não |
| Storage físico | U2 | M | Sim para U2 |
| Identidade do consumer | U2 | P | Sim para U2 |
| Auditoria | U2, U3 | M | Sim para U2 |
| Sincronização | pós-MVP | G | Não |
| Migração de dados | U1 | P | Sim para U1 |
| Observabilidade | U1, U4 | P | Não |
| CLI | U1–U4 | M | Não |
| i18n | U3, paralelo | P | Não |
| Plugin system | U3 | M | Não |

## Conclusão

Os itens 1, 2, 3 e 8 (versionamento, semver, entrypoint, migrations) **definem** o ULTRON como produto usável. Os outros 8 são **qualidade de vida** e podem ser adicionados incrementalmente. O U1 já cuida de 1, 8, 9 e parte de 10. O U2 cuida de 2, 4, 5, 6. O U3 cuida de 3, 11, 12.
