# Rachel — Etapa 11: contrato de efeitos interativos do Browser Governado

**Estado:** IMPLEMENTADO EM CONTRATO / EXECUTOR AINDA DESABILITADO  
**Data:** 2026-08-29  
**Repositorio de codigo:** `restoffkaua08-afk/rachel-ia`  
**Branch:** `main`

## Motivo

Antes de habilitar `browser.click`, `browser.form`, `browser.login`, `browser.upload` e `browser.download`, a Rachel precisa garantir que uma autorizacao do Cyber esteja vinculada exatamente ao alvo e aos parametros que serao executados. A aprovacao nao pode autorizar genericamente "mexer no navegador" nem ser reutilizada em outra pagina, seletor ou conjunto de campos.

O `ApprovalStore` existente ja protege approvals com hash canonico de argumentos, ferramenta e efeito, e consome cada approval apenas uma vez. O novo contrato browser passa a estruturar os argumentos que obrigatoriamente devem entrar nesse hash.

## Implementacao

Codigo criado:

- `RACHEL_PLATFORM/RUNTIME/SRC/browser_effect_contract.py`.

Commits:

- `b847dbcbb6f1f9138da778c6575bb6158fd91878` — `feat(browser): definir contrato seguro para efeitos interativos`;
- `359b9a131b946cf0f3b442e45c633ab67da4e2b9` — `test(browser): validar binding de efeitos e pós-condições`;
- `5d7980953a2153db99ac4d5fa26a7b893bef0969` — `ci(browser): incluir contrato de efeitos na regressão crítica`.

## Contrato obrigatorio

Todo efeito interativo exige os identificadores da sessao e pagina:

```text
session_id
page_id
action
selector
```

Além disso, toda ação deve declarar pelo menos uma pós-condição verificável:

```text
expected_url_prefix
ou
expected_text
```

Sem uma pós-condição, o efeito é rejeitado antes de chegar ao executor. A Rachel não poderá considerar um clique/formulário concluído apenas porque a chamada Playwright não lançou exceção; deverá existir evidência observável de mudança esperada.

## Binding por tipo de ação

### click

Binding mínimo:

```text
session_id + page_id + selector + pós-condição
```

### form

Binding mínimo:

```text
session_id + page_id + selector + fields + pós-condição
```

Os campos fazem parte do payload aprovado. Alterar um valor depois da aprovação produz outro hash e invalida o approval anterior.

### login

Segue o mesmo contrato estrutural de form e exige `fields`. Credenciais continuam sujeitas às políticas de segredo/privacidade da Rachel; este sublote não cria armazenamento de credenciais nem habilita login automático.

### upload

Binding mínimo:

```text
session_id + page_id + selector + file_path + pós-condição
```

O executor ainda não está habilitado. Quando for implementado, `file_path` também deverá passar pela política de filesystem/scope e classificação de sensibilidade antes de qualquer envio externo.

### download

Binding mínimo:

```text
session_id + page_id + selector + pós-condição
```

Pode incluir `suggested_name`. O download real ainda deverá ser colocado em diretório governado, com validação de tamanho/tipo e evidência de arquivo recebido.

## Proteções de contrato

O módulo implementa:

- ações permitidas explicitamente, com deny-by-default;
- `session_id` e `page_id` obrigatórios;
- seletor obrigatório para todas as ações atualmente previstas;
- rejeição de caracteres de controle no seletor;
- tamanho máximo de seletor;
- ao menos uma pós-condição obrigatória;
- limite de 50 campos em form/login;
- validação de nome e tamanho de valores dos campos;
- `file_path` obrigatório em upload;
- payload canônico para aprovação.

## Relação com Cyber

O `ApprovalStore` existente calcula SHA-256 sobre os argumentos canônicos e verifica no consumo:

1. `tool` idêntica;
2. `effect` idêntico;
3. hash dos argumentos idêntico;
4. approval ainda válido;
5. status `approved`;
6. consumo one-shot.

Com isso, quando o ToolCoordinator passar a usar `approval_bound_arguments()`, uma autorização para:

```text
browser.click
session A
page 1
button#save
```

não poderá ser reutilizada para:

```text
browser.click
session A
page 1
button#delete
```

nem para outra página ou outro conjunto de parâmetros.

## Testes

`test_browser_effect_contract.py` valida:

- presença obrigatória de session/page/selector/pós-condição;
- rejeição sem pós-condição;
- rejeição de seletor com caracteres de controle;
- inclusão de `fields` no payload aprovado;
- inclusão de `file_path` em upload;
- alteração de seletor muda `arguments_hash`;
- alteração de `page_id` muda `arguments_hash`.

## Regra de qualidade

Este sublote **não habilita nenhum efeito remoto**. O executor continua desabilitado até que:

- `BrowserSessionManager` esteja ligado ao BrowserRuntime;
- exista `BrowserContext/Page` persistente real;
- approvals usem este contrato antes da execução;
- click/form tenham pós-condições verificadas após a ação;
- testes provem que approval de um alvo não autoriza outro;
- CI profissional esteja verde.

Isso preserva o princípio `no false success` e impede que uma estrutura parcial seja anunciada como browser interativo pronto.
