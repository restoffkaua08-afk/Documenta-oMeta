# Rachel — Roadmap Definitivo para `Professional Agent Ready`

**Versao:** 1.0  
**Data:** 2026-08-27  
**Repositorio de codigo:** `restoffkaua08-afk/rachel-ia`  
**Repositorio de documentacao:** `restoffkaua08-afk/Documenta-oMeta`  
**Branch oficial de desenvolvimento:** `main`  
**Quantidade fixa de etapas:** **15**

---

## 1. Objetivo deste documento

Este documento passa a ser a referencia principal para responder uma pergunta simples:

> **Quanto falta para a Rachel estar realmente pronta?**

O projeto sera considerado concluido somente quando **todas as 15 etapas abaixo estiverem em estado `VALIDATED` e o Gate Final de Release estiver integralmente verde**.

Nao sera suficiente existir codigo, pastas, interfaces bonitas ou testes isolados. `VALIDATED` significa que a capacidade existe, esta ligada ao caminho real da Rachel, possui regressao automatizada, respeita seguranca e foi comprovada no comportamento que promete.

Este roadmap e finito. Ele nao deve crescer indefinidamente toda vez que surgir uma ideia nova. Melhorias que nao sejam necessarias para cumprir o contrato de produto V1 devem ir para um backlog posterior, sem impedir a conclusao da Rachel V1.

---

## 2. Definicao oficial de "Rachel completa"

A Rachel V1 recebe o selo:

```text
PROFESSIONAL_AGENT_READY = TRUE
```

somente se conseguir, como um produto unico e standalone:

- iniciar de forma reproduzivel;
- conversar por chat de forma persistente e streaming;
- selecionar modelos sem acoplamento a um unico provider;
- compreender intencoes sem exigir comandos artificiais do usuario;
- planejar tarefas multi-etapa;
- executar ferramentas reais com governanca Cyber;
- pausar para aprovacao quando necessario;
- retomar exatamente a operacao aprovada, sem self-approval;
- manipular arquivos em escopos autorizados;
- trabalhar com Git;
- detectar, testar, compilar, verificar tipos e lintar projetos quando suportado;
- compreender repositorios grandes usando contexto limitado;
- pesquisar web com fontes, freshness e conflitos explicitados;
- usar conhecimento documental sem misturar memoria pessoal com RAG;
- usar navegador governado;
- integrar ferramentas MCP de forma segura;
- usar voz pela mesma sessao cognitiva do chat;
- possuir desktop profissional com estados de plano/tool/approval/erro;
- recuperar-se de falhas previsiveis sem inventar sucesso;
- possuir logs, eventos, metricas e diagnostico;
- possuir instalacao/release reproduzivel;
- passar os gates de seguranca, performance, regressao e E2E da Etapa 15.

A Rachel nao precisa de ZANE, Jarvis ou ULTRON para cumprir este contrato. Ela deve chegar completa como projeto independente antes de ser congelada como predecessora do ZANE.

---

## 3. Estados permitidos no tracker

| Estado | Significado |
|---|---|
| `VALIDATED` | Implementado, integrado, testado e gate comprovado. |
| `IMPLEMENTED_REVALIDATION_REQUIRED` | Codigo principal existe, mas falta fechar ou reconciliar o gate formal atual. |
| `IN_PROGRESS` | Implementacao ativa; ainda existem itens obrigatorios do gate. |
| `PARTIAL` | Existem pecas antecipadas, mas a etapa como um todo ainda nao foi executada. |
| `PLANNED` | Especificada, ainda nao concluida no codigo. |
| `BLOCKED` | Nao pode avancar por dependencia ou regressao aberta. |

Uma etapa so muda para `VALIDATED` mediante evidencia verificavel.

---

## 4. Snapshot atual — 2026-08-27

A leitura combinada do repositorio, dos documentos de desenvolvimento e das CIs atuais produz o seguinte estado:

| # | Etapa | Estado atual | Observacao principal |
|---:|---|---|---|
| 1 | CI profissional | `VALIDATED` com higiene pendente | `RACHEL CI` profissional existe e vem sendo usada. O workflow legado `tests` ainda apresenta timeout intermitente/atual em `test_agent_bridge`, portanto o Gate Final continua exigindo que toda CI oficial fique verde ou que workflows obsoletos sejam formalmente removidos. |
| 2 | Cerebro unico + Intent Router | `IMPLEMENTED_REVALIDATION_REQUIRED` | Caminho cognitivo atual ja esta muito alem do estado original, mas falta reconciliar o gate formal e eliminar divergencias documentais antigas. |
| 3 | Runtime persistente + streaming | `IMPLEMENTED_REVALIDATION_REQUIRED` | Backend residente, streaming/cancelamento e contratos existem; precisa de revalidacao final consolidada. |
| 4 | Tool Runtime profissional | `VALIDATED` | Filesystem, Git, dev/process e approvals governados possuem evidencia historica e regressao. |
| 5 | Agent Loop real | `IMPLEMENTED_REVALIDATION_REQUIRED` | `AgentLoopRuntime` esta ativo, persistente, governado e usado em etapas posteriores; falta documento/gate final dedicado. |
| 6 | Model Router | `VALIDATED` | Router, privacidade, fallback e CI automatizada validados. Smoke com providers reais fica no gate de ambiente/release. |
| 7 | Project Intelligence | `VALIDATED` | Working set, symbols, contexto <= 8000 tokens, <= 19 arquivos e integracao ao Agent Loop validados. |
| 8 | Dany Professional | `VALIDATED` | A implementacao foi utilizada e revalidada por etapas posteriores de research, com gates de grounding/freshness/conflito. |
| 9 | Knowledge Port real | `IMPLEMENTED_REVALIDATION_REQUIRED` | Adapter SQLite, bootstrap, isolamento RAG/memoria e testes existem. A antiga sobreposicao de `knowledge=True` foi removida; precisa fechar a CI/gate documental final. |
| 10 | Web Research Professional | `VALIDATED` | Multi-query, fontes primarias, freshness, claim-evidence, conflitos e Dany validados. |
| 11 | Browser governado | `IN_PROGRESS` | Read-only, registry, ToolCoordinator, Cyber e roteamento natural avancaram; session/tab state e efeitos interativos continuam obrigatorios. |
| 12 | MCP Runtime | `PLANNED` | Ainda precisa registry MCP real, discovery, normalizacao, Cyber e uso pelo agente. |
| 13 | Voz integrada ao Agent Loop | `PARTIAL` | Existem runtimes/testes de voz, mas falta provar que voz usa exatamente a mesma sessao/Agent Loop do chat. |
| 14 | Desktop UX profissional | `PARTIAL` | Frontend/Tauri ja existem e buildam, mas ainda falta fechar a UX operacional completa do agente. |
| 15 | Hardening + Release | `PLANNED/PARTIAL` | Existem pecas de seguranca, dataset/model contract e CI, mas o gate final de produto ainda nao foi executado. |

### Leitura honesta do progresso

Pela metrica **estritamente formal**, ha **6 de 15 etapas atualmente marcaveis como `VALIDATED`** neste snapshot: 1, 4, 6, 7, 8 e 10. Isso equivale a **40% dos gates formalmente fechados**.

Esse numero subestima a maturidade real porque as etapas 2, 3, 5 e 9 ja possuem implementacoes substanciais e a 11 esta em execucao. Considerando codigo ja existente, integracoes realizadas e pendencias de gate, a maturidade de engenharia esta aproximadamente na faixa de **60–65%**, mas isso **nao e um percentual de release**.

Portanto, neste ponto a Rachel esta **bem avancada, mas ainda nao pode ser chamada de quase pronta para V1**. O trecho restante inclui quatro etapas relevantes (12–15), conclusao do browser e reconciliacao de gates anteriores.

---

# 5. As 15 etapas definitivas

## ETAPA 1 — CI profissional e rede de seguranca

### Objetivo

Garantir que toda alteracao relevante em `main` seja automaticamente compilada/testada e que regressao nao seja confundida com entrega concluida.

### Obrigatorio

- Core test suite;
- Runtime critical regressions;
- JSON/config validation;
- frontend production build;
- Tauri `cargo check`;
- falha real deve resultar em workflow vermelho;
- workflows oficiais sem falsos verdes por `|| true` em testes obrigatorios;
- nenhum workflow oficial permanentemente vermelho no release.

