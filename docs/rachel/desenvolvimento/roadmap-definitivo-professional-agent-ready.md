# Rachel — Roadmap Definitivo para `Professional Agent Ready`

**Versao:** 1.1  
**Data:** 2026-08-27  
**Repositorio de codigo:** `restoffkaua08-afk/rachel-ia`  
**Repositorio de documentacao:** `restoffkaua08-afk/Documenta-oMeta`  
**Branch oficial de desenvolvimento:** `main`  
**Quantidade fixa de etapas:** **15**  
**Arquitetura complementar:** [`../arquitetura/external-capabilities-na-rachel.md`](../arquitetura/external-capabilities-na-rachel.md)

---

## 1. Objetivo

Este documento e o tracker oficial para responder:

> **Quanto falta para a Rachel estar realmente pronta?**

Rachel V1 so sera concluida quando **15/15 etapas estiverem `VALIDATED`** e o Gate Final da Etapa 15 estiver integralmente verde.

`VALIDATED` exige implementacao, integracao no caminho real, testes, seguranca, observabilidade e evidencia reproduzivel. Codigo existente sem gate nao conta como conclusao formal.

O roadmap continua finito. A incorporacao de External Capabilities **nao cria uma Etapa 16**. As novas capacidades foram distribuidas nas etapas tecnicamente corretas sem transformar providers externos em dependencias do cerebro cognitivo.

---

## 2. Definicao oficial de Rachel completa

```text
PROFESSIONAL_AGENT_READY = TRUE
```

A Rachel deve conseguir, de forma standalone:

- iniciar e atualizar de forma reproduzivel;
- conversar por chat persistente e streaming;
- compreender intencoes naturais;
- planejar e executar tarefas multi-etapa;
- usar ferramentas reais com Cyber/approval;
- retomar exatamente uma operacao autorizada;
- trabalhar com arquivos, Git e validacao de projetos;
- usar modelos locais e providers substituiveis;
- compreender repositorios grandes com contexto limitado;
- usar memoria e conhecimento sem misturar RAG com memoria pessoal;
- pesquisar web com evidencias, freshness e conflitos;
- navegar e interagir via browser governado;
- consumir MCP e External Capabilities por contratos internos;
- usar voz na mesma sessao cognitiva;
- expor tudo por desktop profissional;
- degradar corretamente quando provider/rede/tool falhar;
- proteger segredos, localizacao, documentos e dados pessoais;
- possuir diagnostico, logs, metricas e recovery;
- passar security, performance, E2E e release gates.

Rachel nao depende de Jarvis, ULTRON ou Zane para ser completa. ULTRON pode distribuir componentes, Jarvis pode consumir capabilities operacionais e Zane recebera a consolidacao futura, mas a V1 da Rachel deve operar com capabilities essenciais instaladas localmente.

---

## 3. Regra arquitetural de External Capabilities

A Rachel passa a adotar oficialmente o principio **capability-first**:

```text
correto: weather.get_forecast(location, days)
incorreto: open_meteo.get_forecast(...)
```

Fluxo alvo:

```text
Runtime Cognitivo / Agent Loop
          |
          v
Capability/Tool contract
          |
          v
ToolCoordinator + Cyber
          |
          +--> local tools
          +--> browser
          +--> MCP
          +--> External Capability Runtime
                    |
                    +--> Capability Registry
                    +--> Provider Registry
                    +--> Provider Router
                    +--> Secret Manager adapter
                    +--> Rate Limit Manager
                    +--> Health Manager
                    +--> Usage Ledger
                    +--> HTTP client governado
                    +--> retry/circuit breaker
                    +--> normalized errors
                    |
                    +--> providers aprovados
```

Nenhum provider externo deve ser hardcoded no `NedCognitiveBridge`, prompt, memoria ou planner.

---

## 4. Estados permitidos

