# Rachel — Etapa 11: Browser Governado

**Estado:** EM VALIDACAO INCREMENTAL  
**Data:** 2026-08-27  
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

## CI

Commit:

- `3124f23276f6cd8546f3c9e85d6c484ff9dbb0f7` — `ci(browser): incluir integração do browser governado`.

A regressao critica inclui agora:

- `test_browser_runtime.py`;
- `test_browser_tools.py`.

O run `33074979083`, no head `3124f23276f6cd8546f3c9e85d6c484ff9dbb0f7`, concluiu com:

- Python Core + Runtime contracts: **PASS**;
- Desktop frontend build: **PASS**;
- Tauri Rust check: **PASS**.

Portanto, o primeiro gate integrado de BrowserRuntime + ToolCoordinator + Cyber esta verde. A Etapa 11 como um todo continua aberta porque os efeitos interativos, roteamento natural e sessao persistente ainda nao foram concluidos.

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
- click/form/login/upload/download depois do approval: **PENDING**;
- roteamento natural deterministico no Ned: **PENDING**;
- sessao persistente/tab state: **PENDING**;
- smoke real Playwright com browser instalado: **PENDING PARA AMBIENTE APROPRIADO**;
- CI deste sublote: **GREEN**;
- CI final da etapa: **PENDING**.

## Proximos passos

1. adicionar roteamento natural confiavel para pedidos de navegacao;
2. criar contrato de sessao/tab que impeça efeitos sem pagina alvo conhecida;
3. implementar click/form/login/upload/download em sublotes independentes;
4. vincular approvals a acao + alvo + parametros para impedir reutilizacao indevida;
5. testar que approval de uma operacao nao autoriza outra;
6. executar smoke Playwright real quando dependencia/browser estiver disponivel;
7. fechar Etapa 11 somente com CI completa verde e gate funcional comprovado.
