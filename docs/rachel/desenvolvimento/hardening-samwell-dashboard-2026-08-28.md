# Hardening — Samwell e Dashboard

Data: 2026-08-28
Estado: EM IMPLEMENTACAO / VALIDACAO INCREMENTAL

## Contexto

A regressao legada identificou timeout em `test_agent_bridge.py::AgentBridgeTests::test_dashboard_contains_agent_status`. A analise do caminho do dashboard mostrou que `APP/bridge/rachel_bridge.py::dashboard()` projeta `SamwellRuntime().status()`, enquanto `SamwellRuntime.status()` executa `audit()` completo. O audit percorre dependencias e pode disparar probes sincronos de comandos, ambientes Python e pacotes. Cada probe externo usa subprocesso com timeout proprio.

Esse acoplamento viola uma propriedade desejavel do dashboard: consulta de status deve ser leve, previsivel e nao deve implicitamente executar diagnostico profundo do host.

## Decisao arquitetural

Separar duas operacoes semanticamente diferentes:

1. **status leve de dashboard** — metadados do membro, catalogo, portabilidade e invariantes de seguranca, sem probes profundos;
2. **auditoria profunda explicita** — verificacao de executaveis, ambientes, pacotes e dependencias, mantida no `SamwellRuntime.audit()`/diagnostico dedicado.

O dashboard nao deve aumentar timeouts para esconder o problema. O objetivo e remover trabalho caro do caminho de leitura.

## Implementacao iniciada

Foi criado `RACHEL_PLATFORM/RUNTIME/SRC/samwell_dashboard.py` com `lightweight_status()`. O contrato:

- nao chama `SamwellRuntime.audit()`;
- nao instala, atualiza, remove ou repara dependencias;
- preserva `requires_cyber_for_mutation=true`;
- preserva `execution_enabled=false`;
- expoe `deep_audit_performed=false`;
- mantem a auditoria profunda disponivel separadamente.

Foi criado `RACHEL_PLATFORM/RUNTIME/TESTS/test_samwell_dashboard.py` com teste que substitui `audit()` por uma excecao e comprova que a projecao leve nao o chama, alem de validar os invariantes de seguranca.

Commits Rachel:

- `6681c7d714ec2db08fa7fed5680cbe49d2115d53` — `feat(samwell): separar status leve de auditoria profunda`
- `e41a153da0cb94e2bf7112cf3b9ff54010d8fa3c` — `test(samwell): garantir status leve sem probes profundos`

## Pendente antes de considerar resolvido

- integrar a projecao leve ao `dashboard()` do bridge;
- preservar uma rota explicita para diagnostico profundo;
- executar `test_samwell_dashboard.py`;
- executar `test_agent_bridge.py::AgentBridgeTests::test_dashboard_contains_agent_status` sem timeout;
- validar o workflow legado `tests`;
- validar `RACHEL CI` profissional;
- registrar tempos observados e evidencias finais.

## Criterio de aceite

Este hardening so sera `VALIDATED` quando o dashboard nao disparar auditoria profunda implicitamente, o teste historicamente afetado concluir dentro do limite normal e os workflows oficiais relevantes estiverem verdes.

## Regra de qualidade

Nao aumentar timeout como solucao primaria. Status, health check e dashboard devem permanecer baratos; diagnosticos profundos devem ser operacoes deliberadas e observaveis.
