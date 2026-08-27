# Rachel — Etapa 10: Web Research Professional

**Estado:** VALIDATED  
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

O planner deterministico identifica consultas simples, tecnicas, comparativas, de verificacao e sensiveis a atualidade. Quando necessario, gera variantes para fonte oficial/primaria, documentacao oficial, release notes, atualizacao oficial recente e especificacoes oficiais.

### Multi-query integrado ao ResearchEngine

Commits:

- `82bf797904cf0a33383006b58f3e5913af2b1d90` — `feat(research): integrar multi-query e gate de fontes primarias`;
- `62cbe79239ccfe80dddf3ffdeede38f30688f28a` — `test(research): validar multi-query fontes primarias e freshness`.

O runtime agora planeja varias consultas quando necessario, agrega providers/erros, deduplica candidatos por URL, conserva o melhor candidato por autoridade/score, mantem diversidade de dominios e registra `matched_query`.

A CI inicialmente detectou uma regressao especifica: a palavra inglesa `documentation` nao ativava a classificacao tecnica. O planner foi corrigido no commit `abb4edb...`, e a rodada seguinte do `RACHEL CI` concluiu Core, Runtime, frontend e Tauri com sucesso.

## Freshness real e evidencias estruturadas

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/research_evidence.py`.

Commit inicial:

- `1ef30633a4b25f7c6fc1422b8661f695b063cb57` — `feat(research): adicionar evidencias de publicacao e conflitos`.

O modulo adiciona `PublicationSignal`, `EvidenceClaim`, `ResearchConflict`, extracao conservadora de data, verificacao de janela de freshness, construcao de unidades `claim -> source` e deteccao conservadora de conflitos factuais.

### Correcao do detector de conflitos

O `RACHEL CI` do commit `85055ac6deac8574858ced9e5f4aa686988a9abf` encontrou uma falha real no teste de conflito: frases naturais como `version is 3.13` nao eram reconhecidas porque o regex aceitava apenas separadores diretos como `:` ou `=`.

Commit de correcao:

- `176e676cf71930f2fefbff3451046a012f01b0d8` — `fix(research): detectar marcadores factuais em linguagem natural`.

O detector passou a aceitar conectores naturais conservadores como `is`, `was`, `é`, `era` e `foi`, mantendo a deteccao limitada a marcadores explicitamente comparaveis (`version`, `release`, `limit`).

Isso corrige a falha sem transformar o detector em um classificador semantico amplo ou propenso a falsos positivos.

## Integracao ao ResearchEngine

Commit:

- `bbc623494ba6c1084b31b173554cfc10b02e0ec1` — `feat(research): validar freshness e estruturar evidencias`.

Cada fonte pode carregar `published_at`, `publication_source`, `publication_confidence`, `freshness_verified` e citacao com data de publicacao quando disponivel.

O resultado passou a expor:

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

Se houver conflito detectado, o estado passa a `completed_with_warnings`.

## Contrato de sintese auditavel

Foi adicionado um contrato explicito de sintese ao resultado de pesquisa.

Commit:

- `e2824cb20d34c6533054f4ca5f317f5e88208743` — `feat(research): adicionar contrato estruturado de sintese`.

O resultado agora inclui:

```text
synthesis
├── mode = claim-evidence
├── citation_policy = near-claim
├── supported_claims[]
│   ├── claim_id
│   ├── text
│   ├── citation
│   ├── authority
│   └── published_at
├── conflicts[]
├── required_disclosures[]
├── must_not_invent_claims
├── must_not_hide_conflicts
└── must_not_fake_freshness
```

`required_disclosures` e derivado do estado real da pesquisa e pode exigir explicitamente:

- `source_conflicts`;
- `freshness_unverified`;
- `primary_source_missing`.

Isso fornece ao modelo um conjunto limitado de afirmacoes suportadas e torna mais rastreavel a associacao entre afirmacao final e evidencia.

## Dany integrada ao estado profissional da pesquisa

Commits:

- `d7d7c5f661699ebf0c208595309dc2a2ceb67bd9` — `feat(dany): validar conflitos e freshness em pesquisas`;
- `7cd758773712360d0bd02c5aea100649f40430d0` — `feat(dany): propagar evidencias profissionais de pesquisa`;
- `85055ac6deac8574858ced9e5f4aa686988a9abf` — `test(dany): validar conflitos e freshness de pesquisa`;
- `17f8eaa2ee61276a7e4c9b6367be2f8444dd737d` — `fix(dany): reconhecer disclosure natural de divergencia`.

`EvalContext` representa `research_conflict_count`, `freshness_required` e `freshness_verified`. Os gates criticos `research_conflicts_disclosed` e `freshness_consistent` impedem que a resposta esconda divergencias ou finja atualidade nao verificada.

A CI reforcada encontrou um caso real adicional: a resposta correta `As fontes divergem` ainda era rejeitada porque o vocabulario deterministico aceitava substantivos como `divergencia`, mas nao as formas verbais de `divergir`. A correcao `17f8eaa...` adicionou `diverge`, `divergem`, `divergiu` e `divergiram`, preservando todos os demais gates.

## Teste de caminho research -> resposta -> Dany

O teste de `ResearchEngine` foi ampliado no commit:

- `6dccd47f05b8058fd0080a72f3142a9759fb7172` — `test(research): validar sintese auditavel e gate Dany`.

O teste agora cobre o caminho integrado:

```text
ResearchEngine
   ↓
