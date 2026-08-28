# External Capabilities e Public API Registry do Projeto Meta

## Estado documental

- Estado: Planejado
- Tipo: especificacao transversal de arquitetura e implementacao
- Escopo: Rachel, Jarvis, ULTRON e Zane
- Fonte de descoberta inicial: https://github.com/public-apis/public-apis
- Data da avaliacao inicial: 2026-08-27
- Regra principal: este documento define uma estrategia de integracao. Ele nao e evidencia de que qualquer provider abaixo ja esteja implementado ou operacional.

---

## 1. Objetivo

O repositorio `public-apis/public-apis` funciona como um catalogo comunitario de APIs publicas organizadas por dominio. Para o Projeto Meta, o valor desse catalogo nao esta em integrar centenas de APIs diretamente no codigo, mas em utiliza-lo como fonte de descoberta para uma camada estruturada de capacidades externas.

A direcao arquitetural recomendada e criar no ecossistema Meta um `External Capability Layer`, sustentado por um `Capability Registry` e por um `Provider Router`. A IA ou agente pede uma capacidade sem precisar conhecer o fornecedor concreto utilizado.

Exemplo conceitual:

```text
Usuario
  |
  v
Rachel / Jarvis / Zane
  |
  v
Capability Router
  |
  +--> weather.get_forecast
  +--> search.web
  +--> security.scan_url
  +--> finance.exchange_rate
  +--> documents.ocr
  +--> models.generate
  +--> research.search_papers
  |
  v
Provider Router
  |
  +--> Open-Meteo
  +--> Jina
  +--> VirusTotal
  +--> AwesomeAPI
  +--> OCR.Space
  +--> Groq
  +--> Gemini
  +--> arXiv
```

A abstracao evita acoplamento entre o agente e APIs especificas, facilita fallback, substituicao de provider, controle de custos, observabilidade, seguranca e expansao futura.

---

## 2. Principios arquiteturais

### 2.1 Capability-first

O codigo consumidor deve depender de capacidades, e nao de fornecedores.

Exemplo correto:

```text
weather.get_forecast(location, days)
```

Exemplo a evitar:

```text
open_meteo.get_forecast(...)
```

A chamada especifica ao provider deve permanecer dentro da camada de integracao.

### 2.2 Provider substituivel

Toda capability relevante deve poder ter um provider primario e zero ou mais providers de fallback.

### 2.3 ULTRON opcional

O ULTRON pode atuar como registry, marketplace, catalogo, sistema de descoberta e distribuicao de tools, skills e adapters, mas nao deve virar dependencia obrigatoria para que Rachel, Jarvis ou Zane executem capacidades essenciais.

### 2.4 Seguranca antes de conveniencia

Nenhuma API externa deve ser adicionada somente por ser gratuita. Toda integracao precisa considerar autenticacao, tratamento de secrets, termos de uso, privacidade, rate limits, confiabilidade, disponibilidade e risco de supply chain.

### 2.5 Free tier nao significa infraestrutura garantida

Providers gratuitos podem alterar limites, remover planos ou impor restricoes. O Meta deve tratar free tiers como oportunidade de custo, nunca como garantia arquitetural permanente.

### 2.6 Descoberta e instalacao nao podem ser equivalentes

Encontrar uma API nao autoriza instala-la. Descoberta, avaliacao, validacao, sandbox, registro e ativacao devem ser etapas distintas.

---

## 3. Componentes propostos

### 3.1 Capability Registry

Responsavel por armazenar capacidades disponiveis e seus contratos logicos.

Campos recomendados:

```yaml
id: weather.get_forecast
domain: weather
version: 1
status: active
input_schema: ForecastRequest
output_schema: ForecastResponse
providers:
  - open_meteo
  - hg_weather
policies:
  internet_required: true
  personal_data: false
  cache_ttl_seconds: 900
  timeout_ms: 6000
```

### 3.2 Provider Registry

Mantem metadados tecnicos de fornecedores.

