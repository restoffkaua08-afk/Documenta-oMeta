# ULTRON - Memoria Compartilhada, Namespaces e Grafo

## Principio

ULTRON pode definir uma camada de dados compartilhados entre capacidades, mas nao deve centralizar toda memoria pessoal do ecossistema.

## Namespaces

Cada publisher, pack, agent e consumer possui namespace. Acesso entre namespaces exige contrato e permissao.

Exemplo conceitual:

```text
consumer:zane/user:123/project:meta
pack:security-suite/agent:reviewer
publisher:restoff/workflow:release
```

## Tipos de dados

- configuracao da capacidade;
- estado operacional de workflow;
- artifacts compartilhados;
- metadados e lineage;
- indices de busca;
- relacoes do grafo.

Memoria pessoal e segredos permanecem sob governanca do consumidor.

## Grafo

Nos do grafo podem representar agents, skills, workflows, packs, tools, modelos, publishers e versoes. Arestas representam dependencia, composicao, compatibilidade, producao/consumo e substituicao.

## Visualizacao 3D

A visualizacao 3D e uma interface para explorar relacoes; nao deve ser a unica forma de consultar dados. Tabelas, busca e APIs precisam oferecer acesso preciso e acessivel.

## Retencao e exclusao

- estado temporario possui TTL;
- artifacts seguem politica do workflow;
- remocao respeita dependentes;
- dados pessoais seguem o consumer;
- lineage minimo pode ser preservado para auditoria sem conservar conteudo sensivel.

## Isolamento

Consultas e mutacoes devem aplicar tenant/consumer/namespace. Um pack nao le dados de outro apenas porque compartilha o registry.

