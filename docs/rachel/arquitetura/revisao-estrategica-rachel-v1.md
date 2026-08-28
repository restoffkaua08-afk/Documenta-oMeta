# Rachel — Revisao Estrategica da V1

**Estado:** consolidacao arquitetural atual  
**Data:** 2026-08-28  
**Escopo:** identidade do produto, arquitetura alvo, lacunas, melhorias e pontos fortes apos incorporacao do roadmap profissional e External Capabilities  
**Fontes principais:** `docs/rachel/visao.md`, `docs/rachel/arquitetura/*`, `docs/rachel/desenvolvimento/roadmap-definitivo-professional-agent-ready.md`, `docs/integracao/external-capabilities-public-apis.md`, `docs/integracao/contratos-entre-projetos.md`

---

## 1. O que a Rachel e hoje

Rachel e uma IA/agente pessoal local-first em processo de evolucao para um produto standalone profissional. Ela nao e apenas uma interface de chat e nao deve ser tratada como um wrapper de um provider de LLM.

A arquitetura atual combina:

- runtime cognitivo canonico;
- memoria e conhecimento separados;
- governanca Cyber;
- ferramentas tipadas e auditaveis;
- Agent Loop em consolidacao;
- Model Router provider-agnostic;
- Project Intelligence;
- Dany Professional;
- Web Research com evidencias;
- Browser Governado em implementacao;
- desktop Tauri/React;
- observabilidade e testes;
- evolucao planejada para External Capabilities, MCP, voz e release profissional.

A Rachel atual deve ser entendida como uma plataforma cognitiva pessoal governada, ainda incompleta como produto V1.

---

## 2. O que a Rachel deve se tornar quando completa

Ao final das 15 etapas, Rachel deve ser um agente pessoal standalone capaz de:

1. conversar naturalmente por chat e voz;
2. manter continuidade de sessao e contexto;
3. usar memoria pessoal com governanca;
4. recuperar conhecimento documental sem misturar memoria e RAG;
5. entender intencoes sem exigir nomes internos de tools;
6. planejar tarefas multi-etapa;
7. executar ferramentas reais somente dentro de escopo autorizado;
8. pedir approval para efeitos sensiveis e retomar exatamente a acao aprovada;
9. trabalhar com arquivos, Git, processos e validacao de projetos;
10. compreender repositorios grandes com contexto limitado;
11. pesquisar web com fontes, freshness, conflitos e claim-evidence;
12. navegar e interagir em paginas via browser governado;
13. selecionar modelos locais/cloud conforme tarefa e politica de privacidade;
14. consumir APIs e MCP por contratos de capability, sem acoplamento a providers;
15. usar clima, localizacao, documentos, OCR, research especializado, computacao e outras capabilities aprovadas;
16. degradar de forma segura quando rede/provider/tool falhar;
17. mostrar planos, ferramentas, approvals, erros e diagnosticos numa UX profissional;
18. instalar e iniciar de forma reproduzivel;
19. possuir recovery, security tests, performance benchmarks e E2E;
20. chegar ao selo `PROFESSIONAL_AGENT_READY` sem depender de Jarvis, ULTRON ou Zane.

---

## 3. Identidade arquitetural correta

A Rachel deve permanecer uma unica experiencia, mesmo com varios subsistemas internos.

### Rachel

- identidade;
- conversa;
- coordenacao;
- apresentacao da experiencia;
- contexto da sessao;
- escolha entre resposta direta e delegacao.

### Ned

- raciocinio;
- decomposicao de objetivos;
- planejamento;
- research;
- Project Intelligence;
- escolha de capability/tool abstrata.

### Cyber

- policy enforcement;
- classificacao de risco;
- privacy/data classification;
- approvals;
- escopos;
- secrets;
- bloqueio fail-closed.

### Arya

- ToolCoordinator;
- execucao de tools;
- browser;
- MCP;
- External Capabilities;
- verificacao de efeitos;
- processos pertencentes a Rachel.

### Dany

- avaliacao;
- grounding;
- consistencia com evidencia;
- factualidade dentro do que e verificavel;
- deteccao de falso sucesso;
- conflitos/freshness;
- qualidade do resultado.

Esses nomes representam responsabilidades. Eles nao devem virar sistemas cognitivos independentes competindo pela mesma tarefa.

---

## 4. Mudancas arquiteturais mais importantes introduzidas pelos ultimos planos

### 4.1 De chatbot com tools para plataforma de capacidades governadas

A principal evolucao recente e a adocao de `capability-first`.

Antes, a superficie de execucao podia crescer por tools/providers especificos.

Arquitetura alvo:

```text
intencao
  -> capability/tool contract
  -> Cyber
  -> ToolCoordinator
  -> Capability Runtime / MCP / local executor
  -> Provider Router
  -> provider concreto
```

A Rachel passa a depender de contratos internos estaveis e nao de APIs especificas.

