# Correção JARVIS-004 — RBAC funcional sem extensão Rust

## Registro

- Data: 2026-08-24
- Repositório: `restoffkaua08-afk/Jarvis-ia`
- Branch: `main`
- Implementação: `c113e0d43a6c4f134822caa574dc6afa9c40d6eb`
- Testes: `c5c29e7f501e050575c822a6a7dc4136afebb2cf`

## Problema

`CapabilityPolicy` criava diretamente a implementação Rust. Quando a extensão nativa não estava instalada, o construtor lançava `ImportError`. `setup_security()` capturava a falha e prosseguia com `capability_policy=None`.

O baseline Windows validado utiliza o perfil desktop sem o grupo `desktop-native`. Portanto, a extensão Rust não pode ser considerada obrigatória para o funcionamento da segurança.

## Risco

O Jarvis podia apresentar scanners, auditoria e outras proteções como ativas enquanto o controle RBAC das ferramentas estava ausente. Shell, arquivos, rede e demais ferramentas deixavam de receber verificação de capacidades.

## Correção

- A extensão Rust continua sendo utilizada quando disponível.
- `ImportError` e ausência da implementação nativa acionam o fallback Python.
- `grant()` e `deny()` sincronizam com Rust somente quando a implementação existe.
- `check()` usa Rust quando possível e `_check_python()` nos demais ambientes.
- A implementação Python deixou de ser classificada como referência legada e passou a ser parte suportada do runtime.

## Teste

O teste força `get_rust_module()` a lançar `ImportError` e valida:

- criação bem-sucedida da política;
- concessão limitada por padrão de recurso;
- negação fora do escopo;
- precedência de negação explícita;
- confirmação de que o backend Rust permaneceu ausente.

## Compatibilidade

Não há alteração de interface pública. Ambientes com Rust preservam o caminho acelerado. Ambientes sem Rust passam a ter RBAC funcional em vez de desativação silenciosa.