```yaml
id: open_meteo
capabilities:
  - weather.get_forecast
base_url: https://api.open-meteo.com
requires_secret: false
cost_class: free
health_check: configured
priority: 100
```

### 3.3 Provider Router

Seleciona o provider com base em:

- disponibilidade;
- latencia;
- custo;
- rate limit restante;
- regiao;
- qualidade;
- capacidade pedida;
- politica de privacidade;
- preferencias do usuario;
- necessidade de fallback;
- tipo de dado enviado.

### 3.4 Secret Manager

Chaves de API nunca devem ser persistidas em codigo, manifests publicos, banco sem criptografia ou prompts. O runtime deve referenciar secrets por identificador.

Exemplo:

```yaml
secret_ref: providers.groq.api_key
```

### 3.5 Rate Limit Manager

Deve registrar por provider:

- requests por minuto;
- requests por dia;
- tokens por minuto;
- tokens por dia;
- reset conhecido;
- consumo atual;
- limite estimado restante.

### 3.6 Health Manager

Precisa identificar providers degradados e impedir loops de erro.

Estados sugeridos:

```text
healthy
degraded
rate_limited
auth_error
unavailable
disabled
unknown
```

### 3.7 Usage Ledger

Toda chamada externa relevante deve gerar metadados de auditoria, sem registrar secrets nem conteudo sensivel desnecessario.

Campos uteis:

- capability;
- provider;
- timestamp;
- latencia;
- status;
- custo estimado;
- tokens ou requests consumidos;
- fallback utilizado;
- codigo de erro normalizado.

---

## 4. Camada de modelos e IA

### 4.1 Groq

**Prioridade: Core**

Uso recomendado:

- inferencia LLM de baixa latencia;
- fallback de reasoning;
- tarefas rapidas de classificacao e resumo;
- coding auxiliar;
- speech-to-text com modelos Whisper quando disponivel;
- provider alternativo no Model Router.

Vantagens:

- latencia muito baixa;
- API simples;
- suporte a modelos abertos relevantes;
- free tier util para desenvolvimento e uso controlado;
- pode reduzir dependencia de um unico fornecedor.

Riscos:

- limites do plano gratuito mudam;
- modelos disponiveis podem mudar;
- nao deve ser tratado como armazenamento de memoria;
- chamadas externas podem conter dados que exigem politica de privacidade.

Capabilities candidatas:

```text
models.chat
audio.transcribe
models.classify
models.summarize
```

### 4.2 Google Gemini API

**Prioridade: Core**

Uso recomendado:

- multimodalidade;
- texto;
- imagem;
- documentos;
- reasoning;
- programacao;
- fallback e roteamento por custo/qualidade.

Papel no Meta:

```text
Model Router
  +-- local Ollama/vLLM
  +-- Groq
  +-- Gemini
  +-- Hugging Face / providers adicionais
```

Nao deve substituir os modelos locais. O objetivo e ampliar capacidade e resiliencia.

### 4.3 Jina AI

**Prioridade: Core e altamente recomendada**

Esta e uma das integracoes mais estrategicas encontradas na analise.

Capacidades uteis:

- Reader: pagina web para conteudo limpo orientado a LLM;
- Search: busca orientada a agentes;
- embeddings;
- reranking;
- processamento multimodal dependendo do servico utilizado.

Aplicacoes diretas:

```text
Research Engine
      |
      v
Jina Search / Reader
      |
      v
normalizacao
      |
      v
embeddings
      |
      v
Supabase + pgvector
      |
      v
reranker
      |
      v
Context Builder
      |
      v
LLM
```

Pode reduzir consideravelmente o esforco de implementar pipeline web -> limpeza -> indexacao -> recuperacao -> reranking.

Cuidados:

- validar limites atuais antes da implementacao;
- controlar conteudo enviado;
- manter fallback local ou alternativo para embeddings criticos;
- nao acoplar o formato interno de memoria aos schemas do provider.

### 4.4 Hugging Face

**Prioridade: Core como catalogo/provider abstrato; nao como fonte principal de inferencia gratuita**

Utilidade:

