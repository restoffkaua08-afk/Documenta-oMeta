# ULTRON — External Capabilities, Provider Registry e OpenAPI Discovery

**Estado:** arquitetura aprovada para implementacao futura  
**Data:** 2026-08-28  
**Baseline de codigo auditada:** `a34aeaf`  
**Fonte transversal:** [`../../integracao/external-capabilities-public-apis.md`](../../integracao/external-capabilities-public-apis.md)

## 1. Decisao executiva

O Ultron evoluira de registry de `agent`, `skill`, `workflow` e `pack` para um
catalogo universal que tambem descreve, valida, versiona e distribui:

- capabilities logicas;
- providers externos;
- especificacoes OpenAPI/Swagger;
- adapters gerados;
- tools derivadas;
- health e compatibilidade publicados;
- metadados de custo, quota, privacidade e seguranca.

O Ultron **nao se torna o executor obrigatorio** dessas capabilities. Ele e o
control plane de descoberta e distribuicao. Rachel, Jarvis, Zane e outros
consumers mantem runtime, policy, secrets, cache local e capacidades essenciais
instaladas. Se o Ultron estiver indisponivel, o consumer continua iniciando e
usando o que ja possui.

## 2. Gap confirmado na baseline

Na baseline `a34aeaf`:

- `BaseManifest` possui somente quatro kinds: `agent`, `skill`, `workflow`, `pack`;
- o Registry SQLite persiste e pesquisa esses quatro kinds;
- capabilities aparecem como strings declarativas, nao como recursos versionados;
- nao existe `ProviderManifest` nem `CapabilityManifest`;
- nao existe ingestao de OpenAPI/Swagger;
- nao existe gerador de adapters/tools;
- nao existe Provider Router, Health Manager, Rate Limit Manager ou Usage Ledger;
- o servidor MCP expoe lifecycle do Ultron, nao tools externas geradas.

Logo, esta evolucao e trabalho novo e deve passar por gates proprios.

## 3. Fronteiras de responsabilidade

| Responsabilidade | Ultron | Consumer/runtime |
|---|---:|---:|
| Catalogar capabilities e providers | Sim | Cache local opcional |
| Versionar manifests e specs | Sim | Consumir versao fixada |
| Descobrir APIs candidatas | Sim | Pode fornecer fonte manual |
| Gerar adapter candidato | Sim, em worker isolado | Nao obrigatorio |
| Aprovar/assinar/distribuir adapter | Sim | Decide se instala |
| Guardar segredo real do usuario | Nao no catalogo | Sim, Secret Manager local/cloud |
| Escolher provider durante execucao | Politica recomendada | Sim, decisao final |
| Executar chamada externa | Nao como requisito | Sim |
| Health de catalogo | Sim | Health operacional local |
| Cache e fallback essencial | Snapshot distribuivel | Sim |
| Autorizar uso e dados enviados | Metadados/policy | Sim, autoridade final |

### Invariantes

1. Nenhum consumer depende de consulta ao Ultron para iniciar.
2. Descoberta nunca implica instalacao, ativacao ou execucao.
3. Adapter gerado nasce `candidate` e desabilitado.
4. Segredo nunca entra em manifest, OpenAPI persistida publicamente, log ou prompt.
5. Provider externo nao pode elevar permissoes declaradas pelo recurso pai.
6. Fallback nao pode reduzir privacidade sem autorizacao explicita.
7. Retry de operacao mutante exige idempotencia comprovada.
8. O runtime pode rejeitar qualquer recomendacao do Ultron.

## 4. Modelo de dominio

### 4.1 CapabilityManifest

Representa o contrato logico independente de fornecedor.

Campos minimos:

```yaml
kind: capability
id: meta/weather.get-forecast
version: 1.0.0
domain: weather
operation: get_forecast
input_schema: {}
output_schema: {}
side_effect: none
data_classes: [PUBLIC]
network_required: true
cache_policy:
  allowed: true
  default_ttl_seconds: 900
providers: [meta/open-meteo]
fallback_policy: explicit
```

Regras:

- input/output usam JSON Schema canonico e versionado;
- side effect e `none`, `idempotent_write` ou `non_idempotent_write`;
- breaking change exige major version;
- provider nao redefine o significado da capability;
- schemas internos nao copiam cegamente schemas externos.

### 4.2 ProviderManifest

