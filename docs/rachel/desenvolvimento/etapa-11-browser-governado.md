# Rachel — Etapa 11: Browser Governado

**Estado:** Em implementacao incremental  
**Data:** 2026-08-27  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Adicionar navegacao browser real e governada sem transformar o browser em uma superficie de execucao irrestrita. A etapa deve reutilizar a politica de rede/SSRF ja existente, separar leitura de efeitos e permitir que Cyber governe click, formulario, login, upload e download antes de qualquer um desses efeitos ser habilitado.

## Auditoria inicial

O repositorio nao possuia `browser_runtime.py` nem integracao Playwright encontrada na auditoria inicial. O runtime web existente ja possui `WebPolicy`, normalizacao de URL e `validate_url()` com bloqueio de hosts/redes internas, portanto a Etapa 11 deve reutilizar essa fronteira em vez de criar uma segunda politica de rede independente.

O registry atual possui `web.fetch`, `web.search` e `web.research`, mas ainda nao possui ferramentas `browser.*`.

O `RACHEL_CORE/pyproject.toml` nao declara Playwright como dependencia do Core. Por isso, o primeiro lote mantem Playwright como backend lazy/opcional do Runtime, evitando contaminar o Core e evitando exigir binarios de browser para toda execucao de CI.

## Lote 11A — boundary read-only

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/browser_runtime.py`.

Commit:

- `214caa43f51b955bd25f6d5f5a616c4549c4fdfb` — `feat(browser): adicionar runtime governado read-only`.

### Arquitetura

O modulo introduz:

- `BrowserPageEvidence`;
- `BrowserRuntime`;
- `PlaywrightBrowserBackend`;
- `BrowserError`;
- `BrowserUnavailableError`.

O backend Playwright e importado apenas quando uma operacao real de browser e solicitada. Isso mantem testes e Core independentes da instalacao de Chromium/Playwright.

### Politica de rede

O BrowserRuntime reutiliza:

```text
web_runtime.WebPolicy
        +
web_runtime.validate_url
```

A validacao ocorre no URL solicitado e o backend foi desenhado para aplicar a mesma validacao a cada request HTTP(S) interceptado pela pagina, reduzindo risco de SSRF por redirect ou subrequest.

O URL final tambem e validado antes de ser exposto como evidencia.

### Separacao de efeitos

O boundary classifica desde o primeiro lote:

```text
open/title/read
    -> external-read

click/form/login/upload/download
    -> external-effect
```

Neste lote apenas navegacao/leitura esta habilitada. Operacoes de efeito ainda nao foram implementadas.

Isso evita adicionar formulario/login/click antes de existir contrato Cyber explicito para cada classe de efeito.

## Testes

Foi criado:

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_browser_runtime.py`.

Commit:

- `aff9f5b2f99e4f0bb9b31e10b3ef4da2db598fb9` — `test(browser): validar navegacao read-only e politica SSRF`.

Os testes cobrem:

- evidencia de pagina governada;
- projecao minima de titulo;
- bloqueio de `localhost` pela politica SSRF;
- separacao de efeitos read-only vs effectful;
- rejeicao de acao desconhecida;
- rejeicao de backend que devolve contrato invalido;
- garantia de que efeitos permanecem desabilitados no status.

## CI

O workflow `RACHEL CI` foi atualizado para incluir explicitamente `test_browser_runtime.py` na regressao critica.

Commit:

- `0d3b2b25741d2f6b56a9db6038d10a9afbf0ea9f` — `ci(browser): validar runtime governado na regressao`.

## Gate atual

- boundary de browser: **IMPLEMENTED**;
- backend Playwright lazy: **IMPLEMENTED**;
- reutilizacao de WebPolicy/SSRF: **IMPLEMENTED**;
- validacao de requests durante navegacao: **IMPLEMENTED**;
- leitura de titulo/conteudo: **IMPLEMENTED NO BOUNDARY**;
- testes unitarios governados: **IMPLEMENTED**;
- CI do lote: **PENDING**;
- ferramentas `browser.*` no ToolCoordinator: **PENDING**;
- instalacao/configuracao reproduzivel do Playwright: **PENDING**;
- click/form/login/upload/download: **PENDING E DELIBERADAMENTE DESABILITADOS**;
- aprovacao Cyber para efeitos: **PENDING**;
- Agent Loop usando browser: **PENDING**.

## Proximo lote

1. validar o lote 11A em CI;
2. registrar `browser.status`, `browser.open` e `browser.title` no registry;
3. integrar somente as operacoes read-only ao `ToolCoordinator`;
4. testar o caminho ToolCoordinator -> BrowserRuntime com backend fake;
5. somente depois materializar dependencias/install de Playwright e iniciar efeitos governados.
