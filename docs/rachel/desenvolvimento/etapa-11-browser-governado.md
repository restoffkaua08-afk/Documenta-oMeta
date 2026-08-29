# Rachel — Etapa 11: Browser Governado

**Estado:** EM VALIDACAO INCREMENTAL  
**Data:** 2026-08-29  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Adicionar navegacao browser real e governada sem transformar o browser em uma superficie de execucao irrestrita. A etapa reutiliza a politica de rede/SSRF existente, separa leitura de efeitos remotos e faz o Cyber governar click, formulario, login, upload e download antes de qualquer efeito ser executado.

## Auditoria e origem

A Etapa 11 foi iniciada em lotes anteriores com um boundary read-only:

- `214caa43f51b955bd25f6d5f5a616c4549c4fdfb` — `feat(browser): adicionar runtime governado read-only`;
- `aff9f5b2f99e4f0bb9b31e10b3ef4da2db598fb9` — `test(browser): validar navegacao read-only e politica SSRF`;
- `0d3b2b25741d2f6b56a9db6038d10a9afbf0ea9f` — `ci(browser): validar runtime governado na regressao`.

Esse boundary ja possuia `BrowserPageEvidence`, `BrowserRuntime`, backend Playwright lazy e reaproveitamento de `WebPolicy`/`validate_url` para evitar uma segunda politica de rede independente.

## Arquitetura de rede

```text
BrowserRuntime
    |
    v
PlaywrightBrowserBackend
    |
    +--> URL inicial -> validate_url
    |
    +--> requests/subrequests -> guard -> validate_url
    |
    +--> URL final -> validate_url
    |
    v
Internet publica autorizada
```

Playwright continua sendo carregado somente quando uma operacao real solicita browser. O Core e os testes de contrato nao dependem de binarios Chromium para iniciar.

## Sublote — alinhamento de efeitos ao Cyber

Commit:

- `231f1f90b7ed6c0c28d360e909c666f22293ff0b` — `feat(browser): alinhar efeitos ao Cyber e expor leitura`.

A classificacao passou a usar o vocabulario que `CyberPolicy` realmente entende:

```text
browser.open
browser.title
browser.read
    -> read

browser.click
browser.form
browser.login
browser.upload
browser.download
    -> external
```

Isso evita efeitos declarativos sem policy correspondente. `read` pertence ao conjunto LOW do Cyber; `external` pertence ao conjunto MEDIUM e requer autorizacao.

Tambem foi adicionado `BrowserRuntime.read()`, que devolve URL solicitada, URL final, titulo, texto visivel e tamanho do texto dentro do limite do WebPolicy.

## Testes do BrowserRuntime

Commit:

- `95f80140873ee2ab88fff85061ec50d7b2739f2d` — `test(browser): validar leitura e efeitos governados`.

Os testes comprovam:

- navegacao read-only com evidencia;
- titulo governado;
- leitura de texto visivel;
- bloqueio de localhost/SSRF;
- separacao `read` vs `external`;
- rejeicao de acao desconhecida;
- rejeicao de backend com contrato invalido;
- status que nao anuncia efeitos remotos como habilitados.

## Registry oficial

Commit:

- `890eca8787a07ecd67762323f639ab5ae5428fe1` — `feat(browser): registrar ferramentas governadas`.

O schema de `RACHEL_PLATFORM/CONFIG/tools.registry.json` foi elevado para `1.7`.

Ferramentas registradas:

```text
browser.status
browser.open
browser.title
browser.read
browser.click
browser.form
browser.login
browser.upload
browser.download
```

As operacoes de efeito sao visiveis no contrato, mas a descricao deixa explicito que o executor ainda esta desabilitado. Isso permite que planner, Cyber, UI e testes conhecam a capacidade sem anunciar funcionalidade inexistente.

## Integracao com ToolCoordinator

Commit:

- `bbe540ddcf0b664645a8d8e131b03fed313a79ca` — `feat(browser): integrar browser governado ao ToolCoordinator`.

`ToolCoordinator` agora possui uma dependencia injetavel `BrowserRuntime` e executa:

- `browser.status`;
- `browser.open`;
- `browser.title`;
- `browser.read`.

Todas passam pelo mesmo pipeline de tools:

```text
Ned
 |
 v
ToolCoordinator
 |
 +--> CyberPolicy
 |
 +--> ApprovalStore (quando necessario)
 |
 +--> KingEventBus
 |
 +--> JhonLogger
 |
 v
BrowserRuntime
```

Para `click/form/login/upload/download`, o fluxo sem approval e interrompido pelo Cyber em `approval_required`. Caso um approval seja fornecido, o executor ainda retorna erro explicito de capacidade nao habilitada. A Rachel portanto nao pode fingir que clicou/preencheu/enviou quando o executor interativo ainda nao existe.

