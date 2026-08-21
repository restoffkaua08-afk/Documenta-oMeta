# Ciclo de Vida e Gates

## Estados padrao

| Estado | Definicao |
|---|---|
| Proposed | ideia registrada, ainda nao aprovada |
| Planned | escopo aprovado e ainda nao iniciado |
| In Progress | trabalho ativo com branch/owner identificado |
| Implemented | codigo existe, mas gates podem estar pendentes |
| Validated | evidencias exigidas passaram |
| Ready | capacidade liberada para o escopo declarado |
| Blocked | requisito conhecido impede avancar |
| Deferred | adiado conscientemente |
| Superseded | substituido por decisao posterior |

## Gates do programa

### Gate R - Rachel Standalone

Exige instalacao, inicializacao, conversa, contexto, seguranca, tools previstas, testes, build e documentacao sem dependencia do Jarvis.

### Gate J - Jarvis Standalone

Exige origem upstream preservada, agentes e tools auditados, runtime utilizavel, seguranca, cancelamento, providers, testes, interface aplicavel e documentacao sem dependencia da Rachel.

### Gate C - Consolidation Ready

Exige inventarios de Rachel e Jarvis, matriz de capacidades, contratos candidatos, decisoes de reutilizacao e lista de incompatibilidades.

### Gate U - ULTRON Independent

Exige registry, schemas, versionamento, validacao, instalacao segura e prova de que nenhum consumidor e obrigatorio.

### Gate Z - Zane Independent

Exige inicializacao sem predecessores, contratos proprios, capacidades essenciais, migracao controlada e testes de independencia.

### Gate P - Production Ready

Exige seguranca, observabilidade, backup, restauracao, performance, custos, deploy, rollback, incidentes, privacidade e E2E.

## Pacote minimo de evidencia

Cada gate deve registrar:

- escopo e versao;
- commit ou artefato congelado;
- ambiente de validacao;
- comandos/suites executadas;
- resultados e falhas conhecidas;
- blockers e riscos aceitos;
- responsavel pela aprovacao;
- data da decisao.

## Proibicoes

- nao usar percentual sem metodo;
- nao declarar concluido com base apenas em existencia de pasta;
- nao promover Blocked para Ready para fechar roadmap;
- nao reutilizar evidencia de commit antigo como se cobrisse codigo novo;
- nao confundir build com comportamento E2E;
- nao confundir documentacao de contrato com executor ativo.