research result
   ↓
synthesis claim-evidence
   ↓
evaluate_runtime_response
   ↓
DanyProfessional
```

Casos comprovados pelo teste:

- resposta que omite conflito entre fontes e rejeitada;
- resposta que declara a divergencia e usa evidencias retornadas pode passar;
- resposta que afirma atualidade quando freshness nao foi verificada e rejeitada;
- resposta que admite explicitamente a incerteza temporal satisfaz o gate;
- `source_conflicts`, `freshness_unverified` e `primary_source_missing` aparecem no contrato quando aplicaveis.

## CI reforcada

O workflow foi atualizado no commit:

- `081fcb570ffcde863a791b70496983471808664a` — `ci(research): incluir gates profissionais recentes na regressao`.

Foram adicionados explicitamente a regressao critica:

- `test_research_strategy.py`;
- `test_dany_professional.py`.

`test_research_runtime.py` ja fazia parte da suite e tambem cobre o caminho integrado ate a Dany.

O run `33072136183` detectou a lacuna do disclosure natural. A correcao foi aplicada em `17f8eaa...`.

O run final `33072680019`, no head `17f8eaa2ee61276a7e4c9b6367be2f8444dd737d`, concluiu com:

- Python Core full unit suite: **PASS**;
- Critical Runtime regression suite: **PASS**;
- Desktop frontend build: **PASS**;
- Tauri Rust check: **PASS**.

## Gate de fonte primaria

O `ResearchQualityEvaluator` registra fontes autoritativas, fontes primarias, obrigatoriedade de fonte primaria, freshness requerida e freshness verificada. Quando uma consulta profissional exige fonte primaria e nenhuma e recuperada, `quality.accepted` fica falso e o resultado permanece disponivel como evidencia parcial em `completed_with_warnings`.

## Estado da Etapa 09

O workflow `tests` do commit `385e2fddc897ff8072f7f676262fcb0ab43f8745` concluiu com sucesso. A Etapa 09 ainda possui uma limpeza historica isolada: remover a sobreposicao `capabilities.knowledge = True` em `NedCognitiveBridge.status()` e confirmar o Core/ChatService como fonte unica de verdade do capability.

## Gate final da Etapa 10

- query planning deterministico: **VALIDATED**;
- multi-query: **VALIDATED**;
- deduplicacao cross-query: **VALIDATED**;
- gate de fonte primaria: **VALIDATED**;
- freshness awareness: **VALIDATED**;
- freshness verification por sinal de publicacao: **VALIDATED**;
- classificacao conservadora de conflito entre fontes: **VALIDATED**;
- estrutura claim -> evidence: **VALIDATED**;
- contrato de sintese estruturada: **VALIDATED**;
- Dany exige disclosure de conflitos: **VALIDATED**;
- Dany governa freshness nao verificada: **VALIDATED**;
- caminho `research -> resposta -> Dany`: **VALIDATED**;
- CI profissional ampliada: **VALIDATED**;
- CI final da etapa: **GREEN**.

## Observacoes para evolucao futura

A Etapa 10 esta encerrada para o escopo definido no roadmap. Melhorias posteriores podem incluir metadata HTML estruturada, contradicao semantica mais ampla e benchmarks adicionais de pesquisa profunda. Essas extensoes nao bloqueiam o gate atual e nao devem reabrir indefinidamente a etapa ja validada.

## Proximo passo

1. reconciliar a limpeza restante da Etapa 09;
2. iniciar a Etapa 11 — Browser governado — com auditoria da infraestrutura existente antes de adicionar novas dependencias;
3. manter leitura e efeitos do browser separados por politica Cyber desde o primeiro lote.