| Estado | Significado |
|---|---|
| `VALIDATED` | Implementado, integrado, testado e gate comprovado. |
| `IMPLEMENTED_REVALIDATION_REQUIRED` | Codigo substancial existe, mas o gate formal precisa ser reconciliado. |
| `IN_PROGRESS` | Implementacao ativa. |
| `PARTIAL` | Pecas existem, etapa ainda incompleta. |
| `PLANNED` | Especificada, sem conclusao de codigo. |
| `BLOCKED` | Dependencia/regressao impede avanco. |

External Capabilities nao alteram retroativamente um gate validado apenas por adicionar providers novos. Expansoes entram como subgates futuros e sao validadas nas etapas de ownership e no Gate 15.

---

## 5. Snapshot atual

| # | Etapa | Estado atual | Observacao |
|---:|---|---|---|
| 1 | CI profissional | `VALIDATED` com higiene pendente | `RACHEL CI` e referencia; workflow legado `tests` ainda precisa reconciliar timeout. |
| 2 | Cerebro unico + Intent Router | `IMPLEMENTED_REVALIDATION_REQUIRED` | Deve incorporar routing futuro por `capability_id`, sem provider concreto. |
| 3 | Runtime persistente + streaming | `IMPLEMENTED_REVALIDATION_REQUIRED` | Backend residente/streaming existem; gate final precisa consolidacao. |
| 4 | Tool Runtime profissional | `VALIDATED` | Boundary sera reutilizado por MCP/External Capabilities; nao reabrir. |
| 5 | Agent Loop real | `IMPLEMENTED_REVALIDATION_REQUIRED` | Revalidacao deve incluir capability fake sem acoplamento a provider. |
| 6 | Model Router | `VALIDATED` | Core validado; Groq/Gemini/HF entram como expansoes configuraveis depois da fundacao externa. |
| 7 | Project Intelligence | `VALIDATED` | Sem mudanca de ownership. |
| 8 | Dany Professional | `VALIDATED` | Deve continuar governando evidencias de providers externos. |
| 9 | Knowledge Port / RAG | `IMPLEMENTED_REVALIDATION_REQUIRED` | Baseline real existe; Jina embeddings/reranking e vector backend entram como expansao posterior. |
| 10 | Web Research Professional | `VALIDATED` | Jina Search/Reader, arXiv e CrossRef entram como providers do pipeline, sem substituir Dany/evidence layer. |
| 11 | Browser governado | `IN_PROGRESS` | Continua independente de Jina Reader; session/tab/effects ainda obrigatorios. |
| 12 | External Capability + MCP Runtime | `PLANNED` | Etapa ampliada para fundacao de registries/providers + MCP, preservando o papel original de extensibilidade. |
| 13 | Voz integrada ao Agent Loop | `PARTIAL` | SpeechProvider local/cloud passa a ser contrato; cloud STT sem fallback silencioso. |
| 14 | Desktop UX profissional | `PARTIAL` | Deve incluir status/config/diagnostico de capabilities/providers. |
| 15 | Hardening + Release | `PLANNED/PARTIAL` | Passa a incluir falhas, privacidade e E2E de providers externos. |

### Progresso formal

- `VALIDATED`: **6/15 = 40%**;
- maturidade de engenharia estimada: **aprox. 60–65%**;
- o novo plano nao muda artificialmente esse percentual, pois nenhuma External Capability foi marcada como implementada apenas por existir na documentacao.

---

# 6. As 15 etapas definitivas

## ETAPA 1 — CI profissional e rede de seguranca

### Obrigatorio

- Core test suite;
- Runtime regressions;
- config validation;
- frontend production build;
- Tauri check/build;
- nenhum falso verde;
- workflows oficiais verdes no release.

### Gate

Rede de CI confiavel e release sem workflow oficial permanentemente vermelho.

---

## ETAPA 2 — Cerebro unico + Intent Router

### Objetivo

Um unico caminho cognitivo canonico para chat e acoes.

### Obrigatorio

- fast path;
- planner tipado;
- intents deterministicas quando confiaveis;
- tool/capability IDs validos;
- argumentos validados;
- approval retoma plano exato;
- capabilities reais refletidas no status;
- intent de clima/localizacao/documento/research deve apontar para capability abstrata, nunca provider.

### External Capability subgate

