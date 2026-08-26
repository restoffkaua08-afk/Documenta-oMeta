# Rachel — Etapa 07: Project Intelligence

**Estado:** Em validacao e endurecimento incremental  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

A Etapa 07 consolida a inteligencia de projeto da Rachel para que tarefas de desenvolvimento trabalhem sobre contexto relevante e limitado, em vez de carregar repositorios inteiros ou depender de selecao manual de arquivos.

O gate da etapa exige, entre outros pontos, descoberta de projeto, repo map, indice de simbolos, working set pequeno, memoria de projeto e contexto limitado antes de integracao definitiva ao Agent Loop.

## Estado encontrado no codigo

A auditoria da `main` confirmou que a base de Project Intelligence ja existia antes desta etapa de endurecimento.

Arquivo principal:

- `RACHEL_PLATFORM/RUNTIME/SRC/project_intelligence_runtime.py`

Capacidades ja existentes:

- descoberta de projeto;
- leitura de manifests;
- contagem de linguagens;
- regras de ignore com `.gitignore` e diretorios padrao;
- repo map limitado;
- mapa de dependencias para `package.json`, `pyproject.toml`, `requirements.txt` e `Cargo.toml`;
- indice de simbolos para Python e JavaScript/TypeScript;
- busca textual ranqueada;
- `working_set` limitado e ranqueado por conteudo/path/simbolos;
- instrucoes persistentes de projeto;
- memoria de decisoes arquiteturais reutilizando Bran/CognitiveMemory;
- exposicao via ToolCoordinator com gates de Cyber para escrita.

## Validacao incremental realizada

### Working set em repositorio grande

Foi acrescentado teste para um projeto sintetico com 500 arquivos, verificando que:

- o working set permanece abaixo de 20 arquivos;
- os arquivos semanticamente relevantes continuam presentes;
- a estrategia continua `bounded-working-set`.

Commit:

- `e93760e89ba0c96ebd7f5db85caa8856ab7eaebc` — `test(project): validar working set limitado em repositorio grande`.

O workflow `tests` do commit concluiu com sucesso.

### Orçamento de contexto

Foi criada uma camada pequena e isolada para impor budget conservador antes da integracao no runtime principal:

- `RACHEL_PLATFORM/RUNTIME/SRC/context_budget.py`.

A implementacao define:

- limite padrao de 8.000 tokens estimados;
- limite padrao de 19 arquivos;
- estimativa conservadora de 3 caracteres por token para contexto de codigo/texto;
- preservacao da ordem de relevancia;
- truncamento somente quando necessario;
- metadados de budget e truncamento.

Commits:

- `aff73349062df97fca08c250f6596c4c3bec2aa2` — `feat(project): adicionar orçamento conservador de contexto`;
- `c31a86fcde7ebf00cb84d78e1b42e6f94f79c60c` — `test(project): validar limites do orçamento de contexto`.

Testes especificos verificam:

- estimativa deterministica;
- maximo de 19 arquivos;
- respeito ao budget de tokens;
- preservacao da ordem dos itens ranqueados.

### Contexto real integrado ao Project Intelligence

O `ProjectIntelligenceRuntime` passou a possuir `context_for()`, que:

- parte do ranking existente do working set;
- le o conteudo real dos arquivos selecionados;
- aplica hard cap de 8.000 tokens estimados;
- aplica hard cap de 19 arquivos;
- preserva a ordem de relevancia;
- informa metadados de truncamento e budget.

Commits:

- `41a9387bd6e35b44e7dd4e2a54fbc6e5f460c59e` — `feat(project): gerar contexto real dentro do orçamento`;
- `f416dcd81cf11364ebcfc2e36591dd0bf3128b56` — `test(project): validar contexto real dentro do orçamento`.

O workflow `tests` do commit `f416dcd81cf11364ebcfc2e36591dd0bf3128b56` concluiu com sucesso.

### Boundary para planejamento e Agent Loop

Foi criada uma camada pequena de integracao:

- `RACHEL_PLATFORM/RUNTIME/SRC/project_context_provider.py`.