### Gate de conclusao

`VALIDATED` quando a rede principal de CI funciona e, no Gate 15, todas as workflows oficiais relevantes da `main` estao verdes ou obsoletas foram removidas/documentadas.

---

## ETAPA 2 — Cerebro unico + roteamento de intencao

### Objetivo

Existir um unico caminho cognitivo canonico para chat e acoes, sem dois cerebros divergentes nem prompts que anunciem capacidades falsas.

### Obrigatorio

- entrypoint cognitivo canonico;
- chat comum usa fast path sem planner pesado desnecessario;
- intents claras sao resolvidas deterministicamente quando possivel;
- fallback para modelo somente quando a heuristica e insuficiente;
- planner nunca inventa nome de tool;
- tool arguments validados;
- approvals retomam plano exato e nao replanejam operacao aprovada;
- status/capabilities refletem implementacao real.

### Gate

E2E: conversa simples, research, memoria, filesystem, projeto e browser devem chegar ao dominio correto sem o usuario conhecer nomes internos de membros/tools.

---

## ETAPA 3 — Runtime persistente, streaming e cancelamento

### Objetivo

Transformar Rachel em processo residente responsivo, e nao um sidecar reiniciado a cada mensagem.

### Obrigatorio

- runtime residente;
- protocolo IPC estavel;
- streaming real;
- cancelamento cooperativo;
- resposta parcial cancelada nao pode virar mensagem concluida;
- continuidade de conversation/session;
- health/readiness;
- graceful shutdown;
- metricas de TTFT, total, model e tool separadas.

### Gate

Teste E2E deve manter uma sessao, enviar varias mensagens, cancelar uma geracao, continuar a conversa e encerrar/reiniciar sem corromper estado.

---

## ETAPA 4 — Tool Runtime profissional

### Objetivo

Permitir que Rachel execute trabalho real de forma tipada e auditavel.

### Obrigatorio

- filesystem escopado;
- read/write/patch/copy/move/delete governados;
- Git tipado sem shell generico como caminho principal;
- dev detect/test/build/lint/typecheck;
- ownership de processos iniciados pela Rachel;
- approvals one-shot;
- verificacao pos-acao;
- rollback/backup quando aplicavel;
- fallback generico reduzido e endurecido.

### Gate

Rachel deve modificar um projeto autorizado, mostrar diff, executar validacao e nunca anunciar sucesso quando o resultado real falhou.

---

## ETAPA 5 — Agent Loop real

### Objetivo

Ligar Ned + executor + Cyber em um loop multi-etapa persistente e governado.

### Obrigatorio

- estados de run persistentes;
- budgets de iteracao/tool/time/falhas;
- plan -> execute -> observe -> verify;
- dependencies entre steps;
- pause/waiting approval;
- resume exato;
- cancelamento;
- retry limitado;
- checkpoint;
- sem self-approval;
- verificacao de resultados;
- falha terminal controlada.

### Gate

Um objetivo multi-etapa deve atravessar planejamento, pelo menos duas tools, verificacao, uma pausa de approval em cenario sensivel e retomada sem replanejamento indevido.

---

## ETAPA 6 — Model Router

### Objetivo

Separar inteligencia do provider de inferencia.

### Obrigatorio

- perfis fast/general/reasoning/coding/vision;
- local-only/hybrid/cloud-enabled;
- protecao de conteudo sensivel;
- fallback;
- circuit/failure handling basico;
- health por provider;
- streaming sem duplicacao em fallback;
- provider opcional por configuracao.

### Gate

CI automatizada valida contratos; Etapa 15 executa smoke em provider real do ambiente-alvo.

---

## ETAPA 7 — Project Intelligence

### Objetivo

Trabalhar em repositorios grandes sem despejar o repositorio inteiro no contexto.

### Obrigatorio

- project discovery;
- repo map;
- dependency map;
- symbol index;
- search;
- working set;
- project instructions;
- project memory;
- contexto real <= 8.000 tokens estimados;
- <= 19 arquivos;
- Agent Loop usando o boundary limitado.

### Gate

Projeto sintetico >= 500 arquivos continua produzindo working set pequeno e relevante.

