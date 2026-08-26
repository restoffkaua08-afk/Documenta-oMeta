# Rachel — Etapa 06: Model Router

**Estado:** Implementada e validada por CI automatizada; validacoes dependentes de providers reais permanecem separadas  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

A Etapa 06 introduz uma camada de roteamento de modelos entre o dominio da Rachel e adapters concretos de inferencia. O objetivo e permitir selecao por tipo de tarefa, privacidade e disponibilidade sem acoplar `ChatService` ou o runtime cognitivo a um unico provider.

A etapa preserva o contrato `ModelPort`: para o Core, o `ModelRouter` continua sendo apenas uma implementacao de `ModelPort`. Providers concretos sao injetados pelo bootstrap.

## Implementacao

### Router

Arquivo criado:

- `RACHEL_CORE/src/rachel_core/adapters/model_router.py`

Responsabilidades implementadas:

- perfis `fast`, `general`, `reasoning`, `coding` e `vision`;
- modos de privacidade `local-only`, `hybrid` e `cloud-enabled`;
- classificacao inicial da tarefa;
- deteccao conservadora de conteudo sensivel;
- selecao de candidatos autorizados;
- fallback entre providers permitidos;
- streaming com fallback apenas antes da emissao do primeiro chunk;
- health agregado sem consultar provider cloud quando a politica e `local-only`;
- metadados da ultima rota selecionada;
- manutencao do contrato `ModelPort` existente.

### Politica de privacidade

Arquivo criado:

- `RACHEL_PLATFORM/CONFIG/privacy.policy.json`

Default adotado:

- `mode = local-only`;
- protecao de PII habilitada;
- conteudo sensivel nao pode ser enviado a provider cloud sem alteracao explicita de politica.

A politica foi implementada no nivel do roteador e nao apenas em prompt. Em `local-only`, providers cloud sao excluidos tanto da geracao quanto do health check do router.

### Perfis de modelo

Arquivo criado:

- `RACHEL_PLATFORM/CONFIG/model.profiles.json`

Perfis iniciais:

- `fast`: provider principal local;
- `general`: provider principal local;
- `reasoning`: provider cloud opcional;
- `coding`: provider cloud opcional;
- `reasoning-local-fallback`: provider principal local para reasoning/coding/vision.

Os perfis cloud podem existir no arquivo sem tornar o provider obrigatorio. Se nenhum provider cloud for configurado, ele nao entra no conjunto de adapters disponiveis e o router utiliza fallback compativel.

### Bootstrap

Arquivo alterado:

- `RACHEL_CORE/src/rachel_core/bootstrap.py`

O bootstrap agora:

1. constroi o provider principal ja suportado pela Rachel;
2. registra provider cloud OpenAI-compatible apenas se URL e modelo forem configurados;
3. carrega perfis e politica de privacidade;
4. injeta o `ModelRouter` no `ChatService` como `ModelPort`;
5. mantem opcao de desabilitar o router para diagnostico/compatibilidade.

### Configuracao de ambiente

Arquivo alterado:

- `RACHEL_CORE/.env.example`

Variaveis adicionadas/documentadas:

- `RACHEL_MODEL_ROUTER_ENABLED`;
- `RACHEL_MODEL_PROFILES_PATH`;
- `RACHEL_PRIVACY_POLICY_PATH`;
- `RACHEL_CLOUD_MODEL_BASE_URL`;
- `RACHEL_CLOUD_MODEL_NAME`;
- `RACHEL_CLOUD_MODEL_API_KEY`;
- `RACHEL_CLOUD_MODEL_TIMEOUT_SECONDS`.

Nenhum segredo real foi adicionado ao repositorio.

## Correcao encontrada durante a implementacao

A primeira versao de selecao `local-only` permitia que um perfil local generico `general` aparecesse antes de um perfil local especifico para `reasoning`, devido apenas a prioridade numerica.

Isso contrariava o objetivo do roteamento por tarefa.

A logica foi corrigida para:

1. filtrar somente candidatos locais permitidos;
2. priorizar perfis que declaram explicitamente o `task_type` atual;
3. utilizar perfis genericos apenas como fallback.

O teste automatizado foi reforcado para exigir que reasoning em `local-only` selecione `local-fallback` e nao o perfil generico.

## Testes adicionados

Arquivo:

- `RACHEL_CORE/tests/test_model_router.py`

Cenarios cobertos:

- chat curto seleciona perfil rapido local em modo hibrido;
- planner seleciona reasoning cloud quando o modo permite e o provider existe;
- `local-only` seleciona fallback local especifico e nao consulta cloud;
- conteudo sensivel em `hybrid` permanece local;
- falha do provider cloud cai para provider local autorizado;
- streaming registra a rota escolhida;
- credenciais e PII basica sao identificadas pela protecao inicial.

## Evidencias de CI

O workflow profissional `RACHEL CI` no head `ae6dfd0cf1058a4dcf755ac707a5e4096ec56155`, run `32886817853`, concluiu integralmente com sucesso.

Jobs confirmados:

- `Python Core + Runtime contracts` — **PASS**;
- `Desktop frontend build` — **PASS**;
- `Tauri Rust check` — **PASS**.

O job Python incluiu compilacao das fontes, validacao de configuracoes JSON, suite completa do Core e suite critica de regressao do Runtime. O frontend compilou normalmente e o `cargo check` do Tauri tambem terminou verde.

Runs intermediarios cancelados durante a sequencia de commits foram consequencia da politica `cancel-in-progress` e nao representam falha funcional da implementacao.

## O que esta validado nesta etapa

- contrato do router e providers;
- roteamento por classes iniciais de tarefa;
- politica `local-only` fail-closed para cloud;
- protecao inicial de conteudo sensivel;
- fallback controlado;
- integracao com Bootstrap e ChatService;
- Core + Runtime regressions no head da etapa;
- build do frontend;
- `cargo check` do desktop Tauri.

## O que permanece dependente de ambiente real

Esta etapa nao prova, por si so:

- latencia real inferior a 1 segundo usando um Ollama/modelo real;
- qualidade comparativa entre modelos;
- disponibilidade de um provider cloud real;
- performance de um modelo vision real;
- budgets de `max_tokens`/`context_window` sendo impostos pelo adapter concreto.

Esses itens dependem de configuracao/provider real e permanecem como validacoes de ambiente, sem bloquear a conclusao da implementacao e da CI automatizada da Etapa 06.

## Gate

Estado atual do gate:

- implementacao: **PASS**;
- testes unitarios especificos: **PASS**;
- Core + Runtime regressions: **PASS**;
- frontend build: **PASS**;
- Tauri Rust check: **PASS**;
- smoke/performance com provider local real: **NOT VERIFIED / environment-dependent**;
- provider cloud real: **UNCONFIGURED / optional**.

Classificacao documental:

> `IMPLEMENTED / AUTOMATED CI VALIDATED`

A Etapa 06 esta encerrada no que depende de codigo e CI reproduzivel. Benchmarks com modelos reais continuam explicitamente separados para nao transformar ausencia de ambiente em evidencia ficticia.

## Commits da implementacao

- `cb6e0d845a368a81fc9eb490d704bbf9a8fcd64e` — cria Model Router;
- `601c68781bd259f72733aeb00ccbfa6b596e55f9` — perfis;
- `edacbfdca7c7f0d707ffd0e589dfc49d7e36ad36` — politica de privacidade;
- `4d1f645efda5f257e6f435e01713f16113f58254` — integra bootstrap;
- `6303bb276265ffda517d668e0ab91537dcf02241` — testes do router;
- `75b5fcb0388e4e25ffc70470c726c7ad860af254` — corrige prioridade local especifica;
- `df15fa8eb96e1833912c91b58b22a17af7ca524f` — reforca teste do fallback;
- `ae6dfd0cf1058a4dcf755ac707a5e4096ec56155` — documenta variaveis de ambiente.

## Proxima etapa tecnica

A proxima acao e auditar e completar somente as lacunas reais da Etapa 07 — Project Intelligence. O repositorio ja possui `project_intelligence_runtime.py`, integracao com `ToolCoordinator` e testes relacionados; portanto o desenvolvimento deve ser incremental, preservando o que ja funciona e adicionando apenas os criterios do gate ainda ausentes.
