# Rachel - Runtime Cognitivo

## Objetivo

O Runtime Cognitivo transforma uma mensagem em resposta ou acao governada. Ele deve manter uma unica linha de decisao, evitando cerebros divergentes entre chat, desktop e execucao.

## Componentes documentados

| Componente | Responsabilidade registrada |
|---|---|
| `NedCognitiveBridge` | entrada cognitiva e integracao entre conversa e planejamento |
| `NedToolPlanner` | gera plano de ferramentas quando necessario |
| `IntentRouter` | identifica conversa, acao e rotas deterministicas |
| `CognitiveMemory` | persistencia/recall cognitivo local |
| `ApprovalStore` | ciclo de aprovacoes governadas |
| `ToolCoordinator` | registry, validacao e invocacao de tools |
| `ResidentBridge` | mantem runtime/provider/servicos residentes entre requests |

Os nomes provem dos documentos analisados e precisam ser verificados contra o codigo atual.

## Estados de uma solicitacao

```text
received
  -> classified
  -> planned (quando necessario)
  -> awaiting_approval (quando necessario)
  -> executing
  -> verifying
  -> completed | denied | cancelled | failed | partial
```

Somente `completed` com evidencia suficiente pode ser exposto como sucesso.

## Fast path

Saudacoes, conversa cotidiana e perguntas que nao exigem ferramentas devem evitar planner adicional. O fast path reduz latencia e custo, mas nao pode ignorar memoria/contexto autorizado nem seguranca de entrada.

## Planning path

Solicitacoes de acao produzem plano tipado. Cada passo deve indicar ferramenta, argumentos, dependencia, efeito esperado, risco e criterio de verificacao.

## Retomada apos aprovacao

A aprovacao deve retomar exatamente o plano exibido e aprovado. Replanejar silenciosamente apos a autorizacao altera os argumentos autorizados e viola o contrato de seguranca.

Requisitos:

- plano aprovado possui identificador e hash/versao;
- aprovacao referencia o plano e o passo;
- autorizacao e consumida uma vez;
- alteracao de argumentos exige nova aprovacao;
- negacao encerra ou replaneja de forma explicita, sem executar;
- o plano sensivel nao depende de JSON temporario inseguro no IPC.

## Streaming

Eventos minimos:

- `chat.started`;
- `chat.delta`;
- `chat.completed`;
- `chat.cancelled`;
- `chat.error`;
- estados de planejamento e ferramenta.

O texto parcial nao deve ser salvo como resposta completa quando houver cancelamento ou falha.

## Cancelamento

O servidor residente deve receber cancelamento enquanto a geracao esta ativa. Tarefas multi-etapas exigem semantica adicional de pausa, cancelamento, rollback e retomada; isso pertence ao Agent Loop.

## Metricas

- `total_ms`: solicitacao completa;
- `ttft_ms`: tempo ate o primeiro chunk real;
- `duration_ms` de ferramenta: somente executor real;
- planejamento, provider, fila e sintese devem ter duracoes separadas quando disponiveis;
- valores desconhecidos permanecem nulos, nunca estimados como fato.

## Falhas

- provider indisponivel: fallback permitido ou erro claro;
- tool falha: estado `failed`, saida limitada e evidencia;
- aprovacao expira: nao executar;
- bridge cai: cancelar ou permitir recuperacao definida;
- evento invalido: rejeitar sem corromper sessao;
- memoria falha: sinalizar degradacao e aplicar politica de continuidade.