- acesso a grande variedade de modelos;
- NLP;
- visao;
- audio;
- embeddings;
- integracao com diferentes inference providers;
- experimentacao rapida.

A camada gratuita deve ser considerada limitada. O valor principal para o Meta e diversidade de modelos e compatibilidade, nao a promessa de inferencia gratuita ilimitada.

### 4.5 BRAINIALL

**Prioridade: Capability opcional**

Interessante por foco em transcricao PT-BR e espanhol, diarizacao e saidas SRT/VTT.

Uso sugerido:

```text
SpeechProvider
  +-- Whisper local
  +-- Groq Whisper
  +-- BRAINIALL
```

Antes de producao devem ser avaliados disponibilidade, politica de dados, latencia, limites e precisao em portugues brasileiro.

### 4.6 WolframAlpha

**Prioridade: Core para computacao estruturada**

O LLM nao deve resolver tudo apenas por geracao probabilistica. Uma engine computacional e valiosa para:

- matematica;
- conversoes;
- conhecimento computavel;
- consultas cientificas;
- verificacao de resultados numericos.

Capability sugerida:

```text
compute.query
```

### 4.7 APIs de IA secundarias

Providers como NLP Cloud, Clarifai, OpenVisionAPI, Perspective API e Roboflow podem entrar como tools especializadas, nao como dependencia central.

Classificacao inicial:

| Provider | Papel potencial | Prioridade |
|---|---|---|
| NLP Cloud | NLP especializado | Opcional |
| Clarifai | computer vision | Opcional |
| OpenVisionAPI | visao | Opcional |
| Perspective API | analise/moderacao textual | Opcional |
| Roboflow Universe | modelos especializados de visao | Opcional |
| DeepAI | baixo beneficio frente aos atuais providers | Nao priorizar |
| Brainshop.ai | sobreposicao com LLMs atuais | Nao priorizar |
| AI For Thai | especializado fora do foco principal | Nao priorizar |

---

## 5. Research e Web Intelligence

### 5.1 Jina Search e Reader

Podem compor a primeira camada de leitura e recuperacao web orientada a agentes.

### 5.2 News providers

O catalogo Public APIs inclui multiplas fontes, incluindo GNews, NewsAPI, NewsData, Mediastack e outras.

O Meta nao deve integrar todas. Deve criar uma capability unica:

```text
news.search
news.latest
```

E escolher providers por qualidade, idioma, regiao, cobertura e custo.

Providers candidatos:

- GNews;
- NewsData;
- Newsflash;
- Mediastack;
- fontes especializadas quando necessario.

### 5.3 Pesquisa cientifica

Providers e fontes candidatos:

- arXiv;
- CrossRef;
- Wikipedia;
- Wikidata;
- bases open data apropriadas.

Capabilities:

```text
research.search_papers
research.resolve_doi
knowledge.lookup_entity
knowledge.lookup_reference
```

Arquitetura sugerida:

```text
Deep Research Agent
   |
   +-- Web Search
   +-- Jina
   +-- arXiv
   +-- CrossRef
   +-- Wikipedia/Wikidata
   |
   v
Source Normalizer
   |
   v
Evidence Store
   |
   v
Synthesis
```

---

## 6. Security Capability Pack

A categoria de seguranca e anti-malware do catalogo e uma das mais relevantes para Jarvis e ULTRON.

### 6.1 VirusTotal

Uso:

- reputacao de arquivo;
- hash lookup;
- reputacao de URL;
- analise auxiliar de artefatos externos.

Nunca deve ser considerado veredito absoluto. O resultado deve compor um risk score.

### 6.2 Google Safe Browsing

Uso:

- reputacao de URLs;
- bloqueio preventivo antes de navegacao ou download automatico quando apropriado.

### 6.3 URLScan.io

Uso:

- analise de sites e infraestrutura web;
- coleta de sinais de risco.

### 6.4 AbuseIPDB

Uso:

- reputacao de enderecos IP;
- enrichment defensivo.

### 6.5 AlienVault OTX

Uso:

- threat intelligence;
- indicadores de comprometimento;
- enrichment.

