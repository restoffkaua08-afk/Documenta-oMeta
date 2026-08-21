# Registro de Riscos do Projeto Meta

**Atualizado em:** 2026-08-21

| ID | Risco | Prob. | Impacto | Mitigacao principal | Estado |
|---|---|---|---|---|---|
| R-001 | tratar documento de aceite como prova de codigo | alta | alto | gates com commits e testes reproduzidos | aberto |
| R-002 | iniciar Etapa 5 da Rachel antes de fechar 2 a 4 | media | alto | auditoria consolidada obrigatoria | aberto |
| R-003 | acoplar Zane aos repositorios predecessores | media | critico | inventario, reengenharia e teste de independencia | aberto |
| R-004 | tornar ULTRON requisito de operacao | baixa | alto | degradacao opcional e testes offline | aberto |
| R-005 | vazamento de memoria ou segredos | media | critico | classificacao, vault seguro, minimo privilegio e auditoria | aberto |
| R-006 | lock-in de provider ou infraestrutura | media | alto | ports, adapters, exportacao e testes de substituicao | aberto |
| R-007 | usar memoria pessoal como dataset sem consentimento | baixa | critico | pipelines e consentimentos separados | aberto |
| R-008 | incompatibilidade de licenca do upstream Jarvis | media | alto | inventario de licencas antes da consolidacao | aberto |
| R-009 | documentacao divergir do codigo | alta | alto | atualizacao por PR ligada ao gate | aberto |
| R-010 | sincronizacao corromper ou duplicar memoria | media | alto | versionamento, idempotencia, conflitos e backup | planejado |
| R-011 | custo cloud crescer sem limite | media | alto | budgets, quotas, telemetria e fallback local | planejado |
| R-012 | agente executar acao privilegiada indevida | media | critico | policy engine, approval tokens, sandbox e kill switch | aberto |

## Politica de tratamento

Riscos criticos bloqueiam o gate quando nao existe mitigacao implementada e testada. Aceite de risco deve registrar responsavel, justificativa, validade e condicao de revisao.
