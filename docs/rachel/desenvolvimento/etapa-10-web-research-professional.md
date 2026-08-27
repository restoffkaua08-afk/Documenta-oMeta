# Rachel — Etapa 10: Web Research Professional

**Estado:** Em implementacao incremental  
**Data:** 2026-08-27  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Evoluir `web.research` de uma busca simples com ranking por autoridade para um pipeline profissional de pesquisa que planeje consultas, consulte mais de uma variante quando necessario, prefira fontes primarias, nao finja atualidade quando a data de publicacao nao foi verificada e entregue evidencias auditaveis para a Dany.

## Estado anterior

Antes desta etapa, `ResearchEngine`:

- executava uma unica consulta;
- ordenava candidatos por `authority_score` e `score`;
- evitava repetir dominios;
- recuperava o conteudo real das paginas;
- produzia citacoes com URL, titulo, hash e horario de retrieval;
- aceitava uma pesquisa mesmo sem fonte primaria;
- nao diferenciava consulta simples, tecnica, comparativa ou sensivel a atualidade;
- nao possuia freshness verificavel.

O pipeline ja era governado e melhor que uma busca bruta, mas ainda podia confundir pagina recentemente recuperada com informacao realmente recente.

## Implementacao realizada

### Planejamento profissional de consultas

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/research_strategy.py`.

Commit:

- `8099ed8c6f9d334ccc280d39319e0dbeda18367f` — `feat(research): adicionar planejamento profissional de consultas`.

O modulo introduz `ResearchQueryPlan` e `plan_research_queries()`.

O planner deterministico identifica:

- consultas simples;
- consultas tecnicas;
- comparacoes;
- pedidos de verificacao;
- consultas sensiveis a atualidade.

Quando necessario, gera variantes de busca para:

- fonte oficial/primaria;
- documentacao oficial e release notes;
- atualizacao oficial recente;
- especificacoes oficiais para comparacao.

As variantes sao deduplicadas e limitadas defensivamente.

### Testes do planner

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_research_strategy.py`.

Commit:

- `00a29bcc529407ab34ee02535a812457b4d5593d` — `test(research): validar planejamento multi-query profissional`.

Os testes cobrem:

- consulta simples sem expansao desnecessaria;
- consulta atual exigindo fonte primaria e janela de freshness;
- consulta tecnica adicionando variante de documentacao oficial;
- comparacao adicionando especificacoes oficiais;
- deduplicacao e limite de variantes;
- rejeicao de consulta vazia.

### Multi-query integrado ao ResearchEngine

`RACHEL_PLATFORM/RUNTIME/SRC/research_runtime.py` foi atualizado.

Commit:

- `82bf797904cf0a33383006b58f3e5913af2b1d90` — `feat(research): integrar multi-query e gate de fontes primarias`.

A implementacao agora:

1. cria um `ResearchQueryPlan`;
2. executa cada variante planejada;
3. agrega providers e erros por consulta;
4. mescla candidatos por URL;
5. conserva o melhor candidato pela combinacao de autoridade e score;
6. mantem diversidade de dominios;
7. recupera o conteudo real das fontes;
8. registra qual variante encontrou cada fonte (`matched_query`);
9. exige fonte primaria quando o plano profissional determinar isso;
10. representa freshness separadamente de retrieval.

## Freshness sem falsa confirmacao

A etapa introduziu uma regra importante: `retrieved_at_ms` nao e tratado como data de publicacao.

Enquanto o runtime ainda nao extrair `published_at` de forma confiavel, uma consulta que exige atualidade retorna:

- `freshness_required = true`;
- `freshness_verified = false`;
- issue `FRESHNESS_VERIFIED`;
- estado `completed_with_warnings`.

Isso evita que a Rachel diga que uma informacao e atual apenas porque a pagina foi baixada agora.

## Gate de fonte primaria

O `ResearchQualityEvaluator` passou a registrar:

- `authoritative_sources`;
- `primary_sources`;
- `has_required_primary_source`;
- `freshness_required`;
- `freshness_verified`.

Quando uma consulta profissional exige fonte primaria e nenhuma e recuperada, a pesquisa nao e tratada como sucesso limpo. O resultado continua disponivel como evidencia parcial, mas `quality.accepted` fica falso e o estado passa a `completed_with_warnings`.

Isso se integra ao comportamento da Dany, que ja exige baixa confianca quando uma pesquisa nao possui fonte primaria.

### Testes de integracao do ResearchEngine

`RACHEL_PLATFORM/RUNTIME/TESTS/test_research_runtime.py` foi ampliado.

Commit:

- `62cbe79239ccfe80dddf3ffdeede38f30688f28a` — `test(research): validar multi-query fontes primarias e freshness`.

Os testes agora verificam:

- fonte primaria real como gate;
- multi-query realmente executado;
- metadata do plano de pesquisa;
- consulta atual nao fingir freshness;
- ausencia de fonte primaria resultar em warning, nao em sucesso falso.

A CI do commit `62cbe79239ccfe80dddf3ffdeede38f30688f28a` foi disparada e ainda deve ser considerada em andamento ate conclusao.

## Estado da Etapa 09

O workflow `tests` do commit `385e2fddc897ff8072f7f676262fcb0ab43f8745`, que cobre o fluxo Visao -> CognitiveMemory -> KnowledgePort, concluiu com sucesso em 2026-08-26.

A Etapa 09 ainda nao deve ser marcada como totalmente encerrada ate remover a sobreposicao historica de `capabilities.knowledge = True` em `NedCognitiveBridge.status()` e confirmar a fonte unica de verdade do capability.

## Gate atual da Etapa 10

- query planning deterministico: **IMPLEMENTED / TESTED**;
- multi-query: **IMPLEMENTED / TESTED / CI PENDING**;
- deduplicacao cross-query: **IMPLEMENTED / TESTED / CI PENDING**;
- gate de fonte primaria: **IMPLEMENTED / TESTED / CI PENDING**;
- freshness awareness: **IMPLEMENTED / TESTED / CI PENDING**;
- freshness verification por data de publicacao: **PENDING**;
- classificacao de conflito entre fontes: **PENDING**;
- sintese estruturada claim -> evidence: **PENDING**;
- CI final da etapa: **PENDING**.

## Proximos passos

1. confirmar CI do lote multi-query;
2. extrair `published_at` quando a pagina fornecer metadata confiavel;
3. adicionar avaliacao de freshness baseada em data real de publicacao;
4. detectar divergencias entre fontes e expo-las ao modelo;
5. criar estrutura claim/evidence para sintese auditavel;
6. integrar essas evidencias ao gate profissional da Dany;
7. somente marcar a Etapa 10 como validada apos CI completa verde.