Representa uma implementacao concreta de uma ou mais capabilities.

```yaml
kind: provider
id: meta/open-meteo
version: 1.0.0
capabilities:
  - id: meta/weather.get-forecast
    version_range: ^1.0
base_urls:
  - https://api.open-meteo.com
allowed_domains: [api.open-meteo.com]
auth:
  type: none
health:
  strategy: synthetic_read
  interval_seconds: 300
pricing:
  class: free_or_free_tier
  verified_at: 2026-08-28
privacy:
  maximum_data_class: PUBLIC
rate_limits:
  known: false
```

Regras:

- hosts usam allowlist exata e HTTPS;
- redirects sao revalidados contra allowlist;
- auth descreve mecanismo, nunca valor do segredo;
- preco, quota e termos possuem `verified_at`;
- provider pode ser desabilitado/revogado sem remover a capability;
- health publicado e indicativo; o consumer mede o ambiente real.

### 4.3 OpenApiDefinitionManifest

Referencia uma especificacao, sem transforma-la automaticamente em confiavel.

Campos:

- URL e/ou artefato imutavel por SHA-256;
- versao OpenAPI/Swagger detectada;
- origem, publisher e data de coleta;
- servers e hosts extraidos;
- mecanismos de autenticacao;
- operacoes, schemas e callbacks encontrados;
- resultado de lint/validacao;
- status de risco e quarentena;
- licenca e termos quando conhecidos.

### 4.4 GeneratedAdapterManifest

Liga spec, capability e codigo gerado.

Campos:

- gerador e versao;
- digest da spec de origem;
- operations selecionadas;
- mapeamento input/output;
- dominios e permissoes;
- suite de contrato gerada;
- status de revisao;
- artefato, SBOM, assinatura e proveniencia;
- compatibilidade com runtimes/consumers.

### 4.5 Relacao com Agent, Skill, Workflow e Pack

- `agent` declara capabilities necessarias, sem fixar provider por padrao;
- `skill` pode requerer capabilities e incluir policy/transformacoes;
- `workflow` encadeia capabilities e declara compensacao para efeitos;
- `pack` agrupa capabilities, providers e adapters compativeis;
- provider e dependencia substituivel, nao parte do cerebro do agent;
- lockfile fixa provider/adapter somente no ambiente que optou por isso.

## 5. Estados e lifecycle

### 5.1 Capability

`draft → validated → published → deprecated → revoked`

### 5.2 Provider

`discovered → candidate → validated → published_disabled → active → degraded → disabled → revoked`

### 5.3 OpenAPI e adapter gerado

`discovered → fetched → parsed → security_review → generated → contract_tested → human_or_policy_approved → signed → published_disabled → enabled`

Qualquer falha de integridade, SSRF, schema, assinatura ou policy direciona o
recurso para `quarantined`. Reprocessamento gera nova evidencia, nunca apaga a
anterior.

## 6. Pipeline de discovery OpenAPI/Swagger

Fontes permitidas inicialmente:

- APIs.guru;
- URL OpenAPI fornecida pelo usuario;
- especificacao em repositorio Git fixada por commit;
- artefato enviado e hasheado;
- catalogos futuros explicitamente aprovados.

Pipeline:

```text
Discover
  -> normalize source
  -> preflight URL/repository policy
  -> fetch in isolated worker
  -> verify size, media type and digest
  -> parse OpenAPI/Swagger with limits
  -> resolve references under policy
  -> inspect auth, servers, callbacks and side effects
  -> infer candidate capabilities
  -> generate candidate adapter + tests
  -> static/security validation
  -> sandbox contract tests
  -> review/approval
  -> sign and publish disabled
  -> explicit consumer enablement
```

### Protecoes obrigatorias

- bloqueio de loopback, link-local, metadata services e redes privadas nao autorizadas;
- limite de redirects, tamanho, profundidade, referencias e tempo;
- referencias externas desativadas por padrao;
- nenhum `operationId` ou descricao vira codigo sem sanitizacao;
- callbacks/webhooks nao sao habilitados automaticamente;
- OAuth URLs, scopes e servers sao tratados como dados nao confiaveis;
- geracao nao executa exemplos, scripts ou extensoes da spec;
- SSRF e zip/decompression bombs possuem testes negativos;
- specs mutaveis sao copiadas por digest antes de qualquer decisao.

