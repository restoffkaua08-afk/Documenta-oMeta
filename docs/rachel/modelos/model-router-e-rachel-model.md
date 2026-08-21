# Rachel - Model Router e Rachel Model

## Provider atual registrado

As fontes mencionam uso local via Ollama e baseline temporaria `qwen3:1.7b`. Isso nao equivale a um modelo especializado da Rachel.

## Objetivo do Model Router

Selecionar modelo/provider conforme:

- tipo e complexidade da tarefa;
- necessidade de tools/structured output;
- privacidade;
- contexto requerido;
- latencia;
- custo;
- disponibilidade local/cloud;
- politica do usuario;
- historico de qualidade.

## Interface esperada

```text
ModelRequest
  task_type
  messages/context
  capabilities
  privacy_class
  latency/cost budget
  preferred/forbidden providers

ModelResponse
  provider/model
  content/stream
  usage and latency
  finish/error state
  trace metadata
```

## Fallback

Fallback e permitido somente entre providers autorizados. Ele nao pode enviar dados classificados para cloud apenas porque o modelo local falhou.

## Rachel Model v0.1

O ciclo legado definiu um candidato futuro baseado em `Qwen/Qwen3-1.7B-Base`, mas registrou:

- checkpoint nao criado;
- treinamento nao executado;
- weights nao modificados;
- avaliacao nao produzida;
- promocao bloqueada;
- thresholds nao calibrados.

Portanto, nao existe evidencia de um Rachel Model treinado.

## Condicao para experimento

O roadmap atual coloca o modelo proprio como item condicional da Etapa 15. Requisitos:

1. hardware/runtime adequado;
2. dataset autorizado e versionado;
3. baseline executada;
4. plano de treino reproduzivel;
5. checkpoint e linhagem;
6. avaliacao da Dany profissional;
7. comparacao de regressao;
8. decisao explicita de promocao;
9. rollback;
10. ausencia de bloqueio para Rachel funcionar sem ele.

## Hardware registrado

Um relatorio fornecido descreve um HP 250 G9, i3-1215U, cerca de 8 GB de RAM e GPU Intel sem NVIDIA. Esse dado pertence ao host auditado naquele momento e nao deve ser assumido como hardware permanente do projeto.

