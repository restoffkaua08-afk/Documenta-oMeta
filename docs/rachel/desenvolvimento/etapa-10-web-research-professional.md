# Rachel — Etapa 10: Web Research Professional

**Estado:** Em implementacao incremental  
**Data:** 2026-08-27  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Evoluir `web.research` de uma busca simples com ranking por autoridade para um pipeline profissional de pesquisa que planeje consultas, consulte mais de uma variante quando necessario, prefira fontes primarias, nao finja atualidade quando a data de publicacao nao foi verificada, exponha divergencias e entregue evidencias auditaveis para a Dany.

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

Commits:

- `8099ed8c6f9d334ccc280d39319e0dbeda18367f` — `feat(research): adicionar planejamento profissional de consultas`;
- `abb4edb73683f0d590d586639b3a1ce42459b699` — `fix(research): reconhecer documentation como consulta tecnica`.

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

Os testes cobrem consulta simples, atual, tecnica, comparativa, deduplicacao, limite de variantes e rejeicao de consulta vazia.

### Multi-query integrado ao ResearchEngine

`RACHEL_PLATFORM/RUNTIME/SRC/research_runtime.py` foi atualizado.

Commits:

- `82bf797904cf0a33383006b58f3e5913af2b1d90` — `feat(research): integrar multi-query e gate de fontes primarias`;
- `62cbe79239ccfe80dddf3ffdeede38f30688f28a` — `test(research): validar multi-query fontes primarias e freshness`.

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

A CI inicialmente detectou uma regressao especifica: a palavra inglesa `documentation` nao ativava a classificacao tecnica. O planner foi corrigido no commit `abb4edb...`. A rodada seguinte do `RACHEL CI` concluiu Core, Runtime, frontend e Tauri com sucesso.

## Freshness real e evidencias estruturadas

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/research_evidence.py`.

Commit:

- `1ef30633a4b25f7c6fc1422b8661f695b063cb57` — `feat(research): adicionar evidencias de publicacao e conflitos`.

O modulo adiciona:

- `PublicationSignal`;
- `EvidenceClaim`;
- `ResearchConflict`;
- extracao conservadora de data a partir de URL e conteudo;
- validacao contra datas futuras em relacao ao momento de retrieval;
- verificacao de janela de freshness;
- construcao de unidades `claim -> source`;
- deteccao conservadora de conflitos em marcadores factuais como `version`, `release` e `limit`.

A extracao de data nao considera `retrieved_at_ms` como publicacao. O valor de publicacao precisa ser encontrado em um sinal independente da pagina/URL.

### Integracao ao ResearchEngine

Commit:

- `bbc623494ba6c1084b31b173554cfc10b02e0ec1` — `feat(research): validar freshness e estruturar evidencias`.

Cada fonte agora pode carregar:

- `published_at`;
- `publication_source`;
- `publication_confidence`;
- `freshness_verified`;
- citacao com data de publicacao quando disponivel.

O resultado de pesquisa passou a expor:

```text
research result
├── sources[]
├── evidence
│   ├── claims[]
│   ├── claim_count
│   ├── conflicts[]
│   └── conflict_count
├── search
└── quality
```

Se houver conflito detectado, o estado nao e apresentado como sucesso limpo: `completed_with_warnings`.

### Testes de freshness e conflito

Commit:

- `5032c84bce804e87dcb2e12db9f4ddc2c3e21081` — `test(research): validar freshness real e conflitos de evidencias`.

Os testes agora comprovam:

- ausencia de data nao gera freshness falsa;
- data recente real dentro da janela pode satisfazer o gate;
- `published_at` e preservado no resultado;
- claims estruturadas sao geradas;
- divergencias de versao entre fontes sao expostas como conflito;
- conflito transforma a pesquisa em `completed_with_warnings`.

## Dany integrada ao estado profissional da pesquisa

A Dany passou a receber informacoes adicionais do resultado de `web.research`.

Commits:

- `d7d7c5f661699ebf0c208595309dc2a2ceb67bd9` — `feat(dany): validar conflitos e freshness em pesquisas`;
- `7cd758773712360d0bd02c5aea100649f40430d0` — `feat(dany): propagar evidencias profissionais de pesquisa`;
- `85055ac6deac8574858ced9e5f4aa686988a9abf` — `test(dany): validar conflitos e freshness de pesquisa`.

`EvalContext` agora representa:

- `research_conflict_count`;
- `freshness_required`;
- `freshness_verified`.

Novos gates criticos:

- `research_conflicts_disclosed`;
- `freshness_consistent`.

Consequencias:

- se as fontes divergem, a resposta nao pode esconder o conflito;
- se a consulta exige atualidade e ela nao foi verificada, a resposta precisa declarar incerteza explicitamente;
- se freshness foi verificada por data de publicacao valida, nao e exigida uma ressalva artificial.

Isso mantem a Dany como avaliadora de consistencia/evidencia; ela nao finge provar verdade factual apenas lendo texto.

## Gate de fonte primaria

O `ResearchQualityEvaluator` registra:

- `authoritative_sources`;
- `primary_sources`;
- `has_required_primary_source`;
- `freshness_required`;
- `freshness_verified`.

Quando uma consulta profissional exige fonte primaria e nenhuma e recuperada, a pesquisa nao e tratada como sucesso limpo. O resultado continua disponivel como evidencia parcial, mas `quality.accepted` fica falso e o estado passa a `completed_with_warnings`.

## Estado da Etapa 09

O workflow `tests` do commit `385e2fddc897ff8072f7f676262fcb0ab43f8745`, que cobre o fluxo Visao -> CognitiveMemory -> KnowledgePort, concluiu com sucesso em 2026-08-26.

A Etapa 09 ainda nao deve ser marcada como totalmente encerrada ate remover a sobreposicao historica de `capabilities.knowledge = True` em `NedCognitiveBridge.status()` e confirmar a fonte unica de verdade do capability.

## Gate atual da Etapa 10

- query planning deterministico: **IMPLEMENTED / TESTED / CI GREEN**;
- multi-query: **IMPLEMENTED / TESTED / CI GREEN**;
- deduplicacao cross-query: **IMPLEMENTED / TESTED / CI GREEN**;
- gate de fonte primaria: **IMPLEMENTED / TESTED / CI GREEN**;
- freshness awareness: **IMPLEMENTED / TESTED / CI GREEN**;
- freshness verification por sinal de publicacao: **IMPLEMENTED / TESTED / CI PENDING NO HEAD ATUAL**;
- classificacao conservadora de conflito entre fontes: **IMPLEMENTED / TESTED / CI PENDING NO HEAD ATUAL**;
- estrutura claim -> evidence: **IMPLEMENTED / TESTED / CI PENDING NO HEAD ATUAL**;
- Dany exige disclosure de conflitos: **IMPLEMENTED / TESTED / CI PENDING NO HEAD ATUAL**;
- Dany governa freshness nao verificada: **IMPLEMENTED / TESTED / CI PENDING NO HEAD ATUAL**;
- sintese semantica multi-claim mais avancada: **PENDING**;
- CI final da etapa: **PENDING**.

## Proximos passos

1. confirmar CI do head `85055ac6...`;
2. se necessario, corrigir qualquer regressao sem enfraquecer os gates;
3. melhorar extracao de metadata de publicacao quando o `WebClient` puder preservar metadata HTML estruturada;
4. evoluir conflito de marcadores para contradicao semantica com evidencia rastreavel;
5. produzir sintese estruturada que associe afirmacoes finais a IDs de evidencia;
6. fechar a Etapa 10 somente apos CI completa verde e teste de caminho `web.research -> resposta -> Dany`.