Quando a Etapa 12 existir, testar exemplos como:

```text
"previsao do tempo" -> weather.get_forecast
"onde fica ..."     -> location.geocode
"leia esta pagina"  -> research.read_url ou browser.read conforme intencao
```

### Gate

E2E chega ao dominio/capability correto sem o usuario conhecer nomes internos.

---

## ETAPA 3 — Runtime persistente, streaming e cancelamento

### Obrigatorio

- processo residente;
- IPC estavel;
- streaming/cancelamento;
- session continuity;
- health/readiness;
- graceful shutdown;
- metricas de model/tool/provider separadas quando aplicavel.

### External Capability subgate

Chamadas externas devem herdar correlation id, deadline/cancelamento e nao bloquear o runtime inteiro.

---

## ETAPA 4 — Tool Runtime profissional

### Estado

`VALIDATED` para o escopo atual.

### Regra para novas capabilities

Reutilizar:

```text
ToolSpec -> schema -> Cyber -> ApprovalStore -> ToolCoordinator -> executor -> verify -> ToolResult
```

External Capabilities e MCP nao criam um executor paralelo fora desse boundary.

---

## ETAPA 5 — Agent Loop real

### Obrigatorio

- persisted runs;
- budgets;
- plan -> execute -> observe -> verify;
- dependencies;
- approval pause/resume;
- cancellation;
- retries limitados;
- checkpoint;
- no self-approval;
- terminal failure controlada.

### External Capability subgate

O Agent Loop pede `capability_id`, nao request HTTP. Revalidacao deve provar uma capability fake com provider fake, incluindo erro normalizado e verificacao.

---

## ETAPA 6 — Model Router

### Estado

Base `VALIDATED`.

### Ownership externo

Adicionar futuramente, por configuracao:

- Groq: low-latency chat/classify/summarize e Whisper quando aplicavel;
- Gemini: multimodal/reasoning/coding;
- Hugging Face/inference providers: diversidade de modelos quando justificada.

O provider local continua primeira classe. `local-only` permanece fail-closed e provider cloud nunca recebe dado sensivel apenas porque local falhou.

### Dependencia

Adapters reais adicionais devem usar os contratos/health/secret/usage da Etapa 12 quando a fundacao existir. Smoke real fica na Etapa 15.

---

## ETAPA 7 — Project Intelligence

### Estado

`VALIDATED`.

### Regra

External Capabilities nao alteram o boundary de contexto. Research/API output entra somente como evidencia/contexto limitado quando solicitado; nunca como dump irrestrito.

---

## ETAPA 8 — Dany Professional

### Estado

`VALIDATED`.

### External Capability extension

Dany deve considerar:

- provider result;
- provenance;
- fallback;
- freshness;
- citations;
- partial/error state;
- normalized error;
- ausencia de falso sucesso.

Provider externo nao e fonte automaticamente confiavel.

---

## ETAPA 9 — Knowledge Port real + RAG evolutivo

### Obrigatorio baseline

- adapter real;
- somente `document_chunk` como conhecimento;
- memoria pessoal isolada;
- evidence injection;
- capability real no status;
- DB ausente seguro;
- Visao -> storage -> KnowledgePort -> ChatService.

### Expansao planejada

```text
knowledge.embed
knowledge.rerank
documents.extract
documents.ocr
```

Arquitetura:

```text
Documento
 -> Visao/extractor
 -> chunks governados
 -> EmbeddingProvider (local/Jina opcional)
 -> vector backend (local ou Supabase+pgvector configurado)
 -> retrieval
 -> RerankProvider (local/Jina opcional)
 -> KnowledgePort
```

Regras:

- Jina nao define schema interno;
- embedding tambem e dado e segue privacy policy;
- OCR cloud exige classificacao de documento;
- SQLite atual continua baseline valida;
- pgvector nao e pre-requisito para fechar o adapter local.

---

## ETAPA 10 — Web Research Professional

### Estado

Pipeline principal `VALIDATED`.

### Expansao de providers

Capabilities:

```text
research.search
research.read_url
research.search_papers
research.resolve_doi
news.search
news.latest
```

