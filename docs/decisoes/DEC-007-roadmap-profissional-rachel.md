# DEC-007 - Rachel usa um novo roadmap profissional de 15 etapas

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Contexto

A auditoria da Rachel identificou a necessidade de transformar a arquitetura existente em produto profissional, com CI, seguranca, runtime, memoria, interfaces, observabilidade, release e criterios verificaveis.

## Decisao

O ciclo vigente da Rachel e o roadmap profissional de quinze etapas mantido em `rachel/desenvolvimento/roadmap-profissional.md`. Sua numeracao e independente do ciclo legado.

Etapas devem ser executadas e aprovadas na ordem de dependencias. Trabalho paralelo pode existir, mas nao antecipa o fechamento do gate correspondente.

## Alternativas consideradas

- Continuar adicionando etapas ao ciclo legado: rejeitada por misturar objetivos arquiteturais e de producao.
- Declarar todo o novo ciclo concluido com base na interface: rejeitada porque interface e apenas uma parte do produto.

## Consequencias

- a Etapa 1 possui evidencia registrada;
- Etapas 2, 3 e 4 permanecem pendentes de auditoria consolidada;
- a entrega paralela da interface nao comprova sozinha a etapa de interface do roadmap;
- a Etapa 5 permanece bloqueada ate o fechamento dos gates anteriores.

## Criterio de conclusao

Cada etapa deve possuir escopo, criterios de aceite, testes, evidencias, riscos residuais e decisao explicita de gate.
