# Rachel — Etapa 08: Dany profissional

**Estado:** Em validacao final  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Substituir a validacao estrutural simples da Dany por um gate profissional que nao confunda texto bem-formado com resposta correta. A etapa valida consistencia com resultados de tools, obrigacoes de citacao em pesquisa, grounding em evidencia, alegacoes de sucesso, validacao de codigo e admissao explicita de incerteza quando factualidade nao foi comprovada.

## Pre-requisito

A Etapa 07 — Project Intelligence foi concluida no codigo e sua CI final do commit `6126f2134f009bad3f707989d35c0f58d230fc70` terminou verde. O gate de Project Intelligence passa a ser tratado como validado.

## Estado anterior

O `DanyEvaluator` existente em `RACHEL_PLATFORM/RUNTIME/SRC/cognitive_runtime.py` validava somente:

- resposta nao vazia;
- tamanho valido;
- ausencia de caractere nulo;
- conteudo nao composto apenas por whitespace.

Uma resposta generica e nao grounded podia receber score 100. Esse comportamento nao atendia ao roadmap profissional.

## Implementacao realizada

### Avaliador profissional

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/dany_professional.py`.

Commit:

- `f59c69f6a722bbeb42f1cd33d540c8a62f85aca3` — `feat(dany): adicionar avaliador profissional de grounding`.

A implementacao introduz:

- `EvalContext`;
- `QualityReport` com escopo explicito;
- `DanyProfessional`;
- consistencia com `tool_result` inclusive falhas aninhadas e `returncode != 0`;
- obrigacao de citacao em respostas de pesquisa;
- baixa confianca quando pesquisa nao possui fonte primaria;
- grounding minimo em evidencia concreta;
- deteccao conservadora de URLs, numeros e comandos inventados fora da evidencia;
- admissao de incerteza quando factualidade nao foi verificada;
- impedimento de alegar build/test/lint validado sem check real executado.

A Dany nao afirma provar verdade factual apenas pelo texto. Seu escopo pode ser `structural`, `structural-and-evidence-consistency` ou `grounded` conforme a evidencia disponivel.

### Testes do avaliador

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_dany_professional.py`.

Commit:

- `3653a8a5e432cf2b27584044d0d9c7b21130c554` — `test(dany): validar grounding e consistencia profissional`.

Os testes cobrem:

- resposta vazia rejeitada;
- score nao ser automaticamente 100 para resposta generica;
- tool com `returncode != 0` nao poder ser descrita como sucesso;
- falha declarada corretamente;
- pesquisa sem fonte primaria exigir baixa confianca;
- pesquisa com fontes exigir citacao retornada pelo runtime;
- URL inventada fora da evidencia ser rejeitada;
- validacao apenas estrutural ser admitida explicitamente;
- alegacao de testes/build validos depender de check realmente executado.

A workflow `tests` do commit `3653a8a5e432cf2b27584044d0d9c7b21130c554` concluiu com sucesso.

### Adapter de contexto do runtime

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/dany_runtime.py`.

Commit:

- `075908126780d62b3efb6f386a3cffebf491766d` — `feat(dany): adicionar adapter de contexto para respostas do runtime`.

O adapter converte resultados reais do runtime em `EvalContext` sem obrigar o avaliador a conhecer detalhes de cada tool. Ele:

- extrai URLs de `web.research` como citacoes permitidas;
- conta fontes primarias;
- detecta checks de codigo presentes no resultado;
- identifica quando uma solicitacao exige validacao de codigo;
- preserva `tool_result` como evidencia;
- produz payload de qualidade identificado como `dany-professional`.

### Testes do adapter

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_dany_runtime.py`.

Commit:

- `3e45b014a55881be16bf3cdff906b0855cf8465e` — `test(dany): validar adapter de contexto do runtime`.

Os testes verificam:

- extracao de citacoes e contagem de fontes primarias;
- baixa confianca em pesquisa sem fonte primaria;
- falha aninhada de comando nao ser aceita como sucesso;
- descoberta de `pytest`, `lint` e `build` em resultados reais;
- identificacao do validador profissional no payload.