Providers candidatos:

- Jina Search/Reader;
- arXiv;
- CrossRef;
- Wikipedia/Wikidata;
- news provider aprovado quando realmente necessario.

Todos entram por `Source Normalizer` e pelo pipeline de evidence/freshness/conflict ja existente.

**Jina Reader nao substitui Browser Governado.** Research leitura e browser interativo possuem semanticas diferentes.

---

## ETAPA 11 — Browser Governado

### Obrigatorio

- backend Playwright reproduzivel;
- SSRF em URL inicial/redirect/subrequest;
- open/title/read;
- session/tab state;
- click/form/login/upload/download;
- approval ligado a target+tool+args;
- approval one-shot;
- routing natural;
- smoke real.

### External Capability relation

Depois da Etapa 12, browser pode consultar `security.scan_url` antes de download/navegacao conforme policy. Isso e enriquecimento, nao dependencia para concluir o boundary basico.

---

## ETAPA 12 — External Capability + MCP Runtime

### Objetivo

Criar a camada de extensibilidade externa padronizada da Rachel, abrangendo MCP e APIs/providers aprovados sem acoplamento ao Core cognitivo.

### 12A — Capability/Provider foundation

Obrigatorio:

- Capability Registry versionado;
- Provider Registry;
- `CapabilityProvider` contract;
- Provider Router;
- ExecutionContext com correlation/deadline/privacy/cost/authorization;
- Secret Manager adapter;
- HTTP client governado;
- output schema validation;
- normalized errors;
- timeout;
- retry seguro;
- circuit breaker;
- Rate Limit Manager;
- Health Manager;
- Usage Ledger;
- cache policy;
- data classification `PUBLIC/INTERNAL/PERSONAL/SENSITIVE/SECRET`.

### 12B — MCP Runtime

- registry/config de servidores;
- connect/disconnect lifecycle;
- discovery;
- schema normalization;
- stable namespace;
- health/timeouts;
- Cyber mapping;
- approvals;
- allowlist/trust policy;
- secrets fora de logs/prompts;
- fake MCP server para CI;
- Agent Loop usando MCP via ToolCoordinator.

### 12C — Primeiros providers de prova

Implementar poucos adapters para provar arquitetura, nesta ordem preferencial:

1. `weather.get_forecast` -> Open-Meteo;
2. `location.geocode` -> Nominatim/OSM;
3. `research.read_url` -> Jina Reader;
4. `routes.calculate` -> OpenRouteService quando contrato base estiver estavel.

Depois, conectar providers especializados aos dominios 6/9/10/13.

### 12D — Criterio de aceite de provider

Uma API so entra como `enabled` se possuir:

- utilidade clara;
- HTTPS;
- auth compreendida;
- secret handling;
- rate limit tratado;
- timeout/retry seguro;
- schema de output;
- normalized error;
- privacy classification;
- health;
- observabilidade;
- unit test;
- contract/integration test;
- fallback quando critica;
- data de verificacao de pricing/free tier quando relevante.

### Gate

- capability fake e provider fake em CI;
- pelo menos uma capability real sem secret em contract test;
- um provider com `secret_ref` testado sem expor segredo;
- fallback respeita privacy;
- rate limit/timeout/circuit breaker testados;
- MCP fake descoberto/executado;
- ToolCoordinator/Cyber governam ambos;
- Agent Loop escolhe capability, nao provider;
- CI completa verde.

---

## ETAPA 13 — Voz integrada ao mesmo Agent Loop

### Obrigatorio

- wake/STT/TTS quando habilitados;
- mesma session do chat;
- mesmo runtime/Agent Loop;
- approvals identicos;
- barge-in/cancel;
- fallback de dispositivo;
- adapters fake;
- smoke hardware alvo.

### SpeechProvider

```text
Whisper local
Groq Whisper opcional
BRAINIALL opcional apos avaliacao
```

Audio classificado nao pode migrar para cloud por fallback silencioso.

---

## ETAPA 14 — Desktop UX profissional

### Obrigatorio

