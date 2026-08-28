# Rachel — Arquitetura de External Capabilities

**Estado:** especificacao aprovada para planejamento; implementacao ainda nao iniciada como camada transversal  
**Data:** 2026-08-27  
**Fonte transversal:** `docs/integracao/external-capabilities-public-apis.md`  
**Escopo deste documento:** somente Rachel; as fronteiras com Jarvis, ULTRON e Zane sao preservadas

---

## 1. Objetivo

Este documento traduz a especificacao transversal de `External Capabilities` para a arquitetura concreta da Rachel e define onde cada componente deve entrar no roadmap profissional sem reabrir etapas validadas de forma artificial e sem antecipar implementacoes antes de suas dependencias.

A regra central e:

> Rachel consome **capabilities de alto nivel**; providers externos permanecem atras de registries, routers, adapters e politica. O cerebro cognitivo nao deve conter clientes HTTP de fornecedores especificos.

Exemplos de capabilities que a Rachel podera consumir:

```text
models.chat
models.summarize
models.classify
weather.get_forecast
location.geocode
location.reverse_geocode
routes.calculate
research.search
research.read_url
research.search_papers
research.resolve_doi
knowledge.embed
knowledge.rerank
documents.ocr
files.convert
compute.query
audio.transcribe
calendar.public_holidays
currency.quote
```

A lista acima representa contratos internos. Ela nao significa que os providers citados na especificacao transversal ja estejam instalados ou operacionais.

---

## 2. Posicao na arquitetura da Rachel

A camada deve ficar **entre o runtime de tools/modelos e os providers externos**, nunca dentro do `NedCognitiveBridge`, do prompt ou da memoria.

Arquitetura alvo:

```text
Usuario
  |
  v
Desktop / Chat / Voz
  |
  v
Runtime Cognitivo canonico
  |
  +--> Intent Router
  +--> Agent Loop
  +--> Dany
  |
  v
Capability boundary
  |
  +--> Model Router ---------------------> local / Groq / Gemini / HF
  |
  +--> ToolCoordinator
          |
          +--> tools locais
          +--> browser governado
          +--> MCP Runtime
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
                    +--> error normalization
                    +--> timeout/retry/circuit breaker
                    |
                    +--> Jina
                    +--> Open-Meteo
                    +--> Nominatim / OpenRouteService
                    +--> arXiv / CrossRef
                    +--> OCR.Space
                    +--> WolframAlpha
                    +--> outros providers aprovados
```

O planner deve escolher `weather.get_forecast`, e nao `open_meteo.request`. O adapter de Open-Meteo e detalhe interno do Provider Router.

---

## 3. Componentes obrigatorios da camada

### 3.1 Capability Registry

Mantem contratos logicos versionados.

Campos minimos:

```yaml
id: weather.get_forecast
version: 1
domain: weather
input_schema: ForecastRequest
output_schema: ForecastResponse
providers:
  - open_meteo
policies:
  internet_required: true
  data_classification_max: PERSONAL
  cache_ttl_seconds: 900
  timeout_ms: 6000
```

O registry nao executa chamadas. Ele descreve o que existe e quais contratos devem ser obedecidos.

### 3.2 Provider Registry

Mantem metadados operacionais dos fornecedores:

- capabilities suportadas;
- base URL e dominios permitidos;
- necessidade de segredo;
- `secret_ref`;
- custo/classe de preco;
- prioridade;
- health policy;
- rate-limit metadata;
- politica de privacidade;
- versao do adapter;
- estado `enabled/disabled/degraded`.

### 3.3 Provider Router

Seleciona provider por:

- capability;
- disponibilidade;
- latencia;
- custo;
- quota/rate limit;
- regiao;
- qualidade historica;
- privacy class;
- politica do usuario;
- fallback permitido.

O Router nao pode degradar privacidade silenciosamente. Falha local nao autoriza envio de dados pessoais/sensiveis a cloud.

### 3.4 Secret Manager adapter

Segredos sao referenciados por identificador e nunca entram em:

- prompt;
- memoria;
- logs;
- manifests publicos;
- eventos comuns;
- banco em texto puro.

### 3.5 HTTP client governado

Deve centralizar:

- allowlist/domain policy;
- TLS/HTTPS;
- timeout;
- limite de tamanho;
- redirecionamentos;
- headers sanitizados;
- user-agent identificavel quando exigido;
- parsing defensivo;
- cancelamento;
- metadados de tracing.