## Teste integrado ToolCoordinator -> Browser

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_browser_tools.py`.

Commit:

- `da81d0401df1a25a9f7d9315489b1f18a0bf9fc0` — `test(browser): validar integração governada no ToolCoordinator`.

O teste usa backend fake e bancos temporarios e comprova:

- `browser.title` executa sem approval e reporta `effective_effect=read`;
- `browser.read` retorna texto visivel;
- `browser.form` sem approval retorna `approval_required`;
- o efeito recebido pelo Cyber e `external`;
- um approval e criado;
- ferramentas `browser.*` sao descobertas pelo registry.

## Roteamento natural no Ned

Commits ja incorporados em `main`:

- `16e3460` — `feat(ned): rotear intenções naturais de browser`;
- `35499b7` — `test(ned): validar roteamento natural de browser`;
- `91949b6` — `ci(ned): incluir roteamento natural de browser`.

`NedRouter.browser_intent()` reconhece URL explicita e intencoes read-only para `browser.open`, `browser.title` e `browser.read`, evitando falsos positivos como pedidos de titulo de projeto sem contexto web.

Esse sublote esta implementado e coberto no roteador, mas o gate E2E ainda exige integrar a decisao deterministica ao caminho canonico do planner/Agent Loop. Portanto nao deve ser tratado como roteamento natural completo da Rachel ainda.

## Sublote — contrato de sessao governada

Commits:

- `abe592800f6bc4690bd3570da48ff3cb64577419` — `feat(browser): adicionar gerenciamento de sessão governada`;
- `6198d25db2658c9c96a60ff30d822120bdedcfa8` — `test(browser): validar ciclo de vida de sessão governada`;
- `4e2e4e5d39652b8a663378df878aad798633ac5f` — `ci(browser): incluir sessões governadas na regressão crítica`.

Foi criado `browser_session_runtime.py` para estabelecer antes dos efeitos interativos o contrato de estado que click/form/login/upload/download deverao consumir.

O `BrowserSessionManager` fornece:

- `session_id` estavel e opaco;
- `page_id` estavel e opaco;
- `current_url`;
- `origin` normalizada;
- `created_at_ms` e `last_used_at_ms`;
- TTL configuravel, com piso defensivo;
- limite global de sessoes;
- eviction LRU deterministica quando o limite e atingido;
- fechamento explicito e idempotente;
- limpeza de sessoes expiradas;
- lock interno para evitar corrupcao concorrente da tabela de sessoes;
- validacao da URL em toda criacao/navegacao antes de alterar estado.

O teste cobre:

1. criacao e navegacao com validacao de toda URL;
2. navegacao invalida sem mutar o estado conhecido;
3. expiracao fail-closed;
4. eviction LRU com numero maximo de sessoes;
5. close explicito/idempotente;
6. status que nao anuncia capacidades ainda inexistentes.

### Regra de verdade do status

O status do manager declara explicitamente:

```text
persistent_metadata = true
live_playwright_context_persistence = false
effectful_actions_enabled = false
```

Essa distincao e deliberada. O projeto agora possui persistencia logica/metadata de sessao, mas ainda nao mantem um `BrowserContext/Page` Playwright vivo entre chamadas. Nao sera permitido marcar sessao browser como plenamente concluida antes de existir persistencia real do contexto Playwright.

## CI

O gate integrado inicial do BrowserRuntime + ToolCoordinator + Cyber foi validado anteriormente no run `33074979083`, com Core/Runtime, frontend e Tauri verdes.

O workflow legado `tests` tambem foi recuperado em lote posterior de hardening do dashboard/Samwell. O run `33203477817`, no commit `ab62901290faae7ec6b6b46ae6c8558495d9fe01`, concluiu com `success`, eliminando o timeout historico usado como blocker de higiene de CI.

O novo teste `test_browser_session_runtime.py` passou a integrar a regressao critica profissional no commit `4e2e4e5d39652b8a663378df878aad798633ac5f`. A conclusao desse novo head deve ser registrada separadamente antes de promover o subgate para `VALIDATED`.

## Gate atual

- boundary browser: **VALIDATED**;
- backend Playwright lazy: **IMPLEMENTED**;
- WebPolicy/SSRF compartilhada: **VALIDATED POR TESTES DE CONTRATO**;
- validacao de requests durante navegacao: **VALIDATED POR TESTES DE CONTRATO**;
- abrir pagina: **VALIDATED NO BOUNDARY/COORDINATOR**;
- obter titulo: **VALIDATED NO BOUNDARY/COORDINATOR**;
- ler texto: **VALIDATED NO BOUNDARY/COORDINATOR**;
- registry `browser.*`: **VALIDATED**;
- ToolCoordinator: **VALIDATED**;
- leitura = Cyber LOW: **VALIDATED**;
- efeitos = Cyber approval: **VALIDATED**;
- efeito remoto sem approval -> `approval_required`: **VALIDATED**;
- roteamento natural deterministico no `NedRouter`: **IMPLEMENTED / TESTED**;
- roteamento natural no planner/Agent Loop canonico: **PENDING**;
- contrato de `session_id/page_id`, TTL, close e LRU: **IMPLEMENTED / TESTED / CI DO NOVO HEAD PENDING**;
- persistencia real de `BrowserContext/Page` Playwright: **PENDING**;
- click/form/login/upload/download depois do approval: **PENDING**;
- smoke real Playwright com browser instalado: **PENDING PARA AMBIENTE APROPRIADO**;
- CI final da etapa: **PENDING**.

## Proximos passos

1. integrar `NedRouter.browser_intent()` ao caminho canonico de planejamento/Agent Loop;
2. ligar `BrowserSessionManager` ao `BrowserRuntime` sem enfraquecer `WebPolicy`;
3. criar backend Playwright persistente que mantenha `BrowserContext/Page` por `session_id` e feche por TTL/close;
4. implementar `click` e `form` primeiro, com seletor/alvo e pos-condicao verificavel;
5. vincular approvals a ferramenta + efeito + session/page + alvo + parametros para impedir reutilizacao indevida;
6. implementar login, upload e download em sublotes separados;
7. executar smoke Playwright real quando dependencia/browser estiver disponivel;
8. fechar Etapa 11 somente com CI completa verde, testes E2E e evidencia de execucao real.
