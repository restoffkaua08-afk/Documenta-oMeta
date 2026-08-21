# Zane - Control, Execution e Data Plane

## Control Plane

Responsabilidades:

- usuarios, tenants e dispositivos;
- configuracao e feature flags;
- politicas e permissoes;
- approvals e kill switch;
- custos e quotas;
- administracao e auditoria;
- lifecycle de extensoes.

Control Plane decide o que pode acontecer; nao executa diretamente tools de usuario.

## Execution Plane

Responsabilidades:

- sessoes e runtime;
- intent routing;
- planner/Agent Loop;
- Model Router;
- Tool/Skill/MCP Runtime;
- jobs, filas e cancelamento;
- streaming e eventos;
- verificacao de resultados.

Execution Plane obedece politicas e produz evidencias.

## Data Plane

Responsabilidades:

- PostgreSQL e dados transacionais;
- memoria e embeddings;
- conhecimento/documentos;
- object storage e backups;
- cache/Redis quando necessario;
- retencao, exportacao e exclusao;
- isolamento por tenant/usuario/projeto.

## Fluxo

```text
client
  -> Control: auth, policy, session
  -> Execution: route, plan, approve, execute
  -> Data: read/write scoped context and evidence
  -> Execution: verify and stream
  -> Control/Data: audit and quotas
```

## Separacao fisica

Os planos sao logicos. Em desenvolvimento podem rodar juntos. Em producao, edge, API e workers podem ser separados sem mudar contratos.

## Consistencia

- approval referencia plano e argumentos;
- eventos sao ordenados por request/job;
- mutacoes usam idempotency keys quando aplicavel;
- jobs possuem estado duravel;
- cache nunca e a unica fonte de verdade;
- falha de observabilidade nao autoriza efeito.

