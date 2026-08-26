# Rachel — Etapa 08: Dany profissional

**Estado:** Em desenvolvimento incremental  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Substituir a avaliacao estrutural antiga, que podia retornar score 100 para qualquer resposta nao vazia, por uma camada de qualidade que diferencie estrutura, consistencia com execucao, grounding, citacoes, validacao de codigo e incerteza factual.

O roadmap exige especificamente:

- resposta consistente com `tool_result`;
- falha de tool nunca descrita como sucesso;
- pesquisa sem fonte primaria marcada como baixa confianca;
- citacoes obrigatorias quando a pesquisa possui fontes;
- verificacao de grounding em evidencias;
- deteccao de URLs, numeros e comandos obviamente nao sustentados pela evidencia;
- claims de build/test/lint apenas quando a validacao correspondente realmente ocorreu;
- capacidade de declarar que somente estrutura/evidencia foi validada, sem fingir factualidade.

## Primeiro lote implementado

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/dany_professional.py`.

A nova camada define:

- `EvalContext`;
- `QualityReport`;
- `DanyProfessional`.

Checks implementados:

- `not_empty`;
- `valid_size`;
- `no_null_character`;
- `request_fulfilled`;
- `tool_result_consistent`;
- `citations_present`;
- `grounded_in_evidence`;
- `no_obvious_hallucination`;
- `admits_uncertainty`;
- `code_validation_consistent`.

O avaliador nao declara factualidade por inferencia. O campo `scope` diferencia:

- `structural`;
- `structural-and-evidence-consistency`;
- `grounded`.

Commit:

- `f59c69f6a722bbeb42f1cd33d540c8a62f85aca3` — `feat(dany): adicionar avaliador profissional de grounding`.

## Testes adicionados

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_dany_professional.py`.

Os testes cobrem:

- resposta vazia rejeitada;
- score nao sendo automaticamente 100;
- `returncode != 0` incompatível com alegacao de sucesso;
- falha explicitamente admitida;
- pesquisa sem fonte primaria exigindo baixa confianca;
- pesquisa com fonte exigindo citacao retornada;
- URL inventada fora da evidencia sendo rejeitada;
- admissao explicita de validacao apenas estrutural;
- alegacao de testes/build validado somente quando check correspondente foi executado.

Commit:

- `3653a8a5e432cf2b27584044d0d9c7b21130c554` — `test(dany): validar grounding e consistencia profissional`.

## Estado atual

A nova Dany existe e possui testes isolados, mas ainda nao substituiu a `DanyEvaluator` antiga no caminho cognitivo. Isso e deliberado: primeiro o contrato novo deve passar na CI; depois a integracao sera feita em lote separado.

## Proximos passos

1. validar a nova suite na CI;
2. integrar `DanyProfessional` ao `cognitive_runtime.py`;
3. construir `EvalContext` a partir de chat/tool/research;
4. garantir que respostas de pesquisa sem fonte primaria recebam instrucao explicita de baixa confianca antes da sintese;
5. testar o caminho completo tool -> resposta -> Dany;
6. remover/aposentar a avaliacao antiga somente depois da regressao verde;
7. fechar o gate da Etapa 08.

## Gate atual

- nova Dany profissional: **IMPLEMENTED**;
- testes isolados: **IMPLEMENTED / CI PENDING**;
- tool failure consistency: **IMPLEMENTED / TEST PENDING CI**;
- research low-confidence rule: **IMPLEMENTED / TEST PENDING CI**;
- citation rule: **IMPLEMENTED / TEST PENDING CI**;
- obvious unsupported specifics: **IMPLEMENTED / TEST PENDING CI**;
- code validation consistency: **IMPLEMENTED / TEST PENDING CI**;
- integracao no cognitive runtime: **PENDING**;
- gate completo: **NOT YET CLOSED**.
