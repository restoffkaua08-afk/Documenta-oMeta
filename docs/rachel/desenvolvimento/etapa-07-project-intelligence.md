# Rachel — Etapa 07: Project Intelligence

**Estado:** Em validacao final  
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

### Orcamento de contexto

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

Os testes verificam normalizacao de entrada, delegacao ao runtime, hard caps e rejeicao de `scope`/`task` vazios. O workflow do ultimo commit concluiu com sucesso.

### `project.context` passa a servir contexto real limitado

O `ToolCoordinator` deixou de expor apenas o `working_set` pela ferramenta `project.context` e passou a chamar `ProjectIntelligenceRuntime.context_for()`.

Com isso, a ferramenta agora retorna os conteudos reais dos arquivos selecionados dentro do budget da etapa, em vez de apenas a lista ranqueada de caminhos/metadados.

A alteracao tambem reduziu o limite solicitado pela tool para o hard cap oficial de 19 arquivos. O budget de tokens continua sendo aplicado internamente pelo `context_for()` com teto de 8.000 tokens estimados.

Commit:

- `486b8698f7df71d44f911846b9c16a21eb079a29` — `feat(project): servir contexto limitado pela tool project.context`.

A CI completa `RACHEL CI` desse commit concluiu verde nos tres jobs: Python Core + Runtime contracts, Desktop frontend build e Tauri Rust check.

### Teste especifico da tool `project.context`

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_project_context_tool.py`.

O teste monta um projeto sintetico com 40 arquivos e valida o caminho completo via `ToolCoordinator.invoke("project.context", ...)`.

Ele exige que:

- a tool conclua com `state=completed`;
- o resultado tenha no maximo 19 arquivos;
- a estimativa permaneça em no maximo 8.000 tokens;
- cada item retornado possua `content` real;
- o arquivo relevante seja selecionado e exponha o simbolo esperado.

Commit:

- `05a0b148b69a31c34ab12e55d01383e40706167c` — `test(project): validar contexto limitado via tool coordinator`.

A `RACHEL CI` desse commit concluiu verde nos jobs Python Core + Runtime, Desktop frontend build e Tauri Rust check.

### Contexto limitado no planejamento de Ned e no Agent Loop

A auditoria mostrou que o `AgentLoopRuntime` cria seus planos pelo `TaskOrchestrator`. Em vez de acoplar o loop diretamente ao indexador, a integracao foi feita no boundary de planejamento utilizado por ambos.

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/project_planning_context.py`.

Essa camada:

- identifica objetivos orientados a projeto/codigo/repository;
- solicita contexto ao `ProjectContextProvider` somente para esse tipo de tarefa;
- usa `workspace` e caminho `.` como raiz governada;
- mantem os hard caps definidos pelo provider;
- nao faz scan de projeto em objetivos comuns que nao necessitam contexto;
- empacota o contexto em um bloco explicito `[PROJECT_CONTEXT_BOUNDED]` para o planejador.

Commit:

- `13fadb0da623f6929a6cf2d1cf86266eee47825d` — `feat(project): preparar contexto limitado para planejamento`.

O `TaskOrchestrator.model_specifications()` foi entao conectado a essa camada. Como o `AgentLoopRuntime.start()` usa `TaskOrchestrator.create_plan()` quando o plano e gerado pelo modelo, tarefas profissionais de projeto passam pelo mesmo caminho e recebem o contexto limitado antes da criacao das etapas.

Commit:

- `e630b864002faf3419ec348794f0087ce2e248ba` — `feat(project): integrar contexto limitado ao planejamento`.

Foi criado o teste:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_project_planning_context.py`.

O teste valida:

- injecao de contexto real em tarefa de projeto;
- limite de 8.000 tokens e 19 arquivos na chamada ao runtime;
- ausencia de scan/contexto em tarefa comum;
- passagem do contexto pelo caminho real `AgentLoopRuntime -> TaskOrchestrator -> model planning`, usando `execute=False` para isolar o gate de planejamento sem executar tools.

Commits:

- `8383eb22ae0e83b2bf570c1eaf9739694213c874` — `test(project): validar contexto no planejamento do agente`;
- `6126f2134f009bad3f707989d35c0f58d230fc70` — `test(agent): provar contexto limitado no agent loop`.

Os workflows do ultimo commit estao em execucao. Esta integracao somente deve ser marcada como evidencia final depois que os testes e a `RACHEL CI` concluirem verdes.

## Decisao de arquitetura

O Agent Loop nao recebeu dependencia direta de `ProjectIntelligenceRuntime`. O caminho escolhido e:

```text
AgentLoopRuntime
    -> TaskOrchestrator
        -> project_planning_context
            -> ProjectContextProvider
                -> ProjectIntelligenceRuntime.context_for
```

Isso mantem o loop desacoplado do indexador, reutiliza o mesmo boundary ja validado e permite substituir a estrategia de contexto no futuro sem reescrever o loop profissional.

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
- `project.context` servindo contexto real limitado: **IMPLEMENTED / CI PASS**;
- teste dedicado da tool `project.context`: **IMPLEMENTED / CI PASS**;
- planejamento de Ned consumindo contexto limitado: **IMPLEMENTED / CI IN PROGRESS**;
- Agent Loop recebendo contexto limitado no model planning: **IMPLEMENTED / CI IN PROGRESS**;
- gate completo da Etapa 07: **AWAITING FINAL CI**.

## Proximos passos

1. aguardar os workflows do commit `6126f2134f009bad3f707989d35c0f58d230fc70`;
2. se verdes, reconciliar o gate da Etapa 07 como validado;
3. revisar rapidamente se existe algum requisito documental do roadmap ainda sem evidencia;
4. somente depois iniciar a proxima etapa da Rachel.

A etapa nao deve ser marcada como `VALIDATED` antes da evidencia final da CI.