## 7. Geracao automatica de tools e adapters

O gerador produz candidato deterministico:

- nome interno estavel;
- descricao sanitizada;
- JSON Schema de entrada reduzido a operacao selecionada;
- validador de resposta;
- mapping de erros HTTP para erros Ultron;
- auth por `secret_ref` abstrato;
- timeout e limites conservadores;
- lista exata de dominios;
- testes de serializacao, contrato e erro;
- manifest, SBOM e proveniencia.

Nao gera ou habilita automaticamente:

- shell/subprocess;
- filesystem amplo;
- rede fora dos hosts declarados;
- upload de dados pessoais/sensiveis;
- retries em operacoes nao idempotentes;
- callbacks, webhooks ou polling infinito;
- execucao arbitraria de codigo de vendor extensions.

## 8. Provider Router e resiliencia

O Ultron publica metadados e policy defaults. O router efetivo roda no consumer
ou em servico opcional substituivel.

Entradas de selecao:

- capability e versao;
- health local e remoto;
- deadline e timeout total;
- classe de dado;
- scopes autorizados;
- custo/orcamento;
- quota/rate limit restante;
- regiao e termos;
- qualidade historica;
- preferencia explicita do usuario.

Regras:

- circuit breaker por provider/operacao;
- retry limitado com backoff e jitter somente quando seguro;
- budget total inclui todos os fallbacks;
- `PERSONAL`/`SENSITIVE` nao migra para provider menos privado;
- mutacao usa idempotency key quando suportada;
- fallback e sempre registrado no Usage Ledger;
- esgotamento produz erro tipado, nunca resposta silenciosamente vazia.

Erros normalizados:

- `PROVIDER_AUTH_ERROR`;
- `PROVIDER_RATE_LIMITED`;
- `PROVIDER_TIMEOUT`;
- `PROVIDER_UNAVAILABLE`;
- `PROVIDER_INVALID_RESPONSE`;
- `PROVIDER_POLICY_DENIED`;
- `PROVIDER_QUOTA_EXCEEDED`;
- `PROVIDER_DATA_CLASS_DENIED`.

## 9. Health, quota, custo e observabilidade

### Health Manager

Estados: `unknown`, `healthy`, `degraded`, `rate_limited`, `auth_error`,
`unavailable`, `disabled`, `revoked`.

Health checks devem ser baratos, sem efeito colateral e sem expor secrets.
Provider sem endpoint adequado usa resultado de chamadas reais anonimizado, nao
trafego sintetico caro ou destrutivo.

### Rate Limit Manager

Registra limites conhecidos, headers observados, consumo, reset, confianca da
estimativa e escopo (secret/user/org/provider). Desconhecido significa politica
conservadora, nao ilimitado.

### Usage Ledger

Registra capability, provider, operacao, latencia, resultado, fallback, quota e
custo estimado por correlation ID. Conteudo e credentials nao sao persistidos
por padrao.

Metricas minimas:

- success/error rate por capability/provider;
- p50/p95/p99;
- fallback e circuit-open rate;
- auth/rate-limit/quota errors;
- custo estimado e divergencia;
- freshness de health/pricing/terms;
- adapters candidatos, rejeitados, quarentenados e ativados.

## 10. Catalogo inicial e prioridade

O catalogo pode indexar muitos candidatos, mas o gate funcional usa poucos
providers de prova:

1. `weather.get_forecast` — Open-Meteo, sem secret;
2. `currency.quote` — AwesomeAPI ou Frankfurter;
3. `research.read_url` — Jina Reader, sujeito a politica;
4. `security.scan_url` — provider defensivo aprovado;
5. APIs.guru — fonte de discovery, nao provider de runtime.

Model providers, research completo, security pack e dominios opcionais entram
depois que a fundacao provar seguranca, health, fallback e observabilidade.

## 11. Dependencias no roadmap

- U6 fornece persistencia/RLS cloud para novos manifests;
- U7 fornece identidade, roles e secrets references;
- U8 implementa schemas e registries de capability/provider;
- U9 implementa discovery OpenAPI e geracao segura;
- U10 expoe contratos REST/MCP de producao;
- U11 protege artefatos/specs/adapters na supply chain;
- U12 executa parsing/testes em workers isolados;
- U13 apresenta catalogo, health, aprovacao e lineage no portal;
- U14 entrega CLI/SDK e integracao real com consumers;
- U15 mede health, usage, custo e fallbacks;
- U17 valida carga e degradacao;
- U18 audita seguranca/privacidade;
- U19 realiza beta e aceite final.

