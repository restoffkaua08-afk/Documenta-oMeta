# Rachel - Evidencias das Etapas 1 a 4

## Etapa 1 - CI minimo

- Status documental: concluida.
- Branch: `evolution/rachel-professional-agent`.
- Commit: `0d27273`.
- Baseline: 372 testes aprovados, 11 xfails documentados e 5 subtestes aprovados.
- Pendente manual: branch protection no GitHub.

## Etapa 2 - Cérebro unico e intent router

O contrato de aceite exige retomada exata apos aprovacao Cyber, ausencia de replanejamento, validacao de ferramenta e argumentos, tratamento correto de estados incompletos e metadados de evidencia.

Status: implementacao indicada pelos documentos, mas sem uma declaracao final inequivoca no material analisado.

## Etapa 3 - Runtime persistente e streaming

Garantias registradas:

- backend residente;
- IPC NDJSON persistente;
- streaming real de provider compativel com OpenAI;
- cancelamento sem persistir resposta parcial como concluida;
- metricas separadas de TTFT, total e ferramenta;
- builds e checks registrados como aprovados.

Status: implementada, sujeita a reconfirmacao do gate final na branch.

## Etapa 4 - Tool Runtime profissional

O documento de aceite declara `CONCLUIDO E VALIDADO NO CI`.

Capacidades registradas:

- filesystem tipado e confinado por escopo;
- Git tipado sem shell;
- validacao de projetos;
- processos pertencentes a Rachel;
- fallback generico endurecido;
- autorizacoes Cyber de uso unico;
- metricas e observabilidade.

Status: declarada concluida documentalmente; a auditoria de codigo deve confirmar a evidencia.

