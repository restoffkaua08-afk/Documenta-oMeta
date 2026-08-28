# ULTRON - Registry e Componentes

## Visao

O registry e a fonte de metadados das capacidades publicadas. Ele nao e o executor principal das capacidades e nao substitui a politica do consumidor.

## Entidades

### Agent

Perfil executavel ou configuracao de agente, com capacidades, runtime, modelos, tools, limites e politica declarada.

### Skill

Capacidade reutilizavel composta por instrucoes, tools ou pipeline.

### Workflow

Grafo/ordem de passos, dependencias, condicoes, entradas, saidas e compensacoes.

### Pack

Unidade distribuivel que agrupa agents, skills, workflows, configuracoes e assets permitidos.

### Capability

Contrato logico versionado, independente de provider, com schemas de entrada e
saida, efeito, classe de dados, cache e politica de fallback.

### Provider

Implementacao substituivel de uma ou mais capabilities. Declara hosts, auth
abstrata, health, quota, custo, privacidade e compatibilidade; nunca secrets.

### OpenAPI definition e generated adapter

Recursos de discovery e geracao controlada. Specs e adapters nascem candidatos,
passam por seguranca/sandbox e sao publicados desabilitados. Detalhes em
[`external-capabilities-provider-registry.md`](external-capabilities-provider-registry.md).

## Componentes do sistema

```text
Registry API
  -> catalog/search
  -> manifests/versions
  -> compatibility

Validation Service
  -> schema
  -> dependency resolution
  -> policy/security
  -> sandbox inspection

Package Store
  -> immutable artifacts
  -> hashes/signatures

Install/Activation Service
  -> download
  -> verify
  -> install
  -> activate/deactivate
  -> rollback

Graph/Portal
  -> dependencies
  -> capabilities
  -> versions and health
```

## Separacao de responsabilidades

- registry descreve;
- package store conserva artefatos;
- validator verifica;
- consumer decide se instala;
- policy do consumer decide se ativa/executa;
- runtime do consumer executa.

## Busca e descoberta

Filtros minimos: tipo, nome, versao, capacidade, runtime, compatibilidade, publisher, licenca, risco e status de verificacao.

## Imutabilidade

Uma versao publicada nao deve ser alterada silenciosamente. Correcoes geram nova versao. Artefatos usam hash e proveniencia.

## Multi-consumidor

Zane, Jarvis, Rachel e ferramentas de desenvolvimento podem consumir ULTRON por adapters independentes. Nenhum consumidor e necessario para o ULTRON iniciar como plataforma.
