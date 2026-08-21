# Zane - Consolidacao de Rachel e Jarvis

## Objetivo

Transformar aprendizados e capacidades comprovadas dos predecessores em uma arquitetura Zane unica, sem manter dois produtos obrigatorios em producao.

## Pre-condicoes

- `RACHEL_STANDALONE_READY`;
- `JARVIS_STANDALONE_READY`;
- inventarios de codigo e capacidades;
- contratos e testes identificados;
- licencas e proveniencia verificadas;
- divergencias registradas.

## Processo por capacidade

1. Identificar responsabilidade.
2. Localizar implementacoes nos predecessores.
3. Comparar comportamento, seguranca e qualidade.
4. Escolher absorver, reimplementar, adaptar, descartar ou adiar.
5. Criar contrato Zane.
6. Implementar sem import circular.
7. Migrar testes/evidencias relevantes.
8. Executar comparacao e regressao.
9. Registrar decisao e origem.

## Matriz inicial

| Dominio | Fonte primaria de aprendizado | Direcao Zane |
|---|---|---|
| Identidade/contexto | Rachel | reimplementar/absorver conceitos |
| Memoria/conhecimento | Rachel | contrato e storage proprios |
| Seguranca/approvals | Rachel | politica Zane propria |
| Agentes/planner | Jarvis + Rachel | runtime unificado |
| Tools/skills/MCP | Jarvis + Rachel | registry Zane proprio |
| Model Router | ambos | interface unica |
| Desktop/UX | Rachel + upstream Jarvis | design system/produto Zane |
| Avaliacao | Rachel/Dany + Jarvis | suite Zane |

## Migracao de dados

Migrar dados por exportadores versionados. Nao reutilizar bancos internos inteiros sem schema e politica. Memorias preservam proveniencia, consentimento e escopo.

## Migracao de identidade

A persona final e Zane. Elementos comportamentais uteis podem ser preservados, mas configuracoes e prompts antigos passam por revisao, nao concatenacao.

## Testes de independencia

- build sem repositorios predecessores;
- runtime sem processos Rachel/Jarvis;
- banco novo inicializa limpo;
- importacao de dados e opcional/reversivel;
- falha de adapter historico nao afeta Zane;
- capacidades essenciais possuem testes Zane nativos.

## Preservacao historica

Rachel e Jarvis permanecem em seus repositorios, com releases finais, tags, documentos e demonstracoes. Eles nao sao apagados nem fingidos como inexistentes.

