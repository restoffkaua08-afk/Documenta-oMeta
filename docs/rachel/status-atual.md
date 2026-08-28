# Rachel - Status Atual

**Data da consolidacao:** 2026-08-27  
**Branch oficial:** `main`  
**Tracker principal:** [`desenvolvimento/roadmap-definitivo-professional-agent-ready.md`](desenvolvimento/roadmap-definitivo-professional-agent-ready.md)  
**Arquitetura de External Capabilities:** [`arquitetura/external-capabilities-na-rachel.md`](arquitetura/external-capabilities-na-rachel.md)

## Resumo executivo

A Rachel possui um roadmap profissional **finito de 15 etapas**. Ela so sera considerada completa quando as 15 estiverem `VALIDATED` e o Gate Final da Etapa 15 estiver integralmente verde.

A especificacao transversal `docs/integracao/external-capabilities-public-apis.md` foi incorporada ao planejamento da Rachel sem criar uma nova etapa e sem marcar nenhuma API como implementada apenas por existir na documentacao.

A decisao arquitetural e **capability-first**: o Runtime Cognitivo/Agent Loop solicita contratos como `weather.get_forecast`, `research.read_url` ou `location.geocode`; providers concretos ficam atras de Capability Registry, Provider Registry/Router, politica, secrets, health e observabilidade.

## Snapshot atual

| Estado | Quantidade |
|---|---:|
| `VALIDATED` | 6 |
| `IMPLEMENTED_REVALIDATION_REQUIRED` | 4 |
| `IN_PROGRESS` | 1 |
| `PARTIAL` | 2 |
| `PLANNED` | 2 |
| **TOTAL** | **15** |

Etapas formalmente `VALIDATED`: **1, 4, 6, 7, 8 e 10**.

Etapas com implementacao substancial que precisam fechar/reconciliar gate: **2, 3, 5 e 9**.

Etapa ativa: **11 — Browser governado**.

A **Etapa 12** passa a ser denominada **External Capability + MCP Runtime**, mantendo o objetivo original de extensibilidade e acrescentando a fundacao necessaria para APIs/providers externos.

## External Capabilities — plano aprovado, nao implementado

### Fundacao na Etapa 12

- Capability Registry;
- Provider Registry;
- Provider Router;
- contrato `CapabilityProvider`;
- Secret Manager adapter;
- HTTP client governado;
- normalized errors;
- timeouts/retries/circuit breaker;
- Rate Limit Manager;
- Health Manager;
- Usage Ledger;
- cache policy;
- classificacao `PUBLIC/INTERNAL/PERSONAL/SENSITIVE/SECRET`;
- MCP normalizado para o mesmo ToolCoordinator/Cyber.

### Primeiras provas recomendadas

1. `weather.get_forecast` -> Open-Meteo;
2. `location.geocode` -> Nominatim/OSM;
3. `research.read_url` -> Jina Reader;
4. `routes.calculate` -> OpenRouteService depois da fundacao estabilizar.

### Expansoes nos dominios corretos

- Etapa 6 / Model Router: Groq, Gemini e Hugging Face como providers configuraveis;
- Etapa 9 / Knowledge-RAG: Jina embeddings/reranking, vector backend e OCR por provider abstrato;
- Etapa 10 / Research: Jina Search/Reader, arXiv, CrossRef e fontes especializadas;
- Etapa 13 / Voz: Whisper local + Groq Whisper/BRAINIALL opcionais;
- Etapa 14 / Desktop: status/config/diagnostico de capability/provider;
- Etapa 15: security, privacy, rate-limit, fallback, offline e E2E.

## Fronteiras preservadas

- **Rachel:** governanca pessoal, memoria/conhecimento, desktop, models, research, browser, voz e consumo de capabilities aprovadas;
- **Jarvis:** coding/dev, repository analysis, skill installation, sandbox, security intake, generated tools e discovery operacional;
- **ULTRON:** catalogo/distribuicao/versionamento de providers, skills, agents, MCP, OpenAPI e adapters; nao monopoliza execucao;
- **Zane:** recebe a camada madura consolidada por contratos versionados, sem depender de internals privados dos predecessores.

O security pack completo para intake de skills/repositorios permanece prioritariamente em Jarvis/ULTRON. Rachel pode reutilizar capabilities defensivas pontuais, mas nao vira marketplace nem instalador universal.

## Progresso

- progresso formal por gates: **6/15 = 40%**;
- maturidade de engenharia estimada: **aprox. 60–65%**;
- o novo plano de External Capabilities **nao altera esse percentual**, pois ainda e planejamento arquitetural e nao evidencia de implementacao.

## Alerta de CI atual

O workflow profissional `RACHEL CI` continua sendo a referencia principal. O workflow legado `tests` apresentou timeout em `test_agent_bridge.py::AgentBridgeTests::test_dashboard_contains_agent_status`, enquanto o Core do mesmo run passou 78/78 testes.

Esse timeout permanece pendencia obrigatoria. A V1 nao sera declarada pronta com workflow oficial permanentemente vermelho ou falha obrigatoria mascarada.

## Ordem imediata

1. corrigir/reconciliar a CI legada;
2. fechar gates 2, 3, 5 e 9;
3. concluir Etapa 11 Browser sem usar Jina como substituto de browser;
4. iniciar Etapa 12A Capability/Provider foundation;
5. executar Etapa 12B MCP Runtime;
6. adicionar apenas providers simples de prova;
7. integrar providers especializados a Model Router, Knowledge/RAG e Research;
8. concluir Etapa 13 voz/shared session;
9. concluir Etapa 14 UX;
10. executar Etapa 15 e congelar release candidate.

## Definicao curta de pronto

```text
PROFESSIONAL_AGENT_READY = TRUE
```

somente com:

- 15/15 etapas `VALIDATED`;
- CI oficial verde;
- Agent Loop, tools, browser, MCP e External Capabilities funcionais;
- knowledge/research/RAG governados;
- providers externos sem degradacao silenciosa de privacidade;
- voz na mesma sessao;
- desktop/installer reproduziveis;
- security/recovery/performance/E2E aprovados;
- nenhuma capability anunciada sem backend real;
- nenhuma acao/provider declarado como sucesso sem evidencia.

Para criterios completos, usar o [`roadmap definitivo`](desenvolvimento/roadmap-definitivo-professional-agent-ready.md).
