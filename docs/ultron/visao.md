# ULTRON - Visao Inicial

ULTRON e uma plataforma independente de capacidades. Ele organiza, valida, versiona e distribui agentes, skills, workflows e packs.

## Natureza

- amplificador, nao dependencia;
- produto independente;
- registry e protocolo de compatibilidade proprios;
- integracao opcional com Zane;
- possibilidade de visualizacao e exploracao de capacidades.

## Regra de falha

Se ULTRON estiver indisponivel, o Zane deve continuar operando com todas as capacidades nativas essenciais.

## Estado

Arquitetura e conceitos extensamente planejados nas fontes historicas. Implementacao real ainda precisa ser inventariada antes de qualquer declaracao de progresso.

## Produto-alvo

ULTRON deve permitir que desenvolvedores e sistemas:

- publiquem manifests versionados;
- descubram agentes, skills, workflows e packs;
- validem schemas, dependencias e compatibilidade;
- instalem sem ativar automaticamente;
- ativem capacidades dentro de politicas;
- visualizem relacoes e dependencias;
- acompanhem versoes, proveniencia e saude;
- removam ou revertam capacidades com seguranca.

## O que ULTRON nao e

- nao e o cerebro do Zane;
- nao e o runtime obrigatorio do Jarvis;
- nao concede permissao apenas por instalar um pack;
- nao e um repositório de prompts sem schema;
- nao e memoria pessoal global sem isolamento;
- nao deve executar codigo nao confiavel durante validacao.

Ver [`indice.md`](indice.md) para a especificacao completa.