---

## ETAPA 8 — Dany Professional

### Objetivo

Impedir que qualidade seja confundida com texto bem-formado.

### Obrigatorio

- consistencia com tool result;
- falha de comando nao pode virar sucesso;
- citacoes obrigatorias quando pesquisa exigir;
- grounding em evidencias;
- controle de alegacoes de numeros/URLs/comandos;
- disclosure de incerteza;
- checks de codigo baseados em execucao real;
- conflitos/freshness quando aplicavel;
- mesmo gate em streaming e nao-streaming.

### Gate

Fixtures negativas devem provar que Dany bloqueia resposta aparentemente boa, mas incompatível com a evidencia.

---

## ETAPA 9 — Knowledge Port real

### Objetivo

Conectar conhecimento documental governado ao Core sem transformar memoria pessoal em RAG.

### Obrigatorio

- adapter real;
- somente `document_chunk` como conhecimento recuperavel;
- isolamento de memoria pessoal;
- evidence injection no prompt;
- capability derivada do backend real;
- DB ausente tratado de forma segura;
- fluxo Visao -> Bran/Storage -> KnowledgePort -> ChatService;
- configuracao documentada.

### Gate

Documento indexado precisa aparecer como evidencia em consulta relevante; memoria pessoal com as mesmas palavras nao pode vazar como documento.

---

## ETAPA 10 — Web Research Professional

### Objetivo

Pesquisa auditavel, recente quando solicitado e explicitamente baseada em evidencias.

### Obrigatorio

- query planning;
- multi-query;
- primary source preference/gate;
- deduplicacao;
- authority ranking;
- publication signal;
- freshness verification;
- claim -> evidence;
- conflict detection;
- synthesis contract;
- Dany disclosure gates.

### Gate

Pesquisa atual e pesquisa conflitante devem produzir comportamento diferente de uma busca simples e nao podem inventar freshness.

---

## ETAPA 11 — Browser governado

### Objetivo

Permitir navegacao e interacao web reais sem liberar um navegador irrestrito ao agente.

### Obrigatorio

- Playwright/backend reproduzivel;
- SSRF policy em URL inicial, redirects e requests/subrequests;
- `browser.open/title/read`;
- session/tab state persistente;
- alvo conhecido para acao posterior;
- `click` governado;
- `form` governado;
- `login` governado;
- upload/download governados;
- approval ligado a tool + target + argumentos;
- approval nao reutilizavel para outra acao;
- efeitos externos nunca executados silenciosamente;
- roteamento natural;
- smoke real Playwright.

### Gate

E2E deve abrir uma pagina real de teste, ler titulo/conteudo, solicitar approval para efeito, executar somente apos approval e provar que approval diferente/reutilizado e rejeitado.

---

## ETAPA 12 — MCP Runtime

### Objetivo

Dar extensibilidade real e padronizada sem acoplar cada integracao diretamente ao Core.

### Obrigatorio

- config/registry de servidores;
- lifecycle connect/disconnect;
- discovery de tools;
- schema normalization;
- namespace/IDs estaveis;
- capabilities;
- health;
- timeout;
- erros controlados;
- Cyber effect mapping;
- approvals;
- allowlist/trust policy;
- secrets fora de prompts/logs;
- Agent Loop podendo escolher tool MCP;
- servidor fake para CI.

### Gate

Cadastrar servidor MCP fake, descobrir uma tool, executa-la atraves do ToolCoordinator/Agent Loop e provar que uma tool de efeito sensivel e bloqueada sem approval.

---

## ETAPA 13 — Voz integrada ao mesmo Agent Loop

### Objetivo

Voz nao pode ser outro assistente. Deve ser apenas outro transporte para a mesma Rachel.

### Obrigatorio

- wake/STT/TTS quando habilitados;
- mesma conversation/session;
- voz chama o entrypoint cognitivo canonico;
- tools e approvals funcionam igual ao chat;
- barge-in/cancel;
- erro de STT/TTS nao derruba runtime;
- estados de escuta/fala visiveis;
- fallback quando dispositivo nao existe;
- testes com adapters fake;
- smoke em hardware alvo antes do release.

### Gate

