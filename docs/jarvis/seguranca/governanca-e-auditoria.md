# Jarvis - Seguranca, Governanca e Auditoria

## Autoridade

Jarvis standalone precisa de politica propria. Quando integrado temporariamente com Rachel, a politica externa pode impor restricoes adicionais, nunca reduzir silenciosamente as protecoes internas.

## Principios

- menor privilegio;
- deny-by-default;
- separacao entre planejar e autorizar;
- tool + argumentos fazem parte da decisao;
- escopo por sessao/job;
- cancelamento propagado;
- segredos redigidos;
- auditoria correlacionada.

## Pipeline

```text
agent proposal
  -> ToolSpec/schema
  -> policy evaluation
  -> approval when required
  -> executor
  -> verifier
  -> audit event
```

## Sandbox

Codigo, terminal e browser devem usar isolamento proporcional. Sandboxes precisam de limites de filesystem, rede, processo, tempo e recursos.

## Credenciais

Agentes recebem referencias/handles, nao o inventario completo de segredos. A tool resolve credencial somente no momento autorizado e nao a devolve ao modelo.

## Prompt injection

Web, repositorios, documentos, issues, tool output e MCP sao entradas nao confiaveis. Elas nao podem alterar politica nem solicitar exfiltracao.

## Auditoria

Eventos devem registrar request, agent, plan, tool, argumentos sanitizados, decisao de politica, approval, resultado, duracao e estado final.

## Kill switch e limites

O runtime deve permitir cancelamento de job, bloqueio de tools/classes, desativacao de provider e encerramento seguro. Budgets evitam loops e consumo ilimitado.

## Supply chain

- fixar dependencias;
- auditar updates upstream;
- validar MCP/skills antes de ativar;
- verificar manifests e assinaturas quando disponiveis;
- impedir execucao automatica de codigo recebido por pack.

