# Jarvis — Capability/Provider e capacidades externas

## Estado

- Tipo: plano técnico vinculante para a V1 do Jarvis.
- Estado: planejado, com fundações parciais herdadas do OpenJarvis.
- Fonte transversal:
  [`external-capabilities-public-apis.md`](../../integracao/external-capabilities-public-apis.md).
- Roadmap associado:
  [`plano-mestre-conclusao.md`](../desenvolvimento/plano-mestre-conclusao.md).
- Data da consolidação: 2026-08-28.

## Decisão

Jarvis será o runtime standalone que resolve e executa capabilities. Agentes
pedirão operações por contrato lógico, sem depender diretamente do SDK ou do
formato de um fornecedor.

Exemplo:

```text
agent request
  -> capability contract
  -> policy and data classification
  -> provider selection
  -> governed execution
  -> normalized result and evidence
  -> audit and usage ledger
```

A camada será reutilizável, mas permanecerá interna ao runtime Jarvis. Ela não
transformará o Jarvis em marketplace nem em produto pessoal.

## Fronteiras entre projetos

| Projeto | Responsabilidade | Fora de escopo |
|---|---|---|
| Jarvis | resolver, autorizar e executar capabilities instaladas | catálogo global e identidade pessoal |
| ULTRON | catálogo, distribuição, assinatura e compatibilidade | controlar toda execução local do Jarvis |
| Rachel | experiência pessoal, memória e políticas do usuário | implementar SDKs de providers no núcleo cognitivo |
| Zane | consolidar contratos aprovados no produto final | depender permanentemente dos predecessores |

ULTRON será opcional. Jarvis deve manter um registry local das capabilities
essenciais instaladas e continuar operacional quando ULTRON estiver fora do ar.

## Avaliação do código existente

O fork já contém peças que devem ser preservadas e adaptadas:

| Peça atual | Reuso | Lacuna para o plano |
|---|---|---|
| `ToolRegistry` e `ToolSpec` | execução e schemas de tools | capability lógica ainda equivale demais à tool concreta |
| engines local/cloud | providers de modelos | contrato ainda é específico de inferência |
| `MultiEngine` e discovery | seleção e health de modelos | não cobre providers gerais nem política de dados |
| `HttpRequestTool` | SSRF, redirect e limite de resposta | não é gateway tipado de providers |
| credentials TOML | credenciais locais por tool | falta `secret_ref`, redaction e ciclo por provider |
| telemetry/analytics | eventos e usage existentes | falta ledger uniforme por capability/provider |
| MCP client/server | transporte de ferramentas externas | falta governança completa por servidor e capability |
| `DeepResearchAgent` | loop multi-hop e síntese | foco atual é conhecimento privado, não evidência web normalizada |
| `skill_repo_*` | clone, análise de manifest e confirmação | falta risk score, quarentena, reputation e rollback completo |

Essas fundações não autorizam marcar a camada como implementada. O novo núcleo
deve compor os componentes existentes, evitando uma segunda infraestrutura de
tools, segurança ou telemetria.

## Arquitetura-alvo

```text
Agent / Skill / Workflow
        |
CapabilityResolver
        |
Policy + DataClassifier + Budget
        |
ProviderRouter
        |
ProviderGateway
        |
Adapter local, cloud, MCP or generated OpenAPI
        |
Normalized ProviderResult
        |
Evidence + UsageLedger + Audit
```

### Capability Registry

Fonte local versionada dos contratos disponíveis. Campos mínimos:

```yaml
id: research.read_url
version: 1.0.0
input_schema: ResearchReadRequest
output_schema: EvidenceDocument
effects: [network_read]
data_classes_allowed: [PUBLIC, INTERNAL]
providers: [jina_reader, direct_reader]
timeout_ms: 10000
cache_policy: public-read-15m
status: enabled
```

Requisitos:

- IDs estáveis e versionamento semântico;
- schemas fechados para entrada e saída;
- efeitos, idempotência, timeout e política de retry;
- capacidades RBAC e classificação de dados exigidas;
- providers compatíveis e estado de ativação;
- origem e integridade do manifest;
- nenhuma chave ou segredo no manifest.

### Provider Registry

Descreve implementações sem expô-las ao planner:

```yaml
id: jina_reader
kind: http
implements: [research.read_url]
base_url: https://r.jina.ai
secret_ref: providers.jina.api_key
network_domains: [r.jina.ai]
health_check: configured
cost_class: free_or_free_tier
pricing_verified_at: 2026-08-28
```

Estados normalizados:

```text
healthy
degraded
rate_limited
auth_error
unavailable
disabled
unknown
```

### Provider Router

O router deverá filtrar providers na seguinte ordem:

1. capability e versão compatíveis;
2. política e classificação dos dados;
3. autorização e domínio de rede;
4. health, circuit breaker e quota;
5. preferência local/cloud do usuário;
6. qualidade mínima e contexto suportado;
7. custo e latência;
8. fallback permitido para a requisição.

