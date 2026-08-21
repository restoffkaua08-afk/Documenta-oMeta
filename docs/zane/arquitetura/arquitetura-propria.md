# Zane - Arquitetura Propria

## Principio

Zane nao e um monorepo que simplesmente copia duas arvores e as executa lado a lado. Ele possui dominio, contratos, runtime, dados, politicas e interfaces proprios.

## Estrutura conceitual

```text
zane/
  apps/
    web/
    desktop/
    mobile/             futuro
  services/
    api/
    worker/
    edge/
  packages/
    domain/
    runtime-contracts/
    security/
    memory/
    tools/
    ui/
    api-client/
    config/
  infra/
  tests/
  docs/
```

A stack final deve ser confirmada no inicio da implementacao. As fontes sugerem Python/FastAPI no backend, TypeScript/React no web e Tauri no desktop.

## Dominios

- identidade e contexto;
- sessao e conversa;
- memoria e conhecimento;
- planejamento e agentes;
- tools e skills;
- seguranca e approvals;
- modelos e routing;
- jobs e eventos;
- dispositivos e sincronizacao;
- auditoria e observabilidade;
- extensoes ULTRON.

## Contratos antes de implementacoes

Contratos centrais: `RuntimeRequest`, `RuntimeResponse`, `RuntimeEvent`, `ToolSpec`, `ToolResult`, `Approval`, `MemoryRecord`, `KnowledgeResult`, `AgentProfile` e `ModelRequest/Response`.

## Regras de dependencia

- dominio nao depende da UI;
- tools nao acessam politicas por bypass;
- adapters externos dependem de portas internas;
- dados nao sao acessados diretamente pelo cliente;
- ULTRON fica atras de interface opcional;
- nenhum import obrigatorio aponta para repositorios Rachel/Jarvis.

## Modular monolith primeiro

Recomenda-se iniciar como monolito modular bem separado, evitando microservicos prematuros. Servicos podem ser extraidos quando escala, isolamento ou jobs justificarem.

## Criterio de arquitetura propria

Remover os checkouts de Rachel e Jarvis nao pode impedir build, testes ou inicializacao do Zane.

