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

## Status dos gates (atualizado 2026-08-25)

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

### U2 — ✅ Aprovado (`65ff7a5`)

- Resolver recursivo determinístico com seleção da maior versão SemVer compatível
- Ranges exatos, `^`, `~`, curingas e especificadores PEP 440
- Ciclos, conflitos, dependências obrigatórias ausentes e versões revogadas bloqueados
- Package store imutável endereçado por SHA-256, escrita atômica e detecção de adulteração
- Lockfile canônico e determinístico com escrita atômica
- Pipeline transacional: resolver → verificar integridade → armazenar → trocar lockfile
- Estado anterior preservado em pacote ausente, incompleto ou adulterado
- Instalação não ativa, não executa entrypoints e não concede permissões
- Ativação/desativação explícitas, idempotentes e persistidas atomicamente
- Mudança de versão reconcilia o estado e volta a capability para inativa
- Remoção segura bloqueia raiz, capability ativa e dependências ainda utilizadas
- Journal imutável de lockfiles com SHA-256 e rollback explícito; upgrades preservam o estado anterior
- Adapters de referência por mapping e filesystem local confinado
- Coleta conservadora com simulação padrão e proteção de todos os checkpoints
- E2E comprovado: referência → instalação → ativação → upgrade → rollback → coleta
- Handshake SemVer e suíte de conformidade neutra comprovados para Claude, Codex, Zane e consumers próprios
- Major incompatível falha de forma tipada com `PROTOCOL_INCOMPATIBLE`
- 149 testes, 91,15% de cobertura, lint/formatação, tipagem de produção e build aprovados

Gate `ULTRON_INSTALLATION_READY` aprovado. REST, MCP e autenticação cloud permanecem nas etapas de plataforma sem alterar os invariantes locais.

### U3 — 🚧 Em desenvolvimento (fundação `7776827`)

- Pipeline determinístico de validação sem execução de pacotes
- SHA-256 obrigatório e proveniência Git/OCI fixada por commit ou digest
- Detecção de risco subestimado e aviso de aprovação para alto risco
- Status `quarantined` persistido e auditado
- Resolver e instalador rejeitam versões em quarentena
- Assinaturas Ed25519 vinculam publisher, manifest canônico e hash do artefato
- Trust store atômico persiste apenas chaves públicas e bloqueia chaves revogadas
- Fontes remotas sem assinatura válida entram em quarentena; local mantém compatibilidade com aviso
- Promoção exige revalidação, role `security_admin`, correlation ID e evento auditável dedicado
- 159 testes, 91,73% de cobertura, lint/formatação, tipagem e build aprovados
- Pendente: sandbox, revogação propagada e auditoria encadeada

O gate `ULTRON_SECURITY_READY` permanece pendente.
