# DEC-001 - Meta possui quatro projetos durante a engenharia

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Contexto

As fontes historicas misturam produto final, projetos intermediarios e plataformas auxiliares. Sem fronteiras explicitas, Rachel, Jarvis, ULTRON e Zane podem ser interpretados incorretamente como modulos de um unico repositorio desde o inicio.

## Decisao

O Projeto Meta e o programa de engenharia composto por quatro projetos identificaveis:

- Rachel: agente pessoal standalone e governado;
- Jarvis: runtime standalone de agentes e ferramentas;
- ULTRON: plataforma opcional de componentes reutilizaveis;
- Zane: produto final independente criado por consolidacao e reengenharia.

Cada projeto deve possuir visao, arquitetura, roadmap, status, evidencias e ciclo de vida proprios.

## Alternativas consideradas

- Tratar Meta como um unico produto monolitico desde o inicio: rejeitada por apagar fronteiras e maturidades diferentes.
- Tratar Zane como quinto projeto alem do produto final: rejeitada; Zane ja e um dos quatro projetos e o destino do programa.

## Consequencias

- status e gates sao registrados por projeto;
- conclusao de um projeto nao conclui automaticamente o Meta;
- componentes compartilhados exigem contratos explicitos;
- o repositorio documental pode apresentar visao transversal sem eliminar a autonomia dos projetos.

## Criterio de revisao

Revisar apenas se o proprietario alterar formalmente a composicao do Meta ou incorporar um novo produto com ciclo de vida proprio.