O fallback nunca poderá reduzir privacidade silenciosamente. Operações com
efeito precisarão de idempotency key ou ficarão fora de retry automático.

### Provider Gateway

Responsável por:

- resolver `secret_ref` apenas durante execução autorizada;
- usar cliente HTTP padronizado com SSRF e allowlist;
- impor deadline, limite de payload e cancelamento;
- validar resposta contra schema;
- normalizar erros;
- aplicar retry e circuit breaker;
- produzir eventos e usage sem segredos;
- devolver somente `ProviderResult` normalizado.

Erros públicos mínimos:

```text
ProviderAuthError
ProviderRateLimitError
ProviderTimeoutError
ProviderUnavailableError
ProviderInvalidResponseError
ProviderPolicyError
ProviderQuotaExceededError
```

### Classificação de dados

| Classe | Regra inicial |
|---|---|
| `PUBLIC` | provider externo permitido conforme policy |
| `INTERNAL` | somente provider autorizado e contrato explícito |
| `PERSONAL` | consentimento/política e minimização obrigatórios |
| `SENSITIVE` | local por padrão; cloud exige autorização específica |
| `SECRET` | nunca enviado como conteúdo a provider comum |

O classificador fornece decisão à policy; não substitui RBAC, approval nem
regras específicas da tool.

## Capabilities que cabem ao Jarvis V1

### Modelos e execução agentic

```text
models.chat
models.embed
models.rerank
models.classify
models.summarize
```

Providers candidatos: local/Ollama, vLLM, OpenAI, Anthropic, Gemini, Groq,
OpenRouter e Jina para embeddings/reranking. Hugging Face será compatibilidade
opcional, não promessa de inferência gratuita.

Essas capabilities estendem a etapa 08. Não criarão um segundo Model Router;
os engines existentes serão adapters do contrato comum.

### Coding e repositórios

```text
repository.inspect
repository.search_code
repository.read
repository.diff
repository.test
repository.dependencies
repository.vulnerabilities
git.status
git.commit
github.repository
github.issue
github.pull_request
github.release
containers.image_metadata
```

Git local continua sendo o caminho primário para trabalho no checkout. GitHub
é provider externo para objetos remotos. GitLab e Docker Hub são opcionais.
Ações mutáveis permanecem sujeitas a confirmação e escopo.

### Research e evidência

```text
research.search
research.read_url
research.search_papers
research.resolve_doi
knowledge.lookup_entity
knowledge.lookup_reference
```

Providers iniciais candidatos: Jina Search/Reader, mecanismo web existente,
arXiv, CrossRef, Wikipedia e Wikidata.

Todo resultado deverá produzir `EvidenceItem` com:

- `evidence_id` estável na execução;
- URL/origem e provider;
- título, autor e data quando disponíveis;
- instante da coleta;
- trecho ou dado normalizado;
- hash do conteúdo normalizado;
- classificação de confiança e freshness;
- relação entre afirmação e fonte;
- avisos de conflito ou ausência.

O agente não poderá apresentar uma síntese como verificada quando as fontes
necessárias falharem ou se contradisserem sem aviso.

### Segurança de repositórios e skills

```text
security.scan_url
security.lookup_file_hash
security.lookup_ip
security.lookup_cve
security.scan_secrets
security.assess_repository
```

Providers candidatos: Google Safe Browsing, URLScan.io, VirusTotal,
AbuseIPDB, NVD, AlienVault OTX e GitGuardian.

Essas APIs fornecem sinais. Nenhuma delas decide instalação sozinha. O risk
engine local combina:

- origem, commit e integridade;
- symlinks e path traversal;
- manifests, scripts e executáveis;
- capacidades solicitadas;
- dependências e vulnerabilidades;
- segredos encontrados;
- URLs/domínios e reputação externa;
- resultado de sandbox;
- confirmação exigida.

Saídas permitidas: `allow`, `require_review`, `quarantine` e `deny`, sempre com
razões estruturadas. A instalação continua separada de inspeção e ativação.

### OpenAPI e adapters gerados

```text
api.resolve_spec
api.validate_spec
api.generate_adapter_candidate
api.contract_test
api.register_disabled
```

Fontes: especificação fornecida pelo usuário, APIs.guru e futuramente ULTRON.

Pipeline obrigatório:

```text
discover
  -> fetch spec
  -> parse and bound size
  -> resolve references safely
  -> inspect auth and servers
  -> derive candidate capabilities
  -> generate code/manifest in sandbox
  -> static and security validation
  -> contract tests
  -> human/policy approval
  -> register disabled
  -> explicit enable
```

Adapters gerados nunca serão executados nem ativados automaticamente. Specs
remotas são conteúdo não confiável; referências externas, callbacks, uploads,
webhooks e operações destrutivas terão política mais forte.

## Capabilities fora do core da V1

