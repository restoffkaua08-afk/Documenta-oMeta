# Jarvis - Arquitetura em Camadas

## Objetivo

Organizar o fork como runtime reutilizavel, standalone e posteriormente estudado para consolidacao no Zane.

## Camada 0 - Configuracao e segredos

Carrega configuracoes, ambientes, feature flags, providers e referencias a segredos. Segredos nunca devem ser persistidos em YAML versionado.

## Camada 1 - Auditoria e telemetria

Fornece trace/correlation IDs, eventos, duracoes, usage, erros, tool calls, autorizacoes e redacao de dados sensiveis.

## Camada 2 - Model providers

Adapters para engines locais e cloud autorizadas. As fontes planejam Ollama, llama.cpp/vLLM e providers compativeis com APIs conhecidas.

## Camada 3 - Tools

Registry e executores tipados para filesystem, terminal governado, Git, browser, web, MCP, APIs e integracoes.

## Camada 4 - Skills

Capacidades compostas por prompts, tools e pipelines. Skills declaram dependencias, versao, entradas, saidas e politica.

## Camada 5 - Runtime interno

Sessao, roteamento, planejamento, execucao, cancelamento, eventos, budgets, verificacao, retry e persistencia operacional.

## Camada 6 - Agentes

Implementacoes especializadas e camada de perfis. Agentes usam runtime e tools; nao reimplementam seguranca, providers ou persistencia isoladamente.

## Camada 7 - Interfaces publicas

API, streaming, CLI, SDK e interfaces frontend/desktop. Contratos externos devem ser versionados.

## Estrutura-alvo conceitual

```text
src/
  agents/
  runtime/
  tools/
  skills/
  models/
  security/
  events/
  api/
  config/
tests/
  unit/
  integration/
  e2e/
docs/
frontend/
desktop/
```

A arvore real deve preservar a organizacao upstream quando ela for funcional. Esta estrutura e uma fronteira conceitual, nao autorizacao para mover tudo.

## Fluxo end-to-end

```text
request
  -> auth/session
  -> router/profile
  -> agent/planner
  -> policy
  -> tool/model execution
  -> verification
  -> events/stream
  -> response
```

## Runtime standalone

Jarvis deve possuir configuracao e capacidades minimas proprias. Integracoes futuras com Rachel ou Zane entram por adapters; nao por imports circulares no Core.

## Portabilidade

- paths relativos;
- manifests e lockfiles;
- Docker para ambiente reproduzivel quando aplicavel;
- execucao local documentada;
- deteccao clara de dependencias opcionais;
- nenhum caminho absoluto de maquina em registry operacional.

