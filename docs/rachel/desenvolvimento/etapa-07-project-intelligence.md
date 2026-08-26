# Rachel — Etapa 07: Project Intelligence

**Estado:** VALIDATED  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

A Etapa 07 consolida a inteligencia de projeto da Rachel para que tarefas de desenvolvimento trabalhem sobre contexto relevante e limitado, em vez de carregar repositorios inteiros ou depender de selecao manual de arquivos.

## Capacidades validadas

A `main` possui e valida:

- descoberta de projeto;
- leitura de manifests e deteccao de linguagens/build systems;
- repo map limitado;
- mapa de dependencias para Node, Python e Rust;
- indice de simbolos para Python e JavaScript/TypeScript;
- busca textual ranqueada;
- `working_set` limitado por relevancia;
- instrucoes persistentes de projeto;
- memoria de decisoes arquiteturais;
- `project.context` com conteudo real;
- budget conservador de contexto;
- integracao com planejamento de Ned e Agent Loop.

## Evidencias principais

### Escala e working set

Commit:

- `e93760e89ba0c96ebd7f5db85caa8856ab7eaebc` — teste com projeto sintetico de 500 arquivos.

Resultado comprovado:

- working set < 20 arquivos;
- arquivos relevantes permanecem selecionados.

### Budget de contexto

Arquivos:

- `RACHEL_PLATFORM/RUNTIME/SRC/context_budget.py`;
- `RACHEL_PLATFORM/RUNTIME/SRC/project_context_provider.py`.

Commits:

- `aff73349062df97fca08c250f6596c4c3bec2aa2`;
- `c31a86fcde7ebf00cb84d78e1b42e6f94f79c60c`;
- `f94f850c436c0470c82ae7883b1d284b82ae6a0a`;
- `9d309d112a894f9c0aba054579633f0471753be6`.

Hard caps validados:

- ate 8.000 tokens estimados;
- ate 19 arquivos.

### Contexto real

`ProjectIntelligenceRuntime.context_for()` passou a ler conteudo real dos arquivos selecionados e aplicar o budget.

Commits:

- `41a9387bd6e35b44e7dd4e2a54fbc6e5f460c59e`;
- `f416dcd81cf11364ebcfc2e36591dd0bf3128b56`.

### Tool `project.context`

O `ToolCoordinator` passou a servir contexto real limitado.

Commits:

- `486b8698f7df71d44f911846b9c16a21eb079a29`;
- `05a0b148b69a31c34ab12e55d01383e40706167c`.

A CI completa passou em Python Core + Runtime, frontend e Tauri.

### Planejamento e Agent Loop

Foi criada a fronteira:

- `RACHEL_PLATFORM/RUNTIME/SRC/project_planning_context.py`.

Fluxo final:

```text
AgentLoopRuntime
    -> TaskOrchestrator
        -> project_planning_context
            -> ProjectContextProvider
                -> ProjectIntelligenceRuntime.context_for()
```

Commits:

- `13fadb0da623f6929a6cf2d1cf86266eee47825d`;
- `e630b864002faf3419ec348794f0087ce2e248ba`;
- `8383eb22ae0e83b2bf570c1eaf9739694213c874`;
- `6126f2134f009bad3f707989d35c0f58d230fc70`.

O workflow `tests` do commit final `6126f2134f009bad3f707989d35c0f58d230fc70` concluiu com sucesso em Core e Runtime. O teste prova que o Agent Loop recebe contexto limitado pelo mesmo boundary de planejamento e que objetivos comuns nao disparam scan desnecessario.

## Gate final

- project discovery: **PASS**;
- repo map: **PASS**;
- dependency map: **PASS**;
- symbol index Python/JS/TS: **PASS**;
- projeto de 500 arquivos com working set < 20: **PASS**;
- contexto real <= 8.000 tokens estimados: **PASS**;
- <= 19 arquivos: **PASS**;
- memoria/instrucoes de projeto: **PASS**;
- `project.context`: **PASS**;
- planejamento de Ned usando contexto limitado: **PASS**;
- Agent Loop usando contexto limitado: **PASS**;
- CI final da etapa: **PASS**.

## Resultado

**ETAPA 07 — VALIDATED.**

A Rachel agora possui Project Intelligence limitada por budget e integrada ao fluxo agentic sem carregar o repositorio inteiro no contexto. A proxima etapa oficial e a Etapa 08 — Dany profissional (grounding e factualidade).