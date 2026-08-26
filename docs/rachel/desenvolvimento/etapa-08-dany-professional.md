# Rachel — Etapa 08: Dany profissional

**Estado:** Em implementacao incremental  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Substituir a validacao estrutural simples da Dany por um gate profissional que nao confunda texto bem-formado com resposta correta. A etapa deve validar consistencia com resultados de tools, obrigacoes de citacao em pesquisa, grounding em evidencia, alegacoes de sucesso, validacao de codigo e admissao explicita de incerteza quando factualidade nao foi comprovada.

## Pre-requisito

A Etapa 07 — Project Intelligence foi concluida no codigo e sua CI final do commit `6126f2134f009bad3f707989d35c0f58d230fc70` terminou verde. O gate de Project Intelligence passa a ser tratado como validado.

## Estado anterior

O `DanyEvaluator` existente em `RACHEL_PLATFORM/RUNTIME/SRC/cognitive_runtime.py` validava somente:

- resposta nao vazia;
- tamanho valido;
- ausencia de caractere nulo;
- conteudo nao composto apenas por whitespace.

Uma resposta generica e nao grounded podia receber score 100. Esse comportamento nao atende ao roadmap profissional.

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

A CI desse commit foi disparada e ainda nao deve ser tratada como evidencia final enquanto estiver pendente.

## Integracao restante

O novo avaliador e seu adapter ainda precisam substituir o `DanyEvaluator` estrutural nos caminhos canonicos:

1. chat nao-streaming do `NedCognitiveBridge`;
2. resposta grounded apos tool;
3. pesquisa `web.research`;
4. chat streaming do resident bridge;
5. CLI `evaluate`, preservando compatibilidade onde necessario.

A integracao deve manter o principio de que Dany avalia a resposta com o contexto real disponivel, nao com contexto inventado.

## Gate atual da Etapa 08

- avaliador profissional: **IMPLEMENTED / TESTED**;
- consistencia com falha de tool: **IMPLEMENTED / TESTED**;
- citacoes de pesquisa: **IMPLEMENTED / TESTED**;
- baixa confianca sem fonte primaria: **IMPLEMENTED / TESTED**;
- grounding em evidencia: **IMPLEMENTED / TESTED**;
- deteccao conservadora de alegacoes inventadas: **IMPLEMENTED / TESTED**;
- validacao de codigo baseada em checks reais: **IMPLEMENTED / TESTED**;
- adapter de contexto do runtime: **IMPLEMENTED / CI IN PROGRESS**;
- integracao no caminho canonico de chat/tool/stream: **PENDING**;
- CI final da etapa: **PENDING**;

## Proximos passos

1. confirmar CI do adapter de contexto;
2. integrar `DanyProfessional` ao caminho canonico de `NedCognitiveBridge`;
3. passar `tool_result` e metadata de pesquisa reais para o evaluator;
4. integrar o mesmo comportamento ao streaming;
5. adicionar testes de integracao do bridge;
6. somente marcar a Etapa 08 como validada com CI completa verde.