Clima, moedas, geocoding, rotas, OCR, conversão de arquivos, notícias,
feriados, finanças, visão especializada e integrações de nicho não serão
hardcoded no Jarvis V1.

Servirão como fixtures para provar o framework ou packs opcionais distribuídos
pelo ULTRON. A fundação deve suportá-las sem tornar cada domínio requisito da
release.

Provas recomendadas:

| Objetivo | Capability | Provider candidato |
|---|---|---|
| provider sem chave | `weather.get_forecast` | Open-Meteo |
| schema e normalização | `currency.quote` | Frankfurter/AwesomeAPI |
| research | `research.read_url` | Jina Reader |
| segurança | `security.scan_url` | Safe Browsing/URLScan |
| modelo | `models.chat` | local + cloud |

As duas primeiras são fixtures de arquitetura, não funcionalidades centrais do
agente de código.

## Dependências entre etapas

```text
04 RBAC + 05 sandbox
          |
          v
14 capability contracts
          |
          v
15 provider operations
       /     |      \
      v      v       v
16 MCP   17 research 18 security intake
      \      |       /
       \     v      /
        19 OpenAPI
            |
            v
21 ULTRON integration
```

Relações adicionais:

- etapa 08 fornece engines de modelo para `models.*`;
- etapa 09 fornece contexto/indexação para repository e research;
- etapas 13 e 18 formam o intake completo de skills;
- etapa 19 depende também das etapas 07 e 12 para gerar e validar código;
- etapa 24 recebe usage/custo dos providers;
- etapa 26 incorpora diagnóstico de registry, provider e quota;
- etapas 27 e 28 validam as capabilities em benchmarks e estabilidade.

## Ondas de implementação

### Onda 1 — contratos e runtime

1. modelos e schemas imutáveis;
2. Capability Registry local;
3. Provider Registry local;
4. resolver e router;
5. gateway, erros e `secret_ref`;
6. policy de dados, network domains e budgets;
7. health, rate limit, circuit breaker, cache e ledger.

### Onda 2 — provas verticais

1. adaptar `models.chat` ao engine existente;
2. provider read-only sem chave;
3. provider autenticado sem expor secret;
4. fallback compatível com privacidade;
5. falha, timeout, quota e schema inválido.

### Onda 3 — capabilities prioritárias

1. research e evidências;
2. análise GitHub/repositório;
3. security pack para intake;
4. MCP governado;
5. OpenAPI candidate generator.

### Onda 4 — ULTRON opcional

1. importar manifest assinado/identificado;
2. validar versão e permissões;
3. armazenar localmente;
4. registrar desativado;
5. permitir ativação explícita;
6. continuar operando offline com componentes essenciais instalados.

## Critério de aceite de provider

Um provider só pode ser marcado `approved` quando houver evidência de:

- utilidade clara e capability versionada;
- HTTPS e domínios de rede explícitos;
- autenticação e `secret_ref` corretos;
- termos, preço e quota registrados com data de verificação;
- timeout, cancelamento e limite de payload;
- retries idempotentes e circuit breaker;
- schemas de entrada e saída;
- erros normalizados;
- classificação e minimização de dados;
- health check e métricas;
- unit tests, contract tests e teste de integração opcional;
- fallback para capability crítica ou degradação documentada;
- remoção/desativação sem quebrar o runtime standalone.

Free tier é metadado mutável, não garantia arquitetural.

## Gates de segurança

- conteúdo de API, web, MCP, GitHub e OpenAPI é não confiável;
- provider não pode modificar policy ou system prompt;
- respostas não podem revelar headers, tokens ou `secret_ref` resolvido;
- URLs passam por SSRF, redirect checks e domínios autorizados;
- uploads de dados pessoais/sensíveis exigem política explícita;
- retries não repetem efeitos sem idempotência;
- logs e ledger usam redaction;
- provider comprometido pode ser desativado por kill switch;
- adapters e skills entram desativados até aprovação;
- resultados de reputation são sinais, não vereditos absolutos.

## Evidência necessária para conclusão

O conjunto Capability/Provider será aceito somente com:

1. testes unitários dos contratos, registries, router e gateway;
2. testes de concorrência para quotas, cache e circuit breaker;
3. contract tests determinísticos com mocks;
4. integração real opt-in sem chaves na CI pública;
5. matriz de classificação de dados e fallback;
6. prova de ausência de secrets em logs/resultados;
7. failure injection para timeout, quota, auth e payload inválido;
8. benchmark agentic usando capabilities, não HTTP arbitrário;
9. documentação operacional de adicionar, desativar e diagnosticar provider;
10. rastreabilidade entre commit, etapa e evidência de CI.

## Regra de manutenção

Cada implementação relacionada deverá atualizar:

1. registro técnico JARVIS-NNN;
2. histórico central;
3. etapa correspondente do plano mestre;
4. este documento quando contratos ou fronteiras mudarem;
5. matriz de provider/capability quando um adapter for criado ou aprovado.
