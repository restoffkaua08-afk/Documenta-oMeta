# DEC-012 - Rachel Model como capacidade condicional

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Contexto

Fontes anteriores afirmavam tanto que Rachel nao teria pesos proprios quanto que existiria um `Rachel Model v0.1`.

## Decisao

Rachel nao depende de pesos proprios para funcionar. Um modelo especializado permanece como experimento futuro e condicional no final do roadmap.

Treinamento exige hardware adequado, dataset autorizado, baseline, avaliacao, thresholds, decisao de promocao e rollback. Se esses requisitos nao existirem, a capacidade permanece planejada sem bloquear a Rachel standalone.

## Consequencias

- providers substituiveis continuam obrigatorios;
- nenhum checkpoint pode ser anunciado antes de existir;
- treinamento nao implica promocao;
- avaliacao nao implica autorizacao de producao;
- ausencia de hardware preserva o item como roadmap.

