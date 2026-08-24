# ULTRON - Roadmap e Gates

## U0 - Escopo e contratos

- confirmar repositorio e stack;
- congelar schemas iniciais;
- definir protocolo do consumer;
- definir modelo de seguranca.

## U1 - Registry Ready

- CRUD/versionamento de manifests;
- busca e filtros;
- schemas estritos;
- proveniencia e integridade;
- API e testes.

Gate: `ULTRON_REGISTRY_READY`.

## U2 - Installation Ready

- resolucao de dependencias;
- package store;
- install/activate/deactivate/remove;
- lockfile e rollback;
- adapters de referencia.

Gate: `ULTRON_INSTALLATION_READY`.

## U3 - Security Ready

- pipeline de validacao;
- sandbox;
- permissoes;
- supply chain;
- quarentena/revogacao;
- auditoria.

Gate: `ULTRON_SECURITY_READY`.

## U4 - Data e Graph Ready

- namespaces;
- lineage;
- grafo de dependencias;
- busca/portal;
- visualizacao opcional 3D;
- retencao.

Gate: `ULTRON_GRAPH_READY`.

## U5 - Zane Compatibility

- consumer adapter versionado;
- falha segura/offline;
- capacidades nativas preservadas;
- E2E de instalacao e rollback;
- prova de independencia.

Gate: `ULTRON_ZANE_COMPATIBLE`.

## Anti-gates

Nenhum gate pode ser aprovado se:

- instalar executa codigo arbitrario;
- ativar concede permissao automaticamente;
- registry indisponivel derruba o consumidor;
- versao publicada e mutavel;
- namespaces nao isolam dados;
- nao existe rollback/limpeza para falha parcial;
- ULTRON se torna requisito para Zane iniciar.

## Estado atual

Planejado. Nenhum gate esta comprovadamente verde com as fontes analisadas.


---

## Status dos gates (atualizado 2026-08-24)

### U0 — ✅ Aprovado (commit `f240df6`)

- 76 testes unitários, 92.71% cobertura
- Núcleo imutável, Consumer ABC, deny-by-default

### U1 — ✅ Aprovado e estabilizado (base `10ff301`)

- **111 testes**, **89,18% cobertura** após estabilização
- Registry SQLite + FTS5 (aiosqlite), WAL, audit append-only
- API HTTP versionada (`/api/v1/*`) + portal Obsidian (HTMX-free, vanilla JS)
- Estrutura: `src/ultron/registry/`, `src/ultron/api/`, `src/ultron/portal/`
- Endpoints JSON: health, manifests list/search/get, stats, audit
- Páginas HTML: home, browse, manifest detail, graph (Cytoscape), audit
- Métricas: mypy strict, ruff check, ruff format e build aprovados

### U2 — ⏳ Pendente

- Ver checklist acima + adicionar `packaging`-style semver resolution
