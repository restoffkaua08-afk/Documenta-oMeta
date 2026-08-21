# Jarvis - Contratos, Rachel e Consolidacao no Zane

## Independencia primeiro

Jarvis deve ser concluido standalone antes de qualquer acoplamento definitivo. A integracao com Rachel serve para validar contratos e aprender, nao para criar dependencia mutua.

## Contratos minimos

### RuntimeRequest

- request/conversation ID;
- input e contexto autorizado;
- perfil/agente opcional;
- tools/skills permitidas;
- politica e budgets;
- preferencia de modelo;
- tracing e cancelamento.

### RuntimeResponse

- estado final;
- conteudo/stream;
- plano;
- tool calls propostas e executadas;
- evidencias;
- approvals pendentes;
- metricas;
- erros tipados.

### RuntimeEvent

Eventos versionados de sessao, modelo, planejamento, tool, approval, cancelamento e conclusao.

## Adapter Rachel

Traduz contratos cognitivos da Rachel para o runtime Jarvis e converte eventos/resultados de volta. Nao move regras de identidade/memoria pessoal para o Core Jarvis.

## Adapter Zane

Durante a construcao do Zane, adapters e matrizes ajudam a comparar comportamentos. O produto final nao deve depender desse adapter para iniciar Jarvis como servico externo permanente.

## Matriz de consolidacao

Para cada capacidade:

| Decisao | Significado |
|---|---|
| Absorver | incorporar conceito/contrato ao Zane |
| Reimplementar | criar implementacao propria corrigida |
| Adaptar | reutilizar componente isolado por interface |
| Descartar | nao levar ao produto final |
| Adiar | manter como extensao futura |

## Compatibilidade

Contratos usam versionamento semantico e negociacao de capacidades. Mudanca breaking exige nova versao e plano de migracao.

## Teste de independencia

- Jarvis roda sem Rachel;
- Rachel roda sem Jarvis;
- falha de um adapter nao corrompe o outro sistema;
- Zane final roda sem iniciar nenhum predecessor;
- dados podem ser migrados sem importar bancos internos inteiros.