### 6.6 NVD

Uso:

- CVEs;
- vulnerabilidades conhecidas;
- verificacao de dependencias e componentes.

### 6.7 GitGuardian

Uso:

- deteccao de secrets em codigo e repositorios;
- verificacao durante intake de skills e tools.

### 6.8 GreyNoise, Mozilla Observatory e HIBP

Papeis opcionais:

- contexto de IP;
- avaliacao de headers e postura web;
- exposicao conhecida de credenciais, sempre com uso de acordo com os termos e com protecao de dados.

### 6.9 Pipeline de intake de skills/repositorios

Este pipeline e especialmente relevante para o objetivo do Jarvis de instalar skills enviadas por link.

```text
URL/repository recebido
       |
       v
Source Resolver
       |
       v
Clone/Fetch em ambiente controlado
       |
       v
Static Inspection
       |
       +-- secret scan
       +-- dependency inspection
       +-- manifest inspection
       +-- executable/script inspection
       +-- suspicious URL inspection
       |
       v
External Reputation
       |
       +-- Safe Browsing
       +-- VirusTotal
       +-- URLScan
       +-- threat intel
       |
       v
Risk Score
       |
       +-- allow
       +-- require review
       +-- deny/quarantine
       |
       v
Sandbox Test
       |
       v
Install/Register
```

Regra: nenhuma dessas APIs substitui analise estatica, sandbox, permissoes e politica local.

---

## 7. Development Capability Pack

### 7.1 GitHub

Capability central para:

- repositorios;
- arquivos;
- issues;
- pull requests;
- commits;
- releases;
- automacoes de desenvolvimento.

O Jarvis deve abstrair essas operacoes em tools de engenharia, evitando expor diretamente detalhes desnecessarios da API ao planner.

### 7.2 GitLab

Provider secundario para ampliar compatibilidade futura.

### 7.3 Docker Hub

Uso:

- descoberta de imagens;
- metadados;
- tags;
- suporte a workflows de ambiente e deploy.

### 7.4 Cloudflare

Capability opcional para operacoes de infraestrutura web quando configurada pelo usuario.

### 7.5 APIs.guru

**Prioridade arquitetural alta.**

O APIs.guru agrega especificacoes OpenAPI/Swagger de diversas APIs publicas. Isso pode se tornar base para geracao automatica de adapters e tools.

Fluxo proposto:

```text
API descoberta
     |
     v
OpenAPI resolver
     |
     v
Schema parser
     |
     v
Security validation
     |
     v
Tool generator
     |
     +-- nome
     +-- descricao
     +-- input schema
     +-- output schema
     +-- auth requirements
     +-- endpoint mapping
     |
     v
Contract tests
     |
     v
Capability Registry
```

O adapter gerado nao deve ser ativado automaticamente sem validacao.

---

## 8. Documents e Productivity

### 8.1 OCR.Space

Capability:

```text
documents.ocr
```

Uso para imagens e documentos escaneados quando OCR externo for apropriado.

### 8.2 CloudConvert

Capability:

```text
files.convert
```

Pode servir como provider universal de conversao, mas deve existir controle de privacidade para impedir upload de arquivos sensiveis sem politica explicita.

### 8.3 iLovePDF

Capability opcional para transformacoes de PDF.

### 8.4 DocStruct

Potencial para extracao estruturada de documentos, contratos, recibos, extratos e invoices.

Deve ser validado tecnicamente antes de qualquer dependencia, especialmente quanto a confiabilidade, manutencao e tratamento de dados.

### 8.5 Google Docs e Notion

Podem ser tratados como conectores de workspace. A integracao deve permanecer separada do core cognitivo e obedecer autorizacao do usuario.

---

## 9. Geocoding, mapas e rotas

### Providers candidatos

- Nominatim;
- OpenStreetMap;
- OpenRouteService;
- Geoapify;
- OpenCage;
- Mapbox como opcional/pago conforme necessidade.

Capabilities sugeridas:

```text
location.geocode
location.reverse_geocode
location.search_places
routes.calculate
```

