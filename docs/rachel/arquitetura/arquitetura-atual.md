# Rachel - Arquitetura Atual

**Classificacao:** consolidacao documental baseada nos relatorios, auditoria e roadmap fornecidos  
**Verificacao pendente:** confronto integral com a branch atual

## 1. Organizacao observada

A Rachel utiliza uma arquitetura Python para Core e Runtime, uma aplicacao Tauri para desktop e interfaces React/TypeScript. Os documentos registram ainda uma organizacao por orgaos/submodulos, com inventario legado de 23 fontes.

```text
RACHEL_CORE/
  dominio cognitivo, modelos e testes

RACHEL_PLATFORM/
  RUNTIME/
    SRC/
      cognitive_runtime.py
      bran_cognitive.py
      security_runtime.py
      tools_runtime.py
      intent_router.py
    TESTS/

APP/
  src/                 frontend desktop
  src-tauri/           host Rust/Tauri
  bridge/              bridges Python

interface/
  design-system/
  web/
  desktop/             referencia/placeholder; app real permanece em APP/
```

Essa arvore representa os paths mencionados nas fontes. A existencia atual de cada arquivo deve ser confirmada no repositorio da Rachel.

## 2. Camadas logicas

### Interface

Captura intencao, exibe streaming, planos, tools, estados, aprovacoes, erros e metricas. Nao deve executar efeitos diretamente.

### Bridge e transporte

Conecta clientes ao processo Python. O roadmap substitui inicializacao por mensagem por backend residente, com NDJSON no desktop e bridge HTTP/SSE para web.

### Runtime cognitivo

Classifica intencao, recupera contexto, escolhe fast path ou planner, coordena aprovacao, tools, sintese e resposta.

### Dominio/Core

Mantem contratos, modelos, comportamento cognitivo e interfaces internas sem depender da apresentacao.

### Servicos especializados

Memoria, conhecimento, seguranca, ferramentas, modelos, voz, avaliacao e observabilidade.

### Persistencia e configuracao

Armazena conversas, memoria, planos, aprovacoes, registries e configuracao local. Segredos devem permanecer fora de arquivos comuns.

## 3. Fluxo esperado

```text
usuario
  -> cliente web/desktop
  -> bridge residente
  -> IntentRouter
     -> conversa simples: fast path
     -> acao/pesquisa: planner
  -> Cyber/politica
  -> ToolCoordinator quando autorizado
  -> verificacao
  -> sintese
  -> streaming/eventos
  -> persistencia filtrada + auditoria
```

## 4. Contratos arquiteturais

### Portas internas

- `ModelProvider`: gera texto/stream e reporta capacidade.
- `MemoryPort`: grava e recupera memoria com escopo.
- `KnowledgePort`: busca documentos e evidencias.
- `ToolPort`: lista especificacoes e executa invocacoes validadas.
- `ApprovalPort`: cria, consulta e consome autorizacoes.
- `EventPort`: publica eventos sanitizados.
- `AgentRuntime`: coordena ciclos, mas nao se autoautoriza.

Os nomes exatos podem variar no codigo. O requisito e preservar as fronteiras.

## 5. Restricoes atuais conhecidas

- partes do Agent Runtime legado eram somente leitura;
- browser permanecia reservado/desativado;
- memoria dedicada tinha evidencia incompleta no fechamento legado;
- Training Runtime nao estava disponivel no host auditado;
- o modelo proprio nao possuia checkpoint;
- a auditoria identificou divergencia entre dois fluxos cognitivos;
- o desktop antigo iniciava sidecar por solicitacao antes da evolucao para runtime residente;
- a interface entregue paralelamente ainda exige validacao completa no ambiente alvo.

## 6. Direcao de evolucao

A arquitetura nao deve ser descartada. O novo ciclo profissional corrige conexoes, confiabilidade e experiencia em etapas: CI, cerebro unico, runtime residente, tools, Agent Loop, Model Router, inteligencia de projetos, avaliacao, conhecimento, web, browser, MCP, voz, desktop e hardening.

