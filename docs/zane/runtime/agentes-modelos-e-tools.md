# Zane - Runtime, Agentes, Modelos e Tools

## Agent Runtime

O runtime coordena intencao, plano, autorizacao, execucao, verificacao e conclusao. Autonomia e limitada por budgets, politica e cancelamento.

## Perfis internos

O Zane pode usar agentes especializados, mas a interface principal permanece unica. Perfis declaram capacidades e nao funcionam como permissoes.

## Agent Loop

```text
observe
  -> plan
  -> policy/approval
  -> execute
  -> verify
  -> continue | pause | complete | fail
```

Limites: iteracoes, tool calls, tempo, custo, falhas consecutivas e profundidade de delegacao.

## Model Router

Seleciona modelos locais/cloud conforme tarefa, capacidade, privacidade, contexto, latencia e custo. Fallback nao altera classificacao de dados.

## Tool Runtime

Tools sao tipadas, versionadas, governadas e verificaveis. Categorias nativas previstas: filesystem, Git, dev, processos, web, browser, APIs, conhecimento, MCP e dispositivos.

## Skills

Skills compoem capacidades existentes. Elas nao ampliam privilegios e devem declarar dependencias, inputs, outputs, riscos e testes.

## Jobs

Tarefas longas possuem estado duravel, heartbeat, cancelamento, retry controlado, checkpoints e resultados. Worker nao recebe permissao maior que o job.

## Eventos

Clientes recebem eventos versionados de runtime, plano, approval, tool, modelo, job, erro e conclusao.

## Verificacao

Resultado de tool nao e automaticamente objetivo concluido. Verificadores confirmam arquivos, testes, builds, estado remoto ou outros criterios definidos no plano.