Ela fornece `ProjectContextProvider`, que normaliza `scope`, `path` e `task`, aplica novamente os hard caps da etapa e delega exclusivamente a `ProjectIntelligenceRuntime.context_for()`.

Objetivo da camada:

- impedir que planner/Agent Loop dependam diretamente de detalhes internos do indexador;
- manter um unico contrato pequeno para contexto de projeto;
- preservar os limites de 8.000 tokens e 19 arquivos mesmo se um chamador solicitar valores maiores;
- permitir integracao posterior com baixo risco e testes isolados.

Commits:

- `f94f850c436c0470c82ae7883b1d284b82ae6a0a` — `feat(project): adicionar provider de contexto para planejamento`;
- `9d309d112a894f9c0aba054579633f0471753be6` — `test(project): validar provider de contexto para planejamento`.

Os testes verificam normalizacao de entrada, delegacao ao runtime, hard caps e rejeicao de `scope`/`task` vazios. O workflow `tests` do commit `9d309d112a894f9c0aba054579633f0471753be6` concluiu com sucesso, incluindo Core e Runtime.

### `project.context` passa a servir contexto real limitado

O `ToolCoordinator` deixou de expor apenas o `working_set` pela ferramenta `project.context` e passou a chamar `ProjectIntelligenceRuntime.context_for()`.

Com isso, a ferramenta agora retorna os conteudos reais dos arquivos selecionados dentro do budget da etapa, em vez de apenas a lista ranqueada de caminhos/metadados.

A alteracao tambem reduziu o limite solicitado pela tool para o hard cap oficial de 19 arquivos. O budget de tokens continua sendo aplicado internamente pelo `context_for()` com teto de 8.000 tokens estimados.

Commit:

- `486b8698f7df71d44f911846b9c16a21eb079a29` — `feat(project): servir contexto limitado pela tool project.context`.

A CI completa `RACHEL CI` desse commit foi iniciada e ainda esta em execucao. A mudanca nao deve ser considerada evidência final ate os jobs Python/Core/Runtime, frontend e Tauri concluirem verdes.

## Estrategia de integracao segura

A Etapa 07 esta sendo dividida em micro-lotes deliberadamente pequenos. Primeiro foi validado o ranking, depois o budget isolado, depois o contexto real, depois o boundary para planejamento e agora a tool `project.context`. O Agent Loop somente deve ser alterado depois que esta integracao estiver verde na CI.

## Proximos micro-lotes

1. aguardar a CI do commit `486b8698f7df71d44f911846b9c16a21eb079a29`;
2. adicionar teste especifico provando que `project.context` retorna conteudo real limitado e respeita o hard cap de arquivos;
3. validar novamente regressao completa;
4. conectar contexto limitado ao Agent Loop/planning em um lote separado;
5. adicionar teste E2E pequeno demonstrando que o Agent Loop recebe contexto limitado;
6. reconciliar e fechar o gate/documentacao da Etapa 07.

## Gate atual

- project discovery: **IMPLEMENTED / TESTED**;
- repo map: **IMPLEMENTED / TESTED**;
- dependency map: **IMPLEMENTED / TESTED**;
- symbol index: **IMPLEMENTED / TESTED**;
- bounded working set: **IMPLEMENTED / TESTED**;
- escala de 500 arquivos com working set < 20: **PASS**;
- budget de contexto isolado: **IMPLEMENTED / TESTED**;
- contexto real limitado integrado ao Project Intelligence: **IMPLEMENTED / TESTED**;
- provider de contexto para planejamento: **IMPLEMENTED / TESTED**;
- caminho `project.context` servindo contexto real limitado: **IMPLEMENTED / CI IN PROGRESS**;
- teste especifico da tool `project.context`: **PENDING**;
- Agent Loop consumindo contexto limitado: **PENDING / A AUDITAR**;
- gate completo da Etapa 07: **NOT YET CLOSED**.

A etapa nao deve ser marcada como `VALIDATED` enquanto a integracao com planejamento/Agent Loop nao estiver demonstrada por codigo e testes.