### 3.6 Erros normalizados

Erros externos devem virar classes internas previsiveis:

```text
ProviderAuthError
ProviderRateLimitError
ProviderTimeoutError
ProviderUnavailableError
ProviderInvalidResponseError
ProviderPolicyError
ProviderQuotaExceededError
```

O Agent Loop nao deve interpretar mensagens arbitrarias de cada fornecedor.

### 3.7 Rate Limit Manager

Mantem consumo e reset conhecidos/estimados por provider. Deve impedir loops de retry quando o provider esta limitado.

### 3.8 Health Manager

Estados canonicos:

```text
healthy
degraded
rate_limited
auth_error
unavailable
disabled
unknown
```

### 3.9 Usage Ledger

Registra somente metadados necessarios:

- capability;
- provider;
- correlation id;
- timestamp;
- latencia;
- status;
- fallback;
- quota/tokens quando disponivel;
- custo estimado quando aplicavel;
- erro normalizado.

Conteudo sensivel e segredo nao entram no ledger.

### 3.10 Cache governado

Apenas capabilities de leitura com semantica segura podem usar cache. TTL e chave devem ser definidos por capability. Localizacao pessoal e outros dados pessoais exigem politica de retencao especifica.

---

## 4. Classificacao de dados antes da rede

Toda chamada externa deve receber uma classificacao:

```text
PUBLIC
INTERNAL
PERSONAL
SENSITIVE
SECRET
```

Politica minima:

- `SECRET`: nunca enviado a provider externo comum;
- `SENSITIVE`: local por padrao ou approval/politica explicita;
- `PERSONAL`: somente quando necessario e permitido;
- `INTERNAL`: provider permitido apenas conforme politica;
- `PUBLIC`: pode usar provider externo aprovado.

Essa classificacao deve ser aplicada **antes** do Provider Router selecionar um fornecedor.

---

## 5. Como as capabilities entram nas etapas existentes

As 15 etapas do roadmap permanecem fixas. A introducao desta arquitetura **nao cria uma Etapa 16**.

### Etapa 2 — Cerebro unico + Intent Router

Responsabilidade:

- reconhecer intencoes de alto nivel como clima, localizacao, research, documentos e computacao;
- produzir `capability_id` ou tool canonica;
- nunca selecionar provider concreto no cerebro.

Exemplo:

```text
"Como vai estar o tempo amanha em Contagem?"
    -> weather.get_forecast
```

Nao implementar agora; apenas incorporar ao gate quando o Capability Runtime existir.

### Etapa 4 — Tool Runtime profissional

A Etapa 4 permanece `VALIDATED` para o escopo ja fechado. O novo runtime deve **reutilizar**, e nao substituir:

- `ToolSpec`;
- validacao de schema;
- Cyber;
- ApprovalStore;
- ToolCoordinator;
- eventos;
- verificacao;
- observabilidade.

Nao reabrir a Etapa 4 apenas para colocar clientes HTTP novos.

### Etapa 5 — Agent Loop

O Agent Loop deve consumir capabilities como tools tipadas. Ele nao deve gerar requests HTTP arbitrarias quando existir adapter aprovado.

O gate de revalidacao da Etapa 5 deve incluir pelo menos uma capability externa fake para provar plan -> execute -> observe -> verify sem acoplamento a provider.

### Etapa 6 — Model Router

A arquitetura de roteamento ja validada permanece correta. A expansao prevista e adicionar adapters configuraveis para:

- Groq;
- Gemini;
- Hugging Face/inference providers quando justificado.

Groq/Gemini nao devem substituir o provider local. O `local-only` continua fail-closed.

A base da Etapa 6 nao e reaberta. Providers reais adicionais entram como **expansao de provider** dependente da fundacao da Etapa 12 e sao smoke-tested na Etapa 15.

### Etapa 8 — Dany Professional

Dany deve validar respostas derivadas de provider externo com base em:

- ToolResult/ProviderResult;
- provenance;
- freshness quando aplicavel;
- citation/evidence;
- estado de fallback;
- erro/partial result;
- ausencia de falso sucesso.

### Etapa 9 — Knowledge Port / RAG

Aqui entram as capacidades de conhecimento e RAG:

```text
knowledge.embed
knowledge.rerank
documents.extract
```

Jina pode ser provider de embeddings/reranking, mas o schema interno da Rachel nao pode depender do schema da Jina.

