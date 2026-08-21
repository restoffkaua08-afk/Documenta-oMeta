# Rachel - Tool Runtime Profissional

## Estado documental

O documento `LOTE-3-TOOL-RUNTIME-ACCEPTANCE.md` declara o lote concluido e validado no CI. A conclusao definitiva permanece condicionada ao confronto com a branch e a reproducao dos testes.

## Arquitetura

```text
IntentRouter/Planner
  -> ToolSpec selecionada
  -> validacao de schema
  -> Cyber/politica
  -> aprovacao quando exigida
  -> ToolCoordinator
  -> executor tipado
  -> verificacao
  -> ToolResult + eventos + metricas
```

## Contrato de ToolSpec

Campos recomendados:

- nome e versao;
- descricao funcional;
- schema de entrada e saida;
- efeitos e risco;
- escopos permitidos;
- timeout;
- idempotencia;
- capacidade de rollback;
- politica de aprovacao;
- executor e verificador.

## Filesystem tipado registrado

`filesystem.status`, `list`, `stat`, `read`, `search`, `mkdir`, `write`, `patch`, `copy`, `move` e `delete`.

Escopos mencionados: `workspace`, `desktop`, `documents` e `downloads`.

Garantias registradas incluem confinamento de caminho, escrita atomica, backup, patch com match unico, verificacao de mutacoes e delete conservador.

## Git tipado registrado

`git.status`, `diff`, `log`, `branches`, `stage`, `commit`, `branch.create` e `checkout`.

Regras:

- `shell=False`;
- stage e commit sao autorizacoes separadas;
- commit usa somente conteudo staged;
- criar branch nao troca branch silenciosamente;
- `git.push` ficou fora do escopo do lote.

## Desenvolvimento e validacao

`dev.detect`, `test`, `build`, `lint` e `typecheck` selecionam comandos conhecidos pelo runtime conforme manifests e lockfiles. O modelo nao fornece uma linha arbitraria.

## Processos governados

`process.start`, `list`, `status`, `logs` e `stop` operam perfis permitidos e processos pertencentes a Rachel.

## Fallback generico

`arya.run` permanece apenas como fallback governado. As fontes registram bloqueio de shells e interpretadores, resolucao pelo PATH, limites de argumentos e aprovacao obrigatoria.

## ToolResult

Deve distinguir:

- `success` real;
- `return_code` quando aplicavel;
- saida limitada e sanitizada;
- evidencias do efeito;
- duracao exclusiva do executor;
- erro tipado;
- verificacao final;
- possibilidade de rollback.

## Pendencias para o Agent Loop

Tools isoladas nao formam um agente completo. Ainda sao necessarios encadeamento de passos, budgets, pausa/retomada, compensacao, politica de retries e verificacao de objetivo.

