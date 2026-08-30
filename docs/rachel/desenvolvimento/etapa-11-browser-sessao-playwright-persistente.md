# Rachel — Etapa 11: Sessão Playwright persistente

**Estado:** IMPLEMENTADO / VALIDACAO CI PENDENTE  
**Data:** 2026-08-30  
**Repositorio de codigo:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Fechar a lacuna entre a persistencia logica de `session_id/page_id` e a persistencia real de um `BrowserContext/Page` Playwright vivo entre chamadas, sem habilitar prematuramente efeitos remotos como click, formulario, login, upload ou download.

## Implementacao

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/browser_live_session_runtime.py`.

Commits:

- `92f4505144b6f271227c95a3ec71b121d4d96002` — `feat(browser): adicionar backend persistente de contexto Playwright`;
- `2ebbe5f0cfdf22cd979ac96c07e9698d4a77f7b6` — `test(browser): validar contexto persistente governado`;
- `763228bbc069965cf096c9438e7f5eab9098cda0` — `ci(browser): incluir contexto persistente na regressão crítica`.

## Arquitetura

```text
BrowserLiveSessionRuntime
    |
    +--> BrowserSessionManager
    |       - session_id
    |       - page_id
    |       - current_url
    |       - origin
    |       - TTL / cleanup
    |
    +--> PlaywrightSessionBackend
            - browser lazy
            - BrowserContext persistente por session_id
            - Page persistente por session_id
            - request guard em toda requisicao
            - fechamento explicito
```

O browser Playwright continua lazy: nenhum Chromium e iniciado ao importar o runtime. O processo so e aberto no primeiro uso real de uma sessao persistente.

## Politica de rede

A mesma funcao de validacao de URL e injetada no runtime e no backend. Ela e aplicada:

1. antes de qualquer navegacao;
2. em toda request/subrequest Playwright por `page.route("**/*", guard)`;
3. sobre a URL final da pagina antes de atualizar o estado logico da sessao.

Assim, falha de validacao ocorre antes da mutacao do estado conhecido e subrequests bloqueadas nao recebem excecao de politica.

## Semantica de sessao

`BrowserLiveSessionRuntime.create(url)` cria primeiro uma sessao logica e associa a ela um contexto Playwright real. Em caso de falha durante criacao/navegacao inicial, tanto o contexto vivo quanto a sessao logica sao fechados, evitando handles orfaos.

`navigate(session_id, url)` reutiliza o mesmo contexto e a mesma pagina da sessao. Somente depois de receber evidencia valida do backend o `BrowserSessionManager` atualiza `current_url` e `origin`.

`close(session_id)` fecha o `BrowserContext` e remove a sessao logica.

`cleanup()` fecha contextos associados a sessoes expiradas pelo manager logico.

## Regra de seguranca

Este sublote permanece **read-only**.

O status declara:

```text
live_playwright_context_persistence = true
effectful_actions_enabled = false
mode = persistent-read-only
```

Essa distincao e obrigatoria. Ter uma pagina persistente nao autoriza click/form/login/upload/download. Esses efeitos continuam dependendo do contrato de efeitos, do `ApprovalStore`, do Cyber e de verificacao de pos-condicao.

## Testes

Foi criado `RACHEL_PLATFORM/RUNTIME/TESTS/test_browser_live_session_runtime.py` com backend fake, sem exigir Chromium na CI de contrato.

O teste comprova:

1. criacao de contexto vivo associado ao mesmo `session_id`;
2. segunda navegacao reutilizando a mesma sessao;
3. URL invalida bloqueada antes da mutacao do backend/estado;
4. fechamento conjunto do contexto vivo e estado logico;
5. status verdadeiro, mantendo efeitos interativos desabilitados.

O teste foi incluido na regressao critica do `RACHEL CI`.

## Gate

- persistencia logica de sessao: **IMPLEMENTADA / TESTADA**;
- persistencia de `BrowserContext/Page` por session_id: **IMPLEMENTADA / TESTADA POR CONTRATO**;
- Playwright lazy: **PRESERVADO**;
- WebPolicy/SSRF em toda request: **PRESERVADO**;
- click/form/login/upload/download: **NAO HABILITADOS**;
- smoke real com Chromium: **PENDENTE PARA AMBIENTE APROPRIADO**;
- CI do novo head: **PENDENTE DE CONCLUSAO**.

## Proximo sublote

O proximo passo correto e integrar a sessao persistente ao `BrowserRuntime`/ToolCoordinator e, somente depois, habilitar `click` e `form` em lote separado com:

- `session_id` e `page_id` obrigatorios;
- seletor/alvo normalizado;
- argumentos completos incluidos no hash do approval;
- approval one-shot;
- verificacao de pos-condicao;
- no false success;
- testes de tentativa de reutilizacao de approval em outro alvo/pagina.