Arquitetura futura:

```text
Documento
  -> Visao/extractor
  -> chunks governados
  -> embedding provider abstraction
       +-- local
       +-- Jina opcional
  -> vector store local/Supabase+pgvector quando configurado
  -> retrieval
  -> reranker abstraction
       +-- local
       +-- Jina opcional
  -> KnowledgePort
  -> Context Builder
```

O SQLite KnowledgePort atual continua valido como baseline. A camada vetorial/Jina e evolucao, nao motivo para misturar memoria pessoal e RAG.

### Etapa 10 — Web Research Professional

A Etapa 10 permanece `VALIDATED` para o pipeline atual. A expansao planejada adiciona providers sob os contratos existentes:

- Jina Search;
- Jina Reader;
- arXiv;
- CrossRef;
- Wikipedia/Wikidata quando apropriado;
- providers de noticias sob `news.search/news.latest`.

Jina Reader nao substitui Browser Governado. Reader e uma fonte de conteudo para research; Browser continua necessario para estado de pagina e efeitos interativos.

### Etapa 11 — Browser Governado

Nao incorporar Jina aqui como atalho para evitar Playwright. A Etapa 11 continua responsavel por:

- page/session/tab state;
- leitura real de browser;
- click/form/login/upload/download;
- approvals de efeito;
- SSRF;
- target binding.

Pode futuramente consultar `security.scan_url` antes de navegacao/download quando a Etapa 12 existir, mas isso nao bloqueia a implementacao do boundary do browser.

### Etapa 12 — External Capability + MCP Runtime

Esta passa a ser a **etapa principal de fundacao** para a arquitetura transversal.

A Etapa 12 continua contendo MCP, mas seu escopo e ampliado de forma coerente para a camada de extensibilidade externa:

1. Capability Registry;
2. Provider Registry;
3. Provider Router;
4. Provider contract;
5. Secret Manager adapter;
6. HTTP client governado;
7. normalized errors;
8. timeouts/retries/circuit breaker;
9. Rate Limit Manager;
10. Health Manager;
11. Usage Ledger;
12. cache policy;
13. data classification;
14. MCP registry/lifecycle/discovery;
15. normalizacao de MCP tools para o mesmo ToolCoordinator;
16. adapters externos iniciais de prova.

Primeiras provas recomendadas, pequenas e deterministicas:

```text
weather.get_forecast  -> Open-Meteo
location.geocode      -> Nominatim/OSM
research.read_url     -> Jina Reader
compute.query         -> provider configuravel, se credencial/licenca permitirem
```

Nao implementar dezenas de APIs nessa etapa. O objetivo e provar a arquitetura com poucos providers de alto valor.

### Etapa 13 — Voz integrada

A camada de voz continua usando a mesma sessao cognitiva. O provider de STT deve ser abstrato:

```text
SpeechProvider
  +-- Whisper local
  +-- Groq Whisper opcional
  +-- BRAINIALL opcional, apos avaliacao
```

Cloud STT deve respeitar classificacao de dados e politica de privacidade. Audio nao deve ser enviado externamente por fallback silencioso.

### Etapa 14 — Desktop UX

A UI deve expor estado, nao detalhes desnecessarios de provider. Necessario:

- capability status;
- provider selecionado quando util para diagnostico;
- modo local/cloud;
- degradacao/rate limit;
- configuracao de providers;
- secret setup sem mostrar segredo depois de salvo;
- offline/degraded states;
- uso/fallback em diagnostico;
- localizacao somente com consentimento/escopo.

### Etapa 15 — Hardening + Release

O Gate Final deve acrescentar testes para:

- provider auth failure;
- rate limit;
- timeout;
- circuit breaker;
- fallback permitido;
- fallback proibido por privacidade;
- malformed provider response;
- secret redaction;
- capability/provider mismatch;
- localizacao pessoal;
- document upload externo;
- Jina research/RAG quando configurado;
- STT externo quando configurado;
- weather/geocoding E2E;
- offline degradation;
- provider health recovery.

---

## 6. Provider packs e prioridade na Rachel

### Core V1 / alta prioridade

Devem ser planejados como capacidades do produto, sem exigir que todo provider esteja ativo ao mesmo tempo:

| Dominio | Capability | Provider candidato inicial | Etapa de ownership |
|---|---|---|---:|
| Models | `models.chat` | local + Groq/Gemini opcionais | 6/12 |
| Research | `research.search` | engine atual + Jina Search | 10/12 |
| Web reader | `research.read_url` | Jina Reader | 10/12 |
| Embeddings | `knowledge.embed` | local/Jina | 9/12 |
| Reranking | `knowledge.rerank` | local/Jina | 9/12 |
| Weather | `weather.get_forecast` | Open-Meteo | 12 |
| Geocoding | `location.geocode` | Nominatim/OSM | 12 |
| Routes | `routes.calculate` | OpenRouteService | 12 |
| Scientific research | `research.search_papers` | arXiv | 10/12 |
| DOI | `research.resolve_doi` | CrossRef | 10/12 |
| Computation | `compute.query` | WolframAlpha ou provider equivalente | 12 |
| OCR | `documents.ocr` | local primeiro / OCR.Space opcional | 9/12 |
| STT | `audio.transcribe` | Whisper local + Groq opcional | 13/12 |

### Optional packs

Nao bloqueiam Rachel V1 salvo se futuramente promovidos por decisao arquitetural:

- currency/finance;
- news especializados;
- holidays;
- translation API;
- CloudConvert/iLovePDF;
- Notion/Google Docs connectors;
- providers secundarios de visao/NLP;
- BRAINIALL.

### Fora do core da Rachel

Nao hardcodar no Core:

- entretenimento/novelty APIs;
- dezenas de providers redundantes;
- marketplace/discovery universal;
- intake de skills/repositorios como responsabilidade principal;
- threat-intel pack completo de instalacao de skills.

Esses itens pertencem prioritariamente a ULTRON/Jarvis ou a packs futuros.

---

## 7. Fronteiras com Jarvis, ULTRON e Zane

### Rachel

Responsavel por:

- governanca pessoal;
- memoria/conhecimento;
- desktop;
- consentimento;
- contexto pessoal;
- consumo de capabilities aprovadas;
- Model Router;
- research;
- browser;
- voz;
- ferramentas locais aprovadas.

Rachel nao vira marketplace nem instalador universal de providers.

### Jarvis

Principal consumidor operacional para:

- coding;
- GitHub/dev workflows;
- repository analysis;
- skill installation;
- sandbox;
- security checks de intake;
- generated tools;
- API discovery operacional.

O security pack completo de VirusTotal/Safe Browsing/URLScan/NVD/GitGuardian para intake de skills pertence prioritariamente ao Jarvis, podendo Rachel reutilizar somente capabilities defensivas pontuais como `security.scan_url` quando aprovadas.

### ULTRON

Responsavel por distribuicao/catalogo:

- manifests de capabilities/providers;
- skills;
- agents;
- MCP servers;
- OpenAPI specs;
- generated adapters;
- versionamento;
- compatibility/security metadata.

ULTRON nao deve ser dependencia online obrigatoria para executar capabilities essenciais ja instaladas localmente.

### Zane

Recebe a camada madura consolidada. Na migracao, cada capability e provider sera reimplementado, adaptado, substituido ou descartado conforme contrato e benchmark. Zane nao deve depender de internals privados da Rachel/Jarvis/ULTRON.

---

## 8. Ordem de implementacao na Rachel

A ordem recomendada, preservando o roadmap atual, e:

```text
AGORA
  -> concluir/revalidar gates pendentes existentes
  -> concluir Etapa 11 Browser

DEPOIS
  -> Etapa 12A: Capability/Provider foundation
  -> Etapa 12B: MCP Runtime
  -> Etapa 12C: providers simples de prova
       weather + geocode + Jina Reader
  -> Etapa 12D: integrar provider abstractions aos dominios ja existentes
       Model Router / Knowledge / Research
  -> Etapa 13: SpeechProvider compartilhado
  -> Etapa 14: UX/config/diagnostico de providers
  -> Etapa 15: security/reliability/E2E/release
```

Nao iniciar Groq, Gemini, Jina embeddings, OCR cloud, geolocation ou dezenas de APIs enquanto Browser e os gates anteriores ainda estiverem abertos. Primeiro se fecha a fundacao; depois providers entram por contrato.

---

## 9. Gate minimo da Etapa 12 expandida

A etapa so pode ser `VALIDATED` quando:

- Capability Registry possui schema e versionamento;
- Provider Registry possui provider fake e providers reais de prova;
- Provider Router respeita privacidade e fallback;
- secrets sao referenciados, nunca persistidos em manifest/log/prompt;
- rate limit e health possuem estados testados;
- erros externos sao normalizados;
- retries nao duplicam efeitos;
- Usage Ledger registra metadata sem payload sensivel;
- pelo menos uma capability sem secret funciona em contract test;
- pelo menos um provider com secret e testado via fake/secret_ref;
- MCP fake e descoberto e normalizado;
- ToolCoordinator executa capability e MCP pelo mesmo boundary de Cyber;
- Agent Loop consegue planejar uma capability sem conhecer provider;
- provider indisponivel degrada de forma explicita;
- provider alternativo so e usado se privacy policy permitir;
- CI completa fica verde.

---

## 10. Documentos e RAG — decisao especifica

Jina pode melhorar Reader, Search, embeddings e reranking, mas nao muda as fronteiras fundamentais:

```text
memoria pessoal != conhecimento != dataset
```

O KnowledgePort continua sendo a unica porta cognitiva para RAG. `Supabase + pgvector` pode ser um backend futuro, nao requisito para o primeiro adapter. Embeddings sao dados e seguem a mesma governanca de privacidade do conteudo de origem.

OCR externo e conversao de arquivo somente podem receber documentos depois da classificacao de sensibilidade e politica de upload. Para documentos sensiveis, preferir processamento local ou exigir autorizacao/politica explicita.

---

## 11. Localizacao e clima — decisao especifica

Weather nao deve receber localizacao precisa desnecessaria. Fluxo recomendado:

```text
pedido do usuario
  -> resolve localizacao fornecida/consentida
  -> location.geocode (se necessario)
  -> minimiza precisao
  -> weather.get_forecast
  -> normaliza unidades/timezone
  -> apresenta fonte/provider e freshness operacional
```

A localizacao do dispositivo nao deve ser coletada silenciosamente. Se a cidade fornecida pelo usuario for suficiente, nao existe motivo para transmitir GPS preciso.

---

## 12. Research — decisao especifica

Jina entra como provider do pipeline ja validado, nao como novo motor cognitivo.

```text
ResearchQueryPlan
  -> capability research.search
       +-- provider atual
       +-- Jina Search
       +-- fonte especializada
  -> Source Normalizer
  -> fetch/read
       +-- WebClient
       +-- Jina Reader quando permitido
  -> evidence
  -> freshness/conflict
  -> synthesis contract
  -> Dany
```

O pipeline de evidencia, conflitos e freshness da Etapa 10 permanece fonte de verdade. Jina nunca autoriza bypass de Dany nem transforma conteudo recuperado em instrucao confiavel.

---

## 13. Voz — decisao especifica

STT externo e apenas provider de transcricao. Ele nao possui memoria, identity ou Agent Loop proprio.

```text
microfone
  -> SpeechProvider
  -> texto
  -> mesma conversation/session
  -> mesmo Runtime Cognitivo
  -> mesma politica/Agent Loop
```

Whisper local e o fallback de privacidade preferencial quando disponivel. Cloud STT precisa de politica explicita para audio classificado.

---

## 14. Regras de nao implementacao prematura

Antes da Etapa 12:

- nao adicionar dezenas de SDKs;
- nao adicionar chaves de providers;
- nao criar clientes HTTP dentro do `NedCognitiveBridge`;
- nao hardcodar Open-Meteo/Jina/Groq/Gemini em planner;
- nao migrar memoria para pgvector apenas para usar Jina;
- nao substituir Browser por Jina Reader;
- nao adicionar marketplace ULTRON dentro da Rachel;
- nao mover skill intake do Jarvis para Rachel;
- nao transformar free tier em requisito de producao.

---

## 15. Fonte de verdade e manutencao

Para External Capabilities na Rachel:

1. `docs/integracao/external-capabilities-public-apis.md` define a estrategia transversal;
2. este documento define a aplicacao especifica na Rachel;
3. `docs/rachel/desenvolvimento/roadmap-definitivo-professional-agent-ready.md` define a ordem/gates;
4. documentos `etapa-XX` registram implementacao e evidencia;
5. codigo + CI prevalecem sobre declaracoes antigas de conclusao.

Toda implementacao futura de provider deve atualizar documentacao com:

- capability;
- provider;
- schema;
- privacy class;
- secret handling;
- rate limit;
- timeout;
- fallback;
- health;
- testes;
- commit;
- CI;
- data de verificacao de pricing/free tier quando relevante.
