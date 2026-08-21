# DEC-010 - Separacao entre memoria e dataset

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Contexto

Conversas e fatos pessoais podem ajudar a experiencia, mas nao devem ser automaticamente tratados como dados autorizados para treinamento.

## Decisao

Memoria operacional e dataset de avaliacao/treinamento possuem pipelines, consentimentos, retencao e finalidades diferentes.

## Consequencias

- salvar uma memoria nao autoriza uso em treinamento;
- exportacao para dataset exige filtro, proveniencia e politica;
- exclusao de memoria deve respeitar retencao e sincronizacao;
- avaliacao nao pode vazar dados pessoais para providers sem autorizacao.