- streaming/history;
- plan/run viewer;
- tool cards;
- approvals;
- diff viewer;
- cancel/pause/resume;
- diagnostics;
- model/provider status;
- browser state;
- settings;
- offline/degraded states;
- accessibility minima;
- production build/Tauri.

### External Capability UX

- capability status;
- provider ativo quando util para diagnostico;
- local/cloud policy;
- degraded/rate-limited/auth-error state;
- secret setup sem reexibir segredo;
- usage/fallback diagnostics;
- localizacao somente com consentimento/escopo;
- nenhuma UI anuncia provider/capability inexistente.

---

## ETAPA 15 — Hardening, E2E e Release

### 15A — Security

Testar:

- path traversal;
- SSRF;
- prompt/tool injection;
- approval replay/mismatch;
- secret leakage;
- unsafe shell fallback;
- malicious MCP schema;
- browser target mismatch;
- provider auth failure;
- capability/provider mismatch;
- fallback proibido por privacidade;
- localizacao pessoal enviada sem necessidade;
- documento sensivel enviado para OCR/conversion cloud sem policy;
- malformed provider response.

### 15B — Reliability/recovery

- restart/crash recovery;
- migrations;
- cancellation/timeouts;
- model/tool/browser/MCP/provider failure;
- rate limit recovery;
- circuit breaker;
- offline degradation;
- no false success.

### 15C — Performance

Medir no hardware alvo:

- startup/TTFT/total;
- planner/tool/provider latency;
- project context build;
- memory/knowledge lookup;
- external capability p50/p95;
- RAM/CPU;
- long-session growth.

### 15D — E2E profissional

Obrigatorio provar:

1. conversa persistente;
2. streaming/cancel;
3. memoria/forget;
4. knowledge documental;
5. research com citation/freshness/conflict;
6. project intelligence;
7. filesystem/Git/dev validation;
8. Agent Loop + approval resume;
9. browser read/effect;
10. MCP tool;
11. External Capability sem secret (`weather`);
12. geocode com dado minimizado;
13. provider failure + fallback permitido;
14. provider failure + fallback bloqueado por privacy;
15. voz/shared session;
16. desktop end-to-end;
17. restart/recovery;
18. doctor/health;
19. provider/usage diagnostics;
20. no false success.

### 15E — Release engineering

- semver/release notes;
- installer Windows reproduzivel;
- onboarding/dependencies;
- `.env.example` sem segredos;
- migration/uninstall seguro;
- checksums quando aplicavel;
- install/troubleshooting docs;
- RC congelado;
- `v1.0.0` somente apos gate.

### 15F — Rachel Model

Pesos proprios continuam opcionais. Rachel V1 deve permanecer provider-agnostic.

---

# 7. Packs e prioridade

## Core/high priority

| Dominio | Capability | Providers candidatos | Ownership |
|---|---|---|---|
| Models | `models.chat` | local, Groq, Gemini, HF | 6/12 |
| Research | `research.search` | atual, Jina Search | 10/12 |
| Reader | `research.read_url` | WebClient, Jina Reader | 10/12 |
| RAG | `knowledge.embed/rerank` | local, Jina | 9/12 |
| Weather | `weather.get_forecast` | Open-Meteo | 12 |
| Location | `location.geocode` | Nominatim/OSM | 12 |
| Routes | `routes.calculate` | OpenRouteService | 12 |
| Papers | `research.search_papers` | arXiv | 10/12 |
| DOI | `research.resolve_doi` | CrossRef | 10/12 |
| Compute | `compute.query` | WolframAlpha/equivalente | 12 |
| OCR | `documents.ocr` | local, OCR.Space opcional | 9/12 |
| STT | `audio.transcribe` | Whisper local, Groq opcional | 13/12 |

## Optional packs

- currency/finance;
- holidays;
- translation;
- news especializado;
- CloudConvert/iLovePDF;
- workspace connectors;
- optional vision/NLP providers;
- BRAINIALL.

Nao bloqueiam V1 salvo decisao futura explicita.

---

# 8. Fronteiras de projeto

## Rachel