Arquitetura inicial:

```text
Location Provider
  +-- Nominatim / OSM
  +-- OpenRouteService
  +-- provider alternativo
```

Cuidados:

- respeitar politicas de uso e limites de Nominatim;
- nao tratar geocoding publico como servico sem limites;
- considerar cache;
- proteger dados de localizacao do usuario.

---

## 10. Weather Capability Pack

### Provider primario recomendado: Open-Meteo

Motivos:

- simples;
- bom para previsao;
- sem necessidade de chave em cenarios suportados;
- adequado para capability padronizada.

Fallbacks candidatos:

- HG Weather para contexto brasileiro quando util;
- WeatherAPI ou outro provider configuravel.

Contrato sugerido:

```json
{
  "capability": "weather.get_forecast",
  "input": {
    "latitude": -19.9,
    "longitude": -44.0,
    "days": 5,
    "units": "metric"
  }
}
```

O output deve ser normalizado para um schema do Meta, independente do formato do provider.

---

## 11. Currency e Finance

### 11.1 AwesomeAPI / Economia

Provider interessante para moedas e contexto brasileiro.

Capabilities:

```text
currency.quote
currency.convert
```

### 11.2 Frankfurter

Bom candidato a provider simples para cambio.

### 11.3 Finance Pack opcional

Pode incluir:

- market data;
- SEC filings;
- indicadores economicos;
- stocks;
- cambio;
- calculadoras financeiras.

Providers encontrados no ecossistema Public APIs incluem Marketstack, Twelve Data, StockData e outros.

Recomendacao: manter esse dominio como `Optional Pack` do ULTRON ou Zane, e nao como dependencia inicial do core.

---

## 12. Translation e Text Analysis

Uma capability de traducao deve poder escolher provider e fallback.

Candidatos incluem LibreTranslate e servicos especializados.

Capabilities:

```text
text.translate
text.detect_language
text.analyze
```

O LLM pode traduzir, mas API especializada pode ser preferivel em cenarios de custo, previsibilidade, lote ou privacidade.

---

## 13. Calendar, holidays e open data

APIs de feriados como Nager.Date/caldays podem suportar:

```text
calendar.public_holidays
```

Open data governamental pode futuramente compor packs por pais ou dominio, sem poluir o core.

---

## 14. APIs que nao devem entrar no core

O repositorio Public APIs contem muitas categorias legitimas, mas sem valor estrutural para o Meta neste momento, como APIs de animais, anime, piadas, imagens aleatorias, jogos especificos, curiosidades, placeholders e outros dominios de nicho.

Essas APIs nao devem ser hardcoded em Rachel, Jarvis ou Zane.

Elas podem permanecer descobriveis via ULTRON ou por um futuro `Public API Catalog`.

Exemplo:

```text
Zane precisa de uma capability nova
        |
        v
Capability Search
        |
        +-- ULTRON Registry
        +-- Public APIs catalog
        +-- APIs.guru
        +-- MCP/tool catalogs
        |
        v
candidatos
        |
        v
validation pipeline
```

---

## 15. ULTRON como API Capability Registry

Esta analise reforca uma evolucao natural do ULTRON.

O ULTRON pode registrar:

- agents;
- skills;
- tools;
- MCP servers;
- workflows;
- packs;
- external API providers;
- OpenAPI definitions;
- generated adapters.

Isso permite tratar uma API como um tipo de recurso instalavel e versionado.

Manifest conceitual:

```yaml
kind: external-provider
id: open-meteo
version: 1
capabilities:
  - weather.get_forecast
auth:
  type: none
network:
  domains:
    - api.open-meteo.com
permissions:
  filesystem: none
  subprocess: false
risk:
  level: low
```

O ULTRON pode disponibilizar isso para qualquer consumidor compativel, mas Zane/Jarvis devem manter operacao local para capabilities essenciais ja instaladas.

---

## 16. Automatic API Discovery

Uma evolucao de alto valor e permitir que o sistema descubra e prepare integracoes novas.

### Fontes candidatas

