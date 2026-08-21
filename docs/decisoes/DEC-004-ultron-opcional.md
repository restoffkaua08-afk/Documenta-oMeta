# DEC-004 - ULTRON e opcional e independente

**Estado:** ativa  
**Data de confirmacao:** 2026-08-21

## Contexto

ULTRON expande o ecossistema com agents, skills, workflows e packs. Entretanto, transformar sua disponibilidade em requisito do Zane criaria um ponto central de falha e confundiria plataforma de extensao com identidade do produto.

## Decisao

ULTRON sera desenvolvido como projeto independente e sera consumido pelo Zane somente como ampliacao opcional. O Zane deve iniciar e executar suas capacidades essenciais sem conexao, instalacao ou conta no ULTRON.

## Alternativas consideradas

- Incorporar o registry ao Zane: rejeitada por acoplar produto e plataforma de distribuicao.
- Exigir ULTRON para qualquer agente ou skill: rejeitada; componentes nativos e locais devem continuar disponiveis.

## Consequencias

- falhas de ULTRON produzem degradacao apenas das capacidades instaladas por ele;
- integracao usa protocolo versionado, validacao, assinatura e politica local;
- memoria privada e segredos nao sao enviados ao ULTRON por padrao;
- Zane conserva lista e estado dos componentes instalados necessarios para operacao offline permitida.

## Criterio de verificacao

Testes de independencia devem cobrir inicializacao do Zane sem ULTRON, indisponibilidade de rede, falha do registry e revogacao segura de um componente.
