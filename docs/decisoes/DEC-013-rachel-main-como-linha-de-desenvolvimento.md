# DEC-013 — Rachel passa a ser desenvolvida diretamente na `main`

**Estado:** Ativa  
**Data:** 2026-08-25  
**Escopo:** `restoffkaua08-afk/rachel-ia`

## Contexto

A evolucao profissional da Rachel foi desenvolvida na branch `evolution/rachel-professional-agent`, aberta a partir de `main@69efdc5ce239b03098f02eb613e00cc02cd8c88d`.

A branch acumulou 69 commits sem divergencia da `main` (`ahead_by=69`, `behind_by=0`). O head `0d27273a62405b707fb2e37027d9ccd482fffa5d` possuia execucao de CI concluida com sucesso no PR #7, incluindo testes Python, build do frontend e verificacao Rust/Tauri.

O proprietario autorizou explicitamente que, a partir desta consolidacao, o desenvolvimento da Rachel ocorra diretamente na `main`, e que cada alteracao relevante seja documentada neste repositorio.

## Decisao

1. O head validado da branch profissional foi promovido por fast-forward para `main` em 2026-08-25.
2. O PR #7 foi encerrado preservando o historico e passou a constar como integrado.
3. Novas mudancas da Rachel serao aplicadas diretamente na `main` enquanto esta decisao permanecer ativa.
4. Cada mudanca funcional, arquitetural, de gate ou de status deve ser refletida em `Documenta-oMeta` na mesma sessao de desenvolvimento.
5. A ausencia de PR nao reduz o criterio de qualidade: testes, builds, evidencias e gates continuam obrigatorios.

## Alternativas consideradas

- manter a branch profissional indefinidamente;
- continuar exigindo PR para cada lote;
- criar uma nova branch de integracao.

Essas alternativas foram rejeitadas para o fluxo atual porque o proprietario definiu a `main` como linha unica de execucao da Rachel.

## Consequencias

### Positivas

- elimina divergencia entre a linha profissional e a branch principal;
- torna o estado real do repositorio imediatamente visivel na `main`;
- reduz risco de continuar evoluindo uma branch que nao representa o produto oficial.

### Riscos

- mudancas incorretas atingem a branch principal imediatamente;
- nao ha isolamento de PR como barreira adicional.

### Mitigacoes

- commits pequenos e rastreaveis;
- CI e suites de regressao preservados;
- nenhuma declaracao de conclusao sem evidencia;
- documentacao atualizada junto da implementacao;
- uso de commits anteriores como pontos de recuperacao.

## Evidencias

- base anterior: `69efdc5ce239b03098f02eb613e00cc02cd8c88d`;
- head profissional integrado: `0d27273a62405b707fb2e37027d9ccd482fffa5d`;
- PR historico: `#7 — RACHEL Professional Agent Evolution`;
- CI do head no PR #7: concluido com `success`;
- integracao: fast-forward, sem `force`.

## Relacoes

- complementa `DEC-005-planejamento-implementacao-validacao.md`;
- altera somente o processo operacional da Rachel e nao muda as fronteiras definidas em `DEC-002` e `DEC-003`;
- substitui, para a Rachel, a orientacao generica de publicar sempre por branch e PR descrita em `meta/governanca-documental.md`, enquanto esta decisao estiver ativa.