Governanca pessoal, memoria/conhecimento, desktop, consentimento, models, research, browser, voz e consumo de capabilities aprovadas.

## Jarvis

Runtime operacional, coding/dev, repository analysis, skill installation, sandbox, security intake, generated tools e API discovery operacional. O security pack completo de intake permanece prioritariamente aqui.

## ULTRON

Registry/distribuicao de skills, agents, tools, MCP, provider manifests, OpenAPI specs, adapters, versionamento e compatibility/security metadata. Nao monopoliza execucao.

## Zane

Produto consolidado futuro. Recebe contratos maduros, nao internals privados dos predecessores.

---

# 9. Ordem de execucao atualizada

```text
1. reconciliar CI legado e gates 2/3/5/9
2. concluir Etapa 11 Browser
3. Etapa 12A Capability/Provider foundation
4. Etapa 12B MCP Runtime
5. Etapa 12C providers simples: weather/geocode/Jina Reader
6. conectar providers especializados a Model Router / Knowledge / Research
7. Etapa 13 SpeechProvider + shared session
8. Etapa 14 UX de capabilities/providers
9. Etapa 15 hardening + E2E + release
```

### Regra de nao implementacao prematura

Antes da fundacao da Etapa 12:

- nao adicionar SDKs de dezenas de APIs;
- nao adicionar chaves reais;
- nao hardcodar providers no planner;
- nao mover memoria para pgvector apenas por Jina;
- nao substituir Browser por Jina Reader;
- nao trazer marketplace ULTRON para dentro da Rachel;
- nao mover skill intake do Jarvis para Rachel;
- nao assumir free tier como infraestrutura permanente.

---

# 10. Gate Final — `Professional Agent Ready`

Somente quando:

```text
15 / 15 etapas = VALIDATED
```

E:

- [ ] `main` e fonte oficial;
- [ ] nenhuma P0/P1 aberta;
- [ ] workflows oficiais verdes;
- [ ] frontend/Tauri/installer passam;
- [ ] provider real local testado;
- [ ] External Capability Registry/Router testado;
- [ ] weather/geocode E2E passa;
- [ ] provider failure/fallback/privacy gates passam;
- [ ] knowledge/research/Jina adapters configurados nao quebram evidencias;
- [ ] voz usa mesma sessao;
- [ ] browser e MCP passam;
- [ ] security/recovery/performance suites passam;
- [ ] secrets nao aparecem em repo/log/prompt;
- [ ] localizacao/documentos obedecem privacy policy;
- [ ] nenhuma capability critica e hardcoded `true` sem backend;
- [ ] nenhuma tool/provider declara sucesso sem evidencia;
- [ ] documentacao corresponde ao codigo;
- [ ] release candidate congelado;
- [ ] `v1.0.0` criada somente depois do RC validado.

Entao:

```text
RACHEL_STATUS = PROFESSIONAL_AGENT_READY
RACHEL_VERSION = 1.0.0
ACTIVE_FEATURE_DEVELOPMENT = FROZEN
```

---

# 11. Regra de acompanhamento

Cada etapa mantem:

```text
STATE
LAST_VALIDATED_COMMIT
LAST_VALIDATED_CI
OPEN_GATE_ITEMS
```

Toda capability/provider novo deve documentar:

```text
CAPABILITY_ID
PROVIDER_ID
SCHEMA_VERSION
PRIVACY_CLASS
SECRET_REF
RATE_LIMIT_POLICY
TIMEOUT
FALLBACK
HEALTH
TESTS
LAST_VERIFIED_PRICING_DATE (quando aplicavel)
```

Nao elevar percentual por quantidade de codigo. O tracker sobe quando gates fecham.

---

# 12. Fontes de verdade

Ordem:

1. codigo atual;
2. testes/CI atuais;
3. este roadmap;
4. `docs/rachel/arquitetura/external-capabilities-na-rachel.md` para a arquitetura especifica;
5. `docs/integracao/external-capabilities-public-apis.md` para estrategia transversal;
6. documentos historicos.

Os documentos `etapa-XX` continuam registrando implementacao e evidencia detalhada.