### CLI profissional

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/dany_cli.py`.

Commits relacionados:

- `1e63a2d59f286c9f71fadb9d057413ce192cbaa6` — `feat(dany): adicionar CLI profissional de avaliacao`;
- `2ea1732019037363fa85b19598309f6f511d8621` — `feat(dany): rotear evaluate para CLI profissional`;
- `6f040a7d2be3ca203494196b4f31ccb527ee9657` — `test(dany): validar CLI profissional e roteamento`.

O comando `evaluate` do script `RACHEL_PLATFORM/SCRIPTS/rachel.ps1` deixou de compartilhar o entrypoint cognitivo antigo e passou a usar o CLI dedicado da Dany profissional.

### Integracao no runtime cognitivo

O `RACHEL_PLATFORM/RUNTIME/SRC/cognitive_runtime.py` foi atualizado para usar a Dany profissional como avaliador canonico.

Commit:

- `82496771f778181dd5fabd79f11e18503653fbc4` — `feat(dany): integrar avaliacao profissional ao runtime cognitivo`.

Mudancas principais:

- `DanyEvaluator` passa a ser alias de compatibilidade para `DanyProfessional`;
- chat normal usa `evaluate_runtime_response()`;
- payload de qualidade identifica `validator=dany-professional`;
- `quality_scope` deixa de ser fixo e passa a refletir o escopo real do report;
- respostas apos tools sao reavaliadas usando o `tool_result` real como evidencia;
- `web.research` fornece URLs reais como citacoes permitidas;
- pesquisa sem fonte primaria injeta instrucao explicita de baixa confianca;
- resposta grounded rejeitada pela Dany interrompe o caminho em vez de publicar sucesso inconsistente;
- status do Ned passa a declarar `quality_scope=professional-contextual`.

Teste de integracao:

- `22bf108efd847bbb3397ea66f5e2f55ca7073ac9` — `test(dany): validar integracao no bridge cognitivo`.

Esse teste confirma que o bridge usa `DanyProfessional`, que o payload identifica o validador profissional e que o status do runtime nao anuncia mais escopo apenas estrutural.

### Integracao no streaming desktop

O resident bridge em `APP/bridge/rachel_server.py` tambem foi conectado ao mesmo gate profissional para que chat streaming e chat nao-streaming nao tenham criterios de qualidade divergentes.

Commits:

- `2732daead167843018203bedf55a8158d05035ff` — `feat(dany): integrar qualidade profissional ao streaming`;
- `998bde69b409986fed1ef1be948316c3e529fb11` — `test(dany): validar qualidade profissional no streaming`.

O streaming agora:

- avalia a resposta completa ao final da geracao;
- retorna `validator=dany-professional`;
- publica `quality_scope` real;
- preserva update de qualidade no learning engine;
- rejeita resposta que falhe nos checks criticos;
- continua sem avaliar conteudo parcial quando a geracao e cancelada.

## Gate atual da Etapa 08

- avaliador profissional: **IMPLEMENTED / TESTED**;
- consistencia com falha de tool: **IMPLEMENTED / TESTED**;
- citacoes de pesquisa: **IMPLEMENTED / TESTED**;
- baixa confianca sem fonte primaria: **IMPLEMENTED / TESTED**;
- grounding em evidencia: **IMPLEMENTED / TESTED**;
- deteccao conservadora de alegacoes inventadas: **IMPLEMENTED / TESTED**;
- validacao de codigo baseada em checks reais: **IMPLEMENTED / TESTED**;
- adapter de contexto do runtime: **IMPLEMENTED / TESTED**;
- CLI `evaluate` profissional: **IMPLEMENTED / TESTED**;
- chat nao-streaming: **IMPLEMENTED / TESTED**;
- resposta grounded apos tool: **IMPLEMENTED / TESTED BY CONTRACT / FINAL CI PENDING**;
- pesquisa `web.research`: **IMPLEMENTED BY CONTEXT ADAPTER / FINAL CI PENDING**;
- chat streaming: **IMPLEMENTED / TESTED**;
- CI final da etapa: **IN PROGRESS**.

## Proximos passos

1. aguardar a `RACHEL CI` do commit `998bde69b409986fed1ef1be948316c3e529fb11`;
2. se verde, marcar a Etapa 08 como validada;
3. iniciar auditoria da Etapa 09 — Knowledge Port real;
4. nao criar infraestrutura externa: a Etapa 09 deve conectar o runtime de conhecimento existente ao Core por adapter interno e testavel.