Uma tarefa iniciada por voz deve criar o mesmo tipo de run, usar as mesmas policies e poder continuar no chat sem perder contexto.

---

## ETAPA 14 — Desktop UX profissional

### Objetivo

Transformar o runtime tecnico em produto utilizavel sem depender do terminal para operacao normal.

### Obrigatorio

- chat streaming;
- history;
- memory/knowledge quando expostos;
- plan/run viewer;
- tool cards;
- approvals claros;
- diff viewer para alteracoes de codigo/arquivo;
- cancel/pause/resume;
- erros acionaveis;
- model/provider status;
- browser state quando ativo;
- settings;
- diagnostics/doctor;
- logs acessiveis sem vazar segredo;
- loading/empty/offline states;
- keyboard/accessibility minima;
- production build;
- Tauri check/build;
- sem botoes para capacidades inexistentes.

### Gate

Um usuario deve conseguir executar pelo desktop o fluxo chat -> plano -> tool -> approval -> resultado sem abrir terminal.

---

## ETAPA 15 — Hardening, Release e selo final

### Objetivo

Provar que a soma das 14 etapas forma um produto robusto, e nao apenas modulos isolados.

### 15A — Security hardening

Obrigatorio testar:

- path traversal;
- SSRF;
- prompt/tool injection;
- permission escalation;
- approval replay;
- approval mismatch;
- secret leakage/redaction;
- unsafe shell fallback;
- destructive operations;
- malicious/invalid MCP schema;
- browser target mismatch;
- data corruption/migration failure.

### 15B — Reliability e recovery

- restart recovery;
- crash recovery;
- SQLite/schema migrations;
- cancellation;
- timeout;
- model failure/fallback;
- tool failure;
- browser failure;
- MCP failure;
- no false success;
- logs/traces suficientes para diagnostico.

### 15C — Performance

Medir no hardware-alvo:

- startup;
- TTFT;
- total latency;
- planner overhead;
- tool latency;
- project context build;
- memory/knowledge lookup;
- uso de RAM/CPU;
- ausencia de crescimento de memoria descontrolado em sessao longa.

Targets devem ser realistas para o hardware/modelo real e ficar versionados como benchmark, nao como promessa artificial.

### 15D — E2E profissional

Suite obrigatoria deve provar:

1. conversa persistente;
2. streaming e cancelamento;
3. memoria com consentimento;
4. esquecimento/delete;
5. conhecimento documental;
6. web research com citacoes;
7. pesquisa com conflito/freshness;
8. project intelligence;
9. arquivo read/write/patch;
10. Git status/diff/commit controlado;
11. build/test falhando e sendo reportado corretamente;
12. plano multi-etapa;
13. approval e resume;
14. browser read;
15. browser effect governado;
16. MCP tool;
17. voz/shared session;
18. desktop end-to-end;
19. restart/recovery;
20. doctor/health.

### 15E — Release engineering

- versionamento semantico;
- release notes;
- installer Windows reproduzivel;
- dependencias/runtime empacotados ou onboarding claro;
- configuracao `.env.example` sem segredos;
- migration path;
- uninstall sem apagar dados do usuario silenciosamente;
- checksums/artefatos quando aplicavel;
- documentacao de instalacao e troubleshooting;
- tag `v1.0.0` somente depois do gate.

### 15F — Rachel Model

Pesos proprios **nao sao requisito para a Rachel V1 ser completa**. O sistema deve permanecer provider-agnostic.

Treinamento/fine-tuning e permitido apenas se:

- hardware adequado;
- dataset aprovado;
- privacy gate;
- benchmark baseline;
- processo de promocao/reversao;
- licenca da base compativel.

Se essas condicoes nao existirem, Rachel V1 usa providers configuraveis e continua completa.

---

# 6. Gate Final — `Professional Agent Ready`

O selo final so pode ser emitido quando TODOS os itens abaixo forem verdadeiros:

```text
15 / 15 etapas = VALIDATED
```

E adicionalmente:

- [ ] `main` e a unica fonte oficial da V1;
- [ ] nenhuma regressao conhecida P0/P1 aberta;
- [ ] todas as workflows oficiais de release estao verdes;
- [ ] nenhum teste obrigatorio e mascarado como sucesso;
- [ ] frontend production build passa;
- [ ] Tauri build/check passa;
- [ ] installer funciona em instalacao limpa;
- [ ] `rachel doctor`/equivalente reporta estado coerente;
- [ ] chat real foi testado com provider real;
- [ ] terminal/CLI smoke funciona para diagnostico;
- [ ] desktop smoke funciona;
- [ ] E2E das capacidades essenciais passa;
- [ ] security suite passa;
- [ ] recovery suite passa;
- [ ] benchmark de hardware-alvo registrado;
- [ ] nenhuma capability critica e hardcoded como `true` sem backend real;
- [ ] nenhuma acao de tool pode declarar sucesso sem evidencia;
- [ ] segredos nao estao no repositorio/logs/prompts;
- [ ] documentacao corresponde ao codigo atual;
- [ ] existe release candidate congelado;
- [ ] `v1.0.0` criada somente apos a validacao do release candidate.

Somente entao:

```text
RACHEL_STATUS = PROFESSIONAL_AGENT_READY
RACHEL_VERSION = 1.0.0
ACTIVE_FEATURE_DEVELOPMENT = FROZEN
```

Depois disso, Rachel passa para manutencao/referencia e o desenvolvimento principal segue no ZANE.

---

# 7. Regra de acompanhamento para nao se perder

A partir deste documento, cada etapa deve manter quatro campos:

```text
STATE
LAST_VALIDATED_COMMIT
LAST_VALIDATED_CI
OPEN_GATE_ITEMS
```

Ao terminar um sublote:

1. implementar;
2. criar teste;
3. rodar/observar CI;
4. corrigir regressao;
5. atualizar documento da etapa;
6. atualizar este tracker somente se o estado realmente mudou.

Nao aumentar percentual porque "muito codigo foi escrito". O que aumenta a contagem formal e fechar gates.

---

# 8. Pendencias prioritarias a partir deste snapshot

Ordem recomendada para reduzir incerteza rapidamente:

1. corrigir/reconciliar o workflow legado `tests` que atualmente pode falhar por timeout em `test_agent_bridge`;
2. revalidar formalmente Etapas 2, 3 e 5 e criar/atualizar suas evidencias finais;
3. fechar Etapa 9 apos a limpeza de capability e CI atual;
4. concluir Etapa 11 inteira, nao apenas read-only;
5. executar Etapa 12 MCP;
6. fechar Etapa 13 voz compartilhando a sessao do Agent Loop;
7. fechar Etapa 14 Desktop UX;
8. executar Etapa 15 como release candidate real.

---

# 9. Como interpretar "falta muito?"

Neste snapshot:

```text
VALIDATED                         6
IMPLEMENTED_REVALIDATION_REQUIRED 4
IN_PROGRESS                       1
PARTIAL                            2
PLANNED                            2
TOTAL                             15
```

Os itens `PARTIAL` correspondem principalmente a voz e desktop, que ja possuem base mas ainda nao fecharam o contrato profissional. As etapas 12 e 15 ainda exigem trabalho substancial.

A Rachel ja possui uma fundacao forte e varias capacidades profissionais reais. Entretanto, **nao deve ser chamada de 90% ou quase pronta enquanto Browser, MCP, voz integrada, UX final e hardening/release ainda nao estiverem fechados**.

A melhor leitura atual e:

> **codigo/maturidade: aproximadamente 60–65%; release formal: 6/15 gates fechados.**

Esse numero devera subir rapidamente quando 2, 3, 5 e 9 forem revalidadas, porque grande parte do codigo ja existe. O progresso final sera mais lento na Etapa 15, pois ela exige provar o produto inteiro em conjunto.

---

# 10. Fonte de verdade

Este documento **substitui o antigo `roadmap-profissional.md` como tracker principal de progresso**, preservando o arquivo antigo apenas como registro historico.

Os documentos `etapa-XX-*.md` continuam sendo as evidencias detalhadas de cada etapa.

Em caso de divergencia entre um documento antigo e o codigo/CI atual:

1. codigo atual;
2. testes/CI atuais;
3. este roadmap atualizado;
4. documentos historicos.

Nenhuma capacidade sera considerada pronta apenas porque uma documentacao antiga diz que esta pronta.