### 4.2 Provider Registry + Router

Providers externos devem possuir:

- registry;
- health;
- rate limit;
- custo;
- privacy policy;
- fallback;
- circuit breaker;
- erro normalizado;
- Usage Ledger;
- secret_ref.

Isso reduz lock-in e evita espalhar clientes HTTP pelo runtime cognitivo.

### 4.3 Model Router expandido

O Model Router validado deve evoluir por providers configuraveis como Groq, Gemini e Hugging Face, sem substituir o local-first.

O ganho nao e simplesmente "ter mais modelos". O ganho real e:

- resiliencia;
- melhor escolha por tarefa;
- latencia;
- custo;
- multimodalidade;
- fallback controlado;
- politica de privacidade explicita.

### 4.4 RAG evolutivo

O Knowledge Port local continua valido. Jina/embeddings/reranking/pgvector sao expansoes do pipeline, nao substituicoes do contrato.

Arquitetura alvo:

```text
documento
 -> extracao/OCR governado
 -> chunks
 -> embedding provider
 -> vector backend opcional
 -> retrieval
 -> reranking
 -> KnowledgePort
 -> contexto do modelo
```

Memoria pessoal continua separada.

### 4.5 Research profissional evoluido

O research deixou de ser apenas busca e resumo. O desenho atual exige:

- planejamento de queries;
- fontes primarias;
- publication/freshness;
- conflitos;
- claim -> evidence;
- sintese auditavel;
- Dany como gate.

Jina Search/Reader, arXiv e CrossRef entram como providers desse pipeline, nao como nova autoridade cognitiva.

### 4.6 Browser governado

O browser evolui de leitura isolada para uma sessao persistente, governada e auditavel com:

- open/read/title;
- tab/session state;
- click;
- form;
- login;
- upload/download;
- target binding;
- approvals one-shot;
- SSRF policy.

Browser e Jina Reader permanecem complementares.

### 4.7 Voz como transporte, nao outro assistente

Voz deve usar a mesma session, memoria, Agent Loop, tools e approvals do chat.

STT/TTS sao providers substituiveis. Whisper local deve continuar primeira classe; Groq Whisper e outros sao opcionais.

### 4.8 External Capabilities

Rachel ganha uma camada planejada para clima, localizacao, rotas, OCR, computacao, research especializado e outras APIs sem poluir o Core.

Exemplos:

```text
weather.get_forecast
location.geocode
routes.calculate
documents.ocr
compute.query
research.search_papers
research.resolve_doi
```

---

## 5. O que ainda precisa mudar ou melhorar

### 5.1 Fechar uma unica linha cognitiva

As Etapas 2, 3 e 5 precisam de revalidacao formal para garantir que chat, Agent Loop, approvals e interfaces usem o mesmo runtime canonico.

Nao pode existir caminho alternativo que bypassa policies ou apresenta capability diferente do runtime real.

### 5.2 Resolver higiene de CI

O workflow legado `tests` possui timeout conhecido no `test_agent_bridge`. Antes do release:

- corrigir a causa;
- ou remover formalmente workflow obsoleto;
- nunca mascarar falha obrigatoria.

### 5.3 Fechar Knowledge Port

A Etapa 9 ja possui implementacao substancial, mas deve ser revalidada como gate unico e depois preparada para embeddings/rerank opcionais sem quebrar o baseline SQLite.

### 5.4 Completar Browser

A Etapa 11 ainda precisa:

- session/tab state;
- click real;
- form real;
- login;
- upload/download;
- smoke Playwright real;
- security tests de target mismatch/replay/SSRF.

### 5.5 Criar External Capability Runtime

A Etapa 12 e o maior bloco arquitetural novo. Precisa implementar a fundacao antes de qualquer proliferacao de APIs:

- Capability Registry;
- Provider Registry;
- Provider Router;
- Secret Manager adapter;
- governable HTTP client;
- error normalization;
- retry/circuit breaker;
- rate limits;
- health;
- ledger;
- privacy classification;
- schemas e contract tests.

Somente depois entram Open-Meteo, Nominatim, Jina e demais providers.

### 5.6 MCP no mesmo boundary de extensibilidade

MCP deve ser governado pelo mesmo Cyber/ToolCoordinator, nao ter autoridade especial.

### 5.7 Voz compartilhando sessao

O desafio principal nao e STT/TTS. E provar continuidade de contexto e approvals entre voz e chat.

### 5.8 Desktop operacional

O desktop precisa sair de "interface que conversa" para "console completo do agente":

- run/plan viewer;
- tool cards;
- approvals;
- diffs;
- browser state;
- provider/capability health;
- settings;
- doctor;
- recovery/errors.

### 5.9 Hardening/release

A Etapa 15 deve testar a soma do sistema:

