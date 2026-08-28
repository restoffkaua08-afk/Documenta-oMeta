# Jarvis - Roadmap e Gates

> Este documento mantém os gates arquiteturais originais. O acompanhamento
> fechado das 24 etapas, critérios de aceite e percentual oficial está no
> [Plano mestre de conclusão](plano-mestre-conclusao.md).

## Fase J0 - Protecao e importacao

- criar repositorio/branch;
- registrar upstream, commit e licenca;
- importar baseline completa;
- congelar arvore antes das alteracoes.

## Fase J1 - Auditoria real

- inventariar Core, agentes, tools, skills, models, API, CLI, frontend e desktop;
- executar testes e builds existentes;
- classificar funcional, parcial, reservado e quebrado;
- registrar riscos e dependencias.

## Fase J2 - Baseline reproduzivel

- ambientes e lockfiles;
- CI;
- comandos de desenvolvimento;
- testes deterministas;
- artefato/container quando aplicavel.

## Fase J3 - Standalone product

- configuracao propria;
- API/CLI utilizaveis;
- interface aplicavel;
- model providers;
- streaming, eventos e cancelamento;
- tools/skills comprovadas.

## Fase J4 - Governanca

- policy engine;
- approvals;
- sandbox;
- auditoria;
- budgets e kill switch;
- prompt injection e supply chain.

## Fase J5 - Adapters e compatibilidade

- contratos versionados;
- adapter Rachel;
- adapter de estudo para Zane;
- testes de independencia;
- documentacao de consolidacao.

## Gate `JARVIS_STANDALONE_READY`

Exige:

- instalacao limpa documentada;
- baseline upstream rastreavel;
- agentes prioritarios executaveis;
- providers e fallback testados;
- tools e skills com seguranca;
- API/CLI e streaming;
- cancelamento;
- CI e regressao;
- auditoria e segredos;
- operacao sem Rachel/Zane.

## Gate `JARVIS_INTEGRATION_READY`

Adiciona:

- contratos estaveis;
- eventos versionados;
- adapters testados;
- matriz de capacidades;
- incompatibilidades registradas;
- ausencia de imports circulares;
- plano de consolidacao no Zane.

## Estado atual

O repositório e a baseline foram importados e registrados. As entregas
JARVIS-001 a JARVIS-014 estão implementadas na `main`. O plano mestre registra
4 de 24 etapas concluídas, 8 parciais e 12 pendentes em 28 de agosto de 2026.
Nenhum gate de release está verde: ainda faltam validação integral, competência
com modelos reais, segurança fim a fim e aceite no Windows de destino.