## 12. Estrategia de testes

### Unidade e propriedades

- manifests strict/frozen e round-trip canonico;
- SemVer e compatibilidade de schemas;
- normalizacao de erros;
- roteamento/fallback deterministico;
- budgets, quotas, cache e data classification;
- fuzz/property tests para OpenAPI e JSON Schema.

### Contrato

- specs OpenAPI 3.0/3.1/3.2 e Swagger/OpenAPI 2.0 suportadas explicitamente;
- fixtures validas e invalidas versionadas;
- adapter respeita request/response e auth declarados;
- equivalencia entre tool schema, REST, MCP e SDK.

### Seguranca

- SSRF, redirects, DNS rebinding e hosts privados;
- reference/schema bombs e payload excessivo;
- extensoes maliciosas, descriptions hostis e code injection;
- secret leakage em logs, manifests, erros e artefatos;
- cross-tenant, scope escalation e provider substitution;
- assinatura, adulteracao, revogacao e quarentena.

### Integracao e E2E

- discovery → candidate → generation → validation → publish disabled;
- ativacao explicita → chamada por capability → output normalizado;
- health degrada provider → fallback permitido;
- fallback proibido por privacidade falha fechado;
- Ultron offline → consumer usa adapter instalado/cache integro;
- provider offline → consumer preserva capacidades nativas;
- agent, skill, workflow e pack resolvem capability sem acoplamento ao provider.

## 13. Criterios de aceite globais

Uma capability/provider so e aprovada quando:

- utilidade, contrato, owner e lifecycle estao claros;
- HTTPS, hosts, auth, terms, pricing e rate limits foram avaliados;
- schemas de input/output e erros sao validados;
- secrets usam referencia e cofre apropriado;
- classes de dados e politica de envio estao declaradas;
- health, timeout, cache, quota e observabilidade existem;
- fallback e idempotencia possuem comportamento documentado;
- testes unitarios, contrato, integracao, seguranca e E2E passam;
- artefatos possuem digest, proveniencia, assinatura e SBOM quando aplicavel;
- publicacao nasce desabilitada e ativacao exige autoridade;
- operacao offline do consumer permanece valida;
- documentacao corresponde ao codigo e infraestrutura.

## 14. Anti-gates especificos

U8/U9 nao podem ser aprovados se:

- capability for apenas uma string sem contrato versionado;
- provider expuser secret ou valor de credential;
- catalogo confundir discovery com aprovacao;
- OpenAPI remota for executada ou confiada automaticamente;
- gerador habilitar tool sem review/policy;
- host, redirect ou referencia externa escapar da allowlist;
- fallback alterar privacidade ou efeito sem consentimento;
- health central for tratado como verdade operacional absoluta;
- consumer exigir Ultron online para executar o que ja instalou;
- agent/skill/workflow/pack depender diretamente do SDK do fornecedor.

## 15. Atualizacao de padroes e confianca das fontes

Na data desta decisao, a linha mais recente publicada pela OpenAPI Initiative e
OpenAPI 3.2. O U9 deve testar 3.0, 3.1, 3.2 e o legado 2.0 explicitamente, sem
interpretar minor/major desconhecida de forma permissiva.

O APIs.guru e um diretorio comunitario: uma entrada pode nao ser publicada pelo
dono da API, estar desatualizada ou apontar para servico encerrado. Portanto:

- APIs.guru tem `source_trust: community_discovery`;
- `official: true/false/unknown` e preservado como metadado, nao como veredito;
- a spec e copiada por digest e validada independentemente;
- health, termos, auth, hosts, disponibilidade e ownership sao reconfirmados;
- catalogo comunitario nunca concede status `validated` automaticamente.

Fontes normativas e de referencia:

- OpenAPI 3.2: https://spec.openapis.org/oas/v3.2.0.html
- indice oficial de versoes/schemas: https://spec.openapis.org/oas/
- OpenAPI 2.0: https://spec.openapis.org/oas/v2.0.html
- APIs.guru OpenAPI Directory: https://github.com/APIs-guru/openapi-directory