- security;
- recovery;
- provider failure;
- privacy;
- SSRF;
- prompt/tool injection;
- approval replay/mismatch;
- secret leakage;
- malicious MCP schemas;
- installer;
- performance;
- E2E.

---

## 6. Pontos fortes atuais e acrescentados pelos ultimos planos

### 6.1 Local-first real

A Rachel nao depende estruturalmente de cloud. Providers cloud ampliam capacidade, mas nao definem identidade nem memoria.

### 6.2 Governanca forte

Cyber, approvals one-shot, tool schemas, scope validation e Dany criam defesa em profundidade.

### 6.3 Provider-agnostic

Model Router e External Capability Layer reduzem lock-in.

### 6.4 Modularidade sem fragmentacao

As responsabilidades internas sao especializadas, mas existe uma experiencia unica da Rachel.

### 6.5 Research auditavel

Claim-evidence, primary source, freshness e conflict detection colocam o research acima de uma busca web superficial.

### 6.6 Project Intelligence eficiente

Working set limitado e budget de contexto evitam carregar repositorios inteiros e tornam coding/research de projeto mais escalavel.

### 6.7 RAG com separacao semantica correta

Memoria pessoal e conhecimento documental nao sao tratados como a mesma coisa.

### 6.8 Extensibilidade controlada

MCP e External Capabilities entram por contratos governados, com possibilidade futura de distribuicao pelo ULTRON sem dependencia obrigatoria.

### 6.9 Evolucao para multimodalidade

Gemini/provider vision, OCR, documentos, voz e browser preparam Rachel para interacao alem de texto sem reescrever o Core.

### 6.10 Resiliencia e observabilidade

Provider health, circuit breakers, rate limits, Usage Ledger e normalized errors elevam a qualidade operacional prevista.

### 6.11 Privacidade como requisito de roteamento

Privacidade nao fica apenas em prompt. Ela influencia Model Router, Capability Router, uploads, localizacao, audio e documentos.

### 6.12 Independencia arquitetural

Rachel continua standalone. Jarvis, ULTRON e Zane possuem fronteiras claras e nao viram dependencias acidentais.

---

## 7. Pontos fortes que devem ser protegidos durante a implementacao

Nao degradar:

- local-first;
- deny-by-default;
- provider abstraction;
- separacao memoria/conhecimento/dataset;
- one-shot approvals;
- evidence-driven evaluation;
- bounded context;
- no false success;
- independente de ULTRON;
- independente de Jarvis;
- providers externos opcionais e substituiveis.

Se uma nova integracao exigir quebrar um desses principios, o design da integracao deve ser revisto.

---

## 8. Riscos principais apos a expansao

### Complexidade

A maior ameaca agora nao e falta de feature, e excesso de camadas mal integradas. Registries, routers, MCP, browser e providers precisam compartilhar contratos e observabilidade.

### Duplicacao de routers

Model Router, Intent Router e Provider Router possuem funcoes diferentes. Mistura-los criaria comportamento imprevisivel.

### Explosao de providers

Integrar muitas APIs antes da fundacao tornaria o sistema dificil de manter. O plano deve continuar com poucos providers de prova.

### Privacidade

Localizacao, audio, documentos e memoria sao dados de maior sensibilidade. Fallback para cloud precisa ser explicitamente governado.

### Supply chain

MCP, adapters e providers aumentam superficie de ataque. Nenhuma descoberta deve implicar ativacao automatica.

### Falso progresso

Adicionar adapters sem fechar gates nao deve aumentar o percentual formal do roadmap.

---

## 9. Avaliacao geral

A Rachel evoluiu de uma arquitetura de assistente modular para uma proposta de **agente pessoal governado, extensivel e provider-agnostic**.

O ganho mais importante dos planos recentes nao e a quantidade de APIs novas. E a mudanca estrutural que permite adicionar capacidades sem transformar o Core num conjunto de integracoes hardcoded.

A arquitetura final esperada possui quatro propriedades centrais:

```text
PERSONAL
  memoria/contexto/identidade

GOVERNED
  Cyber/approvals/policies/auditoria

AGENTIC
  planejamento/execucao/verificacao/recovery

EXTENSIBLE
  tools/browser/MCP/capabilities/providers
```

Essa combinacao diferencia a Rachel de um chatbot com plugins.

---

## 10. Condicao de sucesso

A Rachel nao deve ser considerada completa porque possui muitas capabilities. Ela deve ser considerada completa quando:

- as 15 etapas estiverem `VALIDATED`;
- as capabilities essenciais funcionarem pelo boundary correto;
- providers externos forem substituiveis;
- privacidade e approvals forem respeitados;
- memoria/conhecimento continuarem coerentes;
- falhas forem observaveis e recuperaveis;
- o desktop permitir operacao normal sem terminal;
- installer/release/E2E estiverem aprovados.

Somente entao a Rachel pode ser congelada como V1 standalone e servir como base conceitual/tecnica para a consolidacao futura no Zane.