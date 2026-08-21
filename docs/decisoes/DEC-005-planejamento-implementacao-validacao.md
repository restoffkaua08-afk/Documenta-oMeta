# DEC-005 - Planejamento, implementacao e validacao sao estados distintos

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Contexto

As fontes incluem planos detalhados, respostas de agentes, documentos de aceite, relatorios e referencias a commits. A linguagem de conclusao usada nesses materiais nao possui sempre o mesmo nivel de evidencia.

## Decisao

Toda afirmacao de progresso deve distinguir pelo menos:

1. especificado ou planejado;
2. implementado ou reportado como implementado;
3. validado contra um gate reproduzivel;
4. liberado para o ambiente definido.

O estado mais avancado somente pode ser usado quando suas evidencias estiverem registradas.

## Alternativas consideradas

- Confiar no documento mais recente: rejeitada porque um documento derivado pode repetir uma afirmacao nao verificada.
- Usar percentuais gerais de conclusao: rejeitada quando nao houver denominador e gates objetivos.

## Consequencias

- roadmaps nao comprovam execucao;
- documentos de aceite sao evidencias auxiliares, nao prova isolada;
- matrizes de gate devem apontar commits, testes, builds ou auditorias;
- contradicoes permanecem visiveis como `a confirmar` ate resolucao.

## Criterio de revisao

A classificacao pode ser refinada, mas nunca deve permitir que planejamento seja apresentado como funcionalidade existente.
