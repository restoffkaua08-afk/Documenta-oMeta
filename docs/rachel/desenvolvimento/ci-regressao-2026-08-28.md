# Rachel — Regressão de CI de 2026-08-28

**Estado:** correção aplicada; validação final em andamento  
**Repositório de código:** `restoffkaua08-afk/rachel-ia`  
**Branch:** `main`

## Contexto

Após a integração do roteamento natural de intenções de browser no Ned, o head `91949b66ae8d5644e09cb92ecd676075a00f87a0` disparou duas regressões observáveis em workflows distintos.

### RACHEL CI

O workflow profissional falhou no job `Python Core + Runtime contracts` antes de executar a regressão de infraestrutura porque `RACHEL_PLATFORM/RUNTIME/TESTS/test_infrastructure.py` passou a importar `pytest`, enquanto esse workflow profissional executa os contratos críticos por `unittest` e não instala `pytest` como dependência do produto.

Erro observado:

```text
ModuleNotFoundError: No module named 'pytest'
```

A causa era de teste/CI, não uma falha funcional do roteamento de browser.

## Correção aplicada

Commit na Rachel:

- `08d7fc393e0c3c86fb2b6152260b1fdcb1ddebad` — `fix(ci): remover dependencia pytest do teste de infraestrutura`.

Mudanças:

- removido `import pytest` de `test_infrastructure.py`;
- o teste que depende dos 23 submódulos deixou de usar markers/xfail do pytest;
- foi adotado `unittest.skipUnless` com opt-in explícito `RACHEL_TEST_SUBMODULES=1`;
- os testes de roteamento natural de browser permanecem executáveis somente com a biblioteca padrão e os módulos da Rachel;
- o gate de submódulos não foi mascarado como sucesso: ele aparece como `skipped` quando o ambiente não declara possuir os submódulos e pode ser ativado explicitamente no ambiente apropriado.

## Evidência parcial do novo run

Novo workflow profissional:

- `RACHEL CI` run `33201886721`;
- head `08d7fc393e0c3c86fb2b6152260b1fdcb1ddebad`.

No momento desta atualização:

- `Python Core + Runtime contracts`: **PASS**;
- `Desktop frontend build`: **PASS**;
- `Tauri Rust check`: **IN PROGRESS**.

A etapa não deve ser classificada como integralmente verde até o último job terminar.

## Workflow legado `tests`

O workflow legado do head anterior apresentou timeout em:

```text
RACHEL_PLATFORM/RUNTIME/TESTS/test_agent_bridge.py::AgentBridgeTests::test_dashboard_contains_agent_status
```

O Core do mesmo run passou 78/78 testes. O stack de timeout mostra o bridge aguardando a ação `dashboard` via subprocesso.

A auditoria do código mostrou que `dashboard()` chama `SamwellRuntime().status()`. O status completo do Samwell executa `audit()`, que percorre dependências e pode executar probes externos via `subprocess.run(..., timeout=10)` de forma síncrona. Isso torna o dashboard suscetível a ultrapassar o timeout global de 30 segundos em ambientes Windows/CI.

### Decisão de qualidade

Não aumentar o timeout global nem esconder a falha como solução principal. O dashboard deve ser uma visão operacional rápida; diagnósticos profundos de dependências pertencem ao caminho explícito de doctor/audit.

Próxima correção planejada:

1. separar status resumido de dashboard de auditoria profunda do Samwell;
2. preservar `SamwellRuntime.status()`/audit profundo para diagnóstico explícito, ou fornecer modo explícito de probe;
3. fazer o dashboard declarar quando probes não foram executados, em vez de fabricar readiness;
4. adicionar regressão que prove que o dashboard não dispara probes externos caros;
5. executar ambos os workflows novamente.

## Regra de aceite

Esta regressão só será considerada encerrada quando:

- workflow profissional `RACHEL CI` estiver completamente verde;
- workflow legado `tests` estiver verde ou tiver sido formalmente substituído/removido com justificativa técnica;
- nenhum teste obrigatório for mascarado;
- documentação refletir o resultado observado.