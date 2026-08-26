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

## Razao para separar o budget antes de integrar

A mudanca foi deliberadamente dividida em um modulo pequeno antes de tocar `ProjectIntelligenceRuntime` ou Agent Loop. Isso reduz o risco de regressao e permite validar a regra de budget isoladamente.

A proxima alteracao somente deve ocorrer depois da CI do commit `c31a86fcde7ebf00cb84d78e1b42e6f94f79c60c` concluir verde.

## Proximos micro-lotes

1. integrar `context_budget.py` ao `ProjectIntelligenceRuntime` sem alterar o ranking atual do working set;
2. produzir contexto real dos arquivos selecionados sob budget de <= 8.000 tokens estimados e < 20 arquivos;
3. adicionar teste de integracao do contexto;
4. somente depois conectar esse contexto ao Agent Loop/planning;
5. revalidar regressao completa;
6. reconciliar o gate/documentacao da Etapa 07.

## Gate atual

- project discovery: **IMPLEMENTED / TESTED**;
- repo map: **IMPLEMENTED / TESTED**;
- dependency map: **IMPLEMENTED / TESTED**;
- symbol index: **IMPLEMENTED / TESTED**;
- bounded working set: **IMPLEMENTED / TESTED**;
- escala de 500 arquivos com working set < 20: **PASS**;
- budget de contexto isolado: **IMPLEMENTED**;
- testes do budget: **COMMITTED / CI IN PROGRESS**;
- contexto real limitado integrado ao Project Intelligence: **PENDING**;
- Agent Loop consumindo contexto limitado: **PENDING / A AUDITAR**;
- gate completo da Etapa 07: **NOT YET CLOSED**.

A etapa nao deve ser marcada como `VALIDATED` enquanto os dois ultimos pontos nao estiverem demonstrados por codigo e testes.