- Public APIs;
- APIs.guru;
- OpenAPI specs fornecidas pelo usuario;
- repositorios GitHub;
- catalogos de MCP/tools;
- Not Human Search ou servicos equivalentes, apos validacao independente.

### Pipeline proposto

```text
Discover
  |
  v
Resolve metadata
  |
  v
Fetch documentation/OpenAPI
  |
  v
Parse capabilities
  |
  v
Auth analysis
  |
  v
Security analysis
  |
  v
Generate adapter candidate
  |
  v
Static validation
  |
  v
Sandbox contract tests
  |
  v
Human/policy approval when required
  |
  v
Register disabled
  |
  v
Enable
```

O sistema deve conseguir dizer por que uma API foi aprovada, rejeitada ou mantida em quarentena.

---

## 17. Contrato padrao de Provider

Interface conceitual:

```python
class CapabilityProvider:
    provider_id: str

    def supports(self, capability: str) -> bool:
        ...

    async def health(self) -> ProviderHealth:
        ...

    async def execute(
        self,
        capability: str,
        input: dict,
        context: ExecutionContext,
    ) -> ProviderResult:
        ...
```

`ExecutionContext` deve conter somente metadados necessarios, por exemplo:

- correlation id;
- deadline;
- privacy policy;
- cost budget;
- model/tool policy;
- user authorization scope.

---

## 18. Normalizacao de erros

Providers retornam formatos diferentes. O Meta deve converter tudo para erros internos previsiveis.

Classes sugeridas:

```text
ProviderAuthError
ProviderRateLimitError
ProviderTimeoutError
ProviderUnavailableError
ProviderInvalidResponseError
ProviderPolicyError
ProviderQuotaExceededError
```

O planner nao deve interpretar mensagens arbitrarias de erro de cada API.

---

## 19. Fallback e resiliencia

Exemplo:

```yaml
capability: models.chat
routing:
  providers:
    - local
    - groq
    - gemini
  retry:
    max_attempts_per_provider: 1
  circuit_breaker: true
  total_timeout_ms: 30000
```

Regras:

1. nao repetir indefinidamente uma chamada com erro;
2. nao trocar automaticamente para provider com politica de privacidade inferior sem autorizacao;
3. nao duplicar efeitos colaterais em retries;
4. registrar quando fallback aconteceu;
5. aplicar circuit breaker a providers instaveis.

---

## 20. Cache

Capacidades de leitura podem usar cache quando adequado.

Exemplos:

- clima: minutos;
- cambio: segundos/minutos conforme contexto;
- CVE: horas;
- geocoding: longo prazo;
- provider metadata: horas;
- resultados de LLM: normalmente nao cachear genericamente sem chave semantica e politica apropriada.

O cache deve respeitar dados pessoais e termos do provider.

---

## 21. Privacidade e classificacao de dados

Antes de uma chamada externa, o runtime deve classificar a carga.

Classes sugeridas:

```text
PUBLIC
INTERNAL
PERSONAL
SENSITIVE
SECRET
```

Regras iniciais:

- SECRET nunca deve ser enviado a provider externo comum;
- credenciais sao tratadas somente pelo Secret Manager;
- PERSONAL depende de politica e necessidade;
- SENSITIVE pode exigir provider local ou aprovacao;
- PUBLIC pode utilizar provider externo de acordo com a capability.

---

## 22. Observabilidade

Metricas uteis:

- sucesso por capability;
- sucesso por provider;
- p50/p95 de latencia;
- erros;
- fallback rate;
- rate limit hits;
- requests por provider;
- custo estimado;
- tokens;
- disponibilidade;
- tempo medio ate recovery.

Isso permite ao Router aprender qual provider realmente funciona melhor no ambiente real.

---

## 23. Selecao inicial recomendada

### Core / alta prioridade

| Capability | Provider inicial recomendado |
|---|---|
| LLM rapido | Groq |
| LLM multimodal externo | Gemini |
| modelos diversos | Hugging Face |
| embeddings | Jina |
| reranking | Jina |
| web reader | Jina Reader |
| web/research search | Jina Search + engine adicional |
| speech-to-text externo | Groq Whisper |
| speech-to-text local | Whisper local |
| computacao | WolframAlpha |
| weather | Open-Meteo |
| geocoding | Nominatim / OpenStreetMap |
| routes | OpenRouteService |
| currency BR | AwesomeAPI |
| research papers | arXiv |
| DOI/references | CrossRef |
| repository/dev | GitHub |
| containers | Docker Hub |
| API discovery | APIs.guru |
| URL safety | Google Safe Browsing |
| URL/site analysis | URLScan.io |
| malware reputation | VirusTotal |
| IP reputation | AbuseIPDB |
| threat intelligence | AlienVault OTX |
| vulnerability data | NVD |
| secret detection | GitGuardian |
| OCR | OCR.Space |
| file conversion | CloudConvert |
| PDF manipulation | iLovePDF |
| holidays | Nager.Date/caldays |

### Secondary / optional

- BRAINIALL;
- GNews;
- NewsData;
- Newsflash;
- NLP Cloud;
- Clarifai;
- OpenVisionAPI;
- Perspective API;
- Roboflow;
- GreyNoise;
- Mozilla Observatory;
- Have I Been Pwned;
- GitLab;
- Geoapify;
- OpenCage;
- WeatherAPI;
- HG Weather;
- LibreTranslate;
- Notion;
- Google Docs;
- DocStruct;
- financial market providers.

### Nao priorizados

- DeepAI como dependencia principal;
- Brainshop.ai;
- APIs especializadas sem relacao com o roadmap;
- dezenas de providers equivalentes para a mesma capability;
- APIs de entretenimento ou conteudo aleatorio no core.

---

## 24. Ordem de implementacao recomendada

### Fase A - Fundacao

1. definir schemas de Capability e Provider;
2. criar Provider Registry;
3. criar Secret Manager adapter;
4. criar HTTP client padrao;
5. normalizar erros;
6. criar timeouts, retries e circuit breaker;
7. criar Usage Ledger;
8. criar health checks.

### Fase B - Primeiras capabilities

Implementar poucas integracoes de alto valor para provar a arquitetura:

1. `weather.get_forecast` -> Open-Meteo;
2. `currency.quote` -> AwesomeAPI/Frankfurter;
3. `security.scan_url` -> Safe Browsing/URLScan;
4. `research.read_url` -> Jina Reader;
5. `research.search` -> Jina Search ou provider escolhido;
6. `models.chat` -> Groq/Gemini via Model Router.

### Fase C - Security Pack

Adicionar VirusTotal, AbuseIPDB, NVD, OTX, GitGuardian e pipeline de intake de repositorios/skills.

### Fase D - Research Pack

Adicionar arXiv, CrossRef, Wikipedia/Wikidata e evidence normalization.

### Fase E - API Discovery

Integrar APIs.guru e parser OpenAPI.

### Fase F - Generated Tools

Gerar adapters candidates automaticamente, sempre com testes e aprovacao antes de ativacao.

### Fase G - ULTRON Registry

Publicar manifests de providers e capabilities no ULTRON, mantendo copia/cache local das tools instaladas necessarias ao runtime.

---

## 25. Criterios de aceite de uma nova API

Uma API so deve ser marcada como aprovada quando os seguintes pontos forem avaliados:

- utilidade clara para uma capability;
- documentacao acessivel;
- HTTPS;
- autenticacao compreendida;
- termos de uso analisados quando relevante;
- rate limits conhecidos ou tratados conservadoramente;
- timeout configurado;
- retries seguros;
- output validado por schema;
- erros normalizados;
- secret handling correto;
- dados enviados classificados;
- health check definido;
- observabilidade ativa;
- testes unitarios do adapter;
- contract/integration test;
- fallback definido quando a capability for critica;
- dependencia nao quebra o funcionamento offline alem do inevitavel.

---

## 26. Regras para APIs gratuitas e free tier

Toda API gratuita deve ser registrada com:

```yaml
pricing:
  tier: free_or_free_tier
  verified_at: YYYY-MM-DD
  limits_known: true|false
  production_allowed: true|false|unknown
```

Motivo: informacao de preco e quota envelhece rapidamente.

A implementacao nao deve assumir permanentemente que Groq, Gemini, Jina, Hugging Face ou qualquer outro provider manterao o mesmo plano gratuito observado durante esta pesquisa.

---

## 27. Relacao com Rachel

Rachel deve consumir capabilities atraves de contratos de alto nivel para:

- contexto pessoal;
- pesquisas;
- clima;
- calendario;
- geolocalizacao;
- conhecimento;
- documentos;
- memoria/RAG;
- voz;
- automacoes.

Rachel nao deve conter dezenas de clientes HTTP de providers diretamente em sua camada cognitiva.

---

## 28. Relacao com Jarvis

Jarvis e o principal consumidor operacional dessa arquitetura.

Uso:

- research;
- coding;
- GitHub;
- repository analysis;
- skill installation;
- threat checks;
- documentation;
- model routing;
- generated tools;
- API discovery;
- sandbox validation.

O agent loop deve pedir tools/capabilities, e nao construir requests HTTP arbitrarias quando existir adapter aprovado.

---

## 29. Relacao com ULTRON

ULTRON pode ser o catalogo universal de:

- capabilities;
- providers;
- skills;
- agents;
- MCP servers;
- OpenAPI specs;
- generated adapters;
- compatibility metadata;
- versionamento;
- security metadata.

Ele deve facilitar descoberta e distribuicao, nao monopolizar execucao.

---

## 30. Relacao com Zane

Zane deve receber a versao consolidada e madura dessa camada.

Arquitetura alvo:

```text
                         ZANE
                           |
                    Capability Router
                           |
       +-------------------+-------------------+
       |                   |                   |
     Models              Tools               Data
       |                   |                   |
   Model Router      Capability Layer     Memory/Data
       |                   |                   |
 local/Groq/...     Provider Router      Supabase/local
                           |
             +-------------+-------------+
             |             |             |
          Jina         Open-Meteo     VirusTotal
          GitHub       Nominatim      arXiv
          etc.          etc.           etc.
```

---

## 31. Decisao arquitetural resultante

A decisao recomendada desta pesquisa e:

> O Projeto Meta deve utilizar o repositorio Public APIs como fonte de descoberta, mas implementar uma camada propria de External Capabilities baseada em contratos internos, providers substituiveis, roteamento, fallback, seguranca, observabilidade e descoberta controlada. APIs externas nao devem ser acopladas diretamente ao cerebro dos agentes.

As descobertas mais estrategicas para estudo e implementacao inicial sao:

1. Jina AI para Reader, Search, embeddings e reranking;
2. Groq para inferencia rapida e Whisper;
3. Gemini como provider multimodal;
4. APIs.guru para descoberta e geracao de adapters OpenAPI;
5. Security APIs para o pipeline de intake de skills e repositorios;
6. Open-Meteo, Nominatim/OpenRouteService e AwesomeAPI como primeiras provas simples do Capability Layer.

---

## 32. Fontes principais

- Public APIs repository: https://github.com/public-apis/public-apis
- Groq documentation: https://console.groq.com/docs
- Google Gemini API documentation: https://ai.google.dev/gemini-api/docs
- Jina AI: https://jina.ai
- Hugging Face Inference Providers: https://huggingface.co/docs/inference-providers
- APIs.guru: https://apis.guru
- Open-Meteo: https://open-meteo.com
- OpenStreetMap / Nominatim: https://nominatim.org
- OpenRouteService: https://openrouteservice.org
- VirusTotal: https://docs.virustotal.com
- URLScan: https://urlscan.io/docs/api
- NVD: https://nvd.nist.gov/developers
- arXiv API: https://info.arxiv.org/help/api
- CrossRef API: https://api.crossref.org

Todas as informacoes de disponibilidade, preco, free tier, limites e termos devem ser reconfirmadas no momento da implementacao, pois sao propriedades externas e mutaveis.
