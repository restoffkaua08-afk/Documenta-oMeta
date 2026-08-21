# Jarvis - Visao Inicial

Jarvis sera um runtime agentic standalone derivado do OpenJarvis. Ele deve ser capaz de funcionar, ser testado e demonstrado sem depender da Rachel ou do Zane.

## Responsabilidades esperadas

- agentes e orquestracao;
- planejamento e execucao;
- ferramentas e skills;
- MCP;
- pesquisa e trabalho com codigo;
- roteamento de modelos;
- cancelamento, auditoria e observabilidade;
- interfaces e adapters documentados.

## Regra de origem

A procedencia, a licenca e o commit upstream devem ser preservados. O projeto nao deve apagar a identidade tecnica do OpenJarvis nem copiar componentes sem rastreabilidade.

## Relacao com Zane

Jarvis nao sera um servico obrigatorio em producao. Suas capacidades comprovadas e seus aprendizados serao considerados durante a construcao da arquitetura unificada do Zane.

## Estado

Planejamento consolidado. O desenvolvimento detalhado deve iniciar somente depois do gate definido para a Rachel.

## Produto-alvo

Jarvis deve chegar ao final como um runtime agentic completo e demonstravel, capaz de:

- conversar e receber solicitacoes estruturadas;
- selecionar agentes e modelos;
- decompor objetivos em planos;
- executar tools e skills dentro de politicas;
- trabalhar com codigo, pesquisa e tarefas longas;
- emitir streaming e eventos;
- cancelar e retomar fluxos permitidos;
- produzir auditoria, metricas e evidencias;
- expor API, CLI e interface aplicavel;
- funcionar sem Rachel e sem Zane.

## O que Jarvis nao deve se tornar

- uma copia sem origem ou licenca;
- um conjunto de agentes renomeados sem contratos;
- um backend que depende da persona Rachel para iniciar;
- um executor que ignora autorizacao;
- o produto final do Meta;
- uma dependencia operacional permanente do Zane.

Ver [`indice.md`](indice.md) para a especificacao completa.

