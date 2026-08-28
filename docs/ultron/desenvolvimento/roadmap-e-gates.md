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

### U3 — ✅ Aprovado (`5627b1f`)

- Pipeline determinístico de validação sem execução de pacotes
- SHA-256 obrigatório e proveniência Git/OCI fixada por commit ou digest
- Detecção de risco subestimado e aviso de aprovação para alto risco
- Status `quarantined` persistido e auditado
- Resolver e instalador rejeitam versões em quarentena
- Assinaturas Ed25519 vinculam publisher, manifest canônico e hash do artefato
- Trust store atômico persiste apenas chaves públicas e bloqueia chaves revogadas
- Fontes remotas sem assinatura válida entram em quarentena; local mantém compatibilidade com aviso
- Promoção exige revalidação, role `security_admin`, correlation ID e evento auditável dedicado
- Revogação desativa atomicamente a versão e dependentes transitivos, preservando independentes e lockfile
- Auditoria encadeada por SHA-256 detecta alteração, reordenação e remoção de eventos
- Estado separado de cabeça e contagem detecta truncamento; migração reconstrói a cadeia histórica
- Contrato de sandbox separado do Registry com backend substituível
- `process.spawn` exige concessão explícita; aprovações pendentes falham de forma fechada
- Rede desligada por padrão e liberada somente quando declarada e concedida
- Imagens fixadas por digest SHA-256 e limites obrigatórios de tempo, memória, CPU, processos e saída
- 168 testes, 91,51% de cobertura, lint/formatação, tipagem e build aprovados

Gate `ULTRON_SECURITY_READY` aprovado para os contratos locais. O backend cloud será conectado a workers isolados fora das funções web.


### U4 — ✅ Aprovado para contratos locais (`30e0698`)

- Store de dados isolado por organização, namespace e chave
- Toda operação exige contexto explícito; não existe consulta global
- Identificadores bloqueiam traversal e formatos ambíguos
- Lineage somente entre registros existentes no mesmo isolamento
- JSON canônico e determinístico
- Projeção determinística de nós e arestas com raízes e profundidade limitada
- Travessia permanece isolada por organização e namespace
- Grafo operacional une manifests, dependências, consumers e instalações
- Relações distinguem dependência declarada, versão resolvida e instalação
- Consumers desconhecidos falham de forma fechada
- `GET /api/v1/graph` expõe contrato JSON versionado com filtros
- Portal `/graph` reutiliza exatamente a projeção operacional da API
- Busca compartilhada por ID, nome e versão, com tipo, relação e limite combináveis
- Retenção por expiração explícita usa plano imutável e aplicação por chaves exatas
- Lineage é limpo atomicamente e planos não atravessam organizações
- Blueprint Supabase equivalente inclui records, lineage, expiração, RLS e grants explícitos
- Índices cobrem foreign keys, projeção, isolamento e retenção
- Escala comprovada com 10.000 nós e 9.999 arestas, busca limitada e sem arestas órfãs
- 186 testes, 92,61% de cobertura, lint, tipagem e build aprovados

Gate `ULTRON_GRAPH_READY` aprovado para os contratos locais. Aplicação da migration, Advisors e testes RLS ocorrerão quando o projeto Supabase for conectado.


### U5 — ✅ Aprovado (`a34aeaf`)

- Catálogo remoto gera snapshot local atômico, determinístico e protegido por SHA-256
- Falha de rede usa o último snapshot íntegro; adulteração falha de forma fechada
- Ausência de servidor e cache produz erro tipado, nunca catálogo vazio silencioso
- Contrato neutro para Claude, Codex, Zane e consumers próprios
- Consumer inicia e preserva capacidades nativas sem depender do Ultron
- Adapter resiliente universal bloqueia install/activate/deactivate/remove offline
- E2E prova cache disponível, ferramentas nativas intactas e nenhuma mutação parcial
- Fonte única mapeia cada método Python para REST e MCP sem divergência
- Todas as mutações exigem confirmação; `/api/v1/protocol` expõe o contrato versionado
- Servidor MCP Streamable HTTP stateless executável na URL canônica `/mcp/`
- Cliente oficial MCP negocia sessão real com o Ultron executado por Uvicorn
- Descoberta HTTP expõe exatamente as oito operações e reutiliza o Registry da API
- Lifecycle persistente e transacional isolado por organização e consumer
- E2E instala e ativa uma capability sem executar código no servidor web
- A mesma prova confirma isolamento entre organizações pelo transporte real
- Conflitos fazem rollback; ativação é idempotente; remoção protege dependências
- Blueprint Supabase impede associação de consumer entre organizações por chave composta
- 208 testes, 91,66% de cobertura, lint, formatação, tipagem e build aprovados

Gate `ULTRON_ZANE_COMPATIBLE` aprovado. Zane, Claude, Codex e outros consumers podem usar o mesmo contrato, enquanto a indisponibilidade do Ultron não impede a inicialização nem afeta capacidades nativas.
