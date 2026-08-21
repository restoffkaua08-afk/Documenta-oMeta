# ULTRON - Schemas de Capacidades

## Regras comuns

Todo manifest deve declarar:

- schema/version;
- ID global e nome;
- versao semantica;
- publisher e proveniencia;
- descricao;
- licenca;
- compatibilidade;
- dependencias;
- riscos/permissoes;
- integridade do artefato;
- entradas/saidas quando aplicavel.

## Agent manifest

Campos conceituais:

```yaml
kind: agent
id: publisher.agent-name
version: 1.0.0
runtime: python
entrypoint: package.module:Agent
capabilities: []
models: []
tools: []
skills: []
budgets: {}
permissions: []
```

O entrypoint nao e executado durante validacao de schema.

## Skill manifest

Declara tipo (`prompt`, `tool`, `pipeline`, `agent`), inputs, outputs, tools requeridas, recursos, exemplos de teste e politica.

## Workflow manifest

Declara passos, dependencias, condicoes, retries, timeouts, idempotencia, compensacoes, artifacts e estados finais.

## Pack manifest

Declara conteudo, dependencias, conflitos, migrations permitidas, assets, hooks proibidos/permitidos e requisitos do consumidor.

## Validacao

1. parse seguro;
2. schema estrito;
3. IDs e semver;
4. integridade;
5. dependencias e ciclos;
6. compatibilidade;
7. licenca/proveniencia;
8. politica e permissoes;
9. inspecao de artefato;
10. testes em sandbox quando autorizados.

## Evolucao de schema

Schemas possuem versao independente das capacidades. Mudanca breaking exige migrador/documentacao e preservacao de leitura das versoes suportadas.

## Proibicoes

- campos desconhecidos tratados como permissao;
- scripts de instalacao executados durante parse;
- URLs mutaveis sem hash;
- dependencias sem faixa/versao;
- permissao implicita por ausencia de campo;
- manifest capaz de redefinir politica do consumidor.

