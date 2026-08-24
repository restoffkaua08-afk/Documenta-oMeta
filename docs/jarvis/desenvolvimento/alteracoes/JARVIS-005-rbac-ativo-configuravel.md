# JARVIS-005 — RBAC ativo e configurável

## Objetivo

Ativar o controle de capacidades do Jarvis por padrão sem interromper as
ferramentas já existentes. A migração usa modo compatível: a política é
instanciada automaticamente, mas agentes sem regras explícitas continuam
permitidos enquanto o catálogo de perfis e concessões é preparado.

## Alterações implementadas

### Configuração

Em `CapabilitiesConfig`:

- `enabled` passou de `false` para `true`;
- foi adicionado `default_deny: bool = false`;
- o campo pode ser sobrescrito em `[security.capabilities]` no TOML.

### Inicialização de segurança

`setup_security()` agora encaminha `default_deny` para
`CapabilityPolicy`. Isso garante que a decisão configurada seja aplicada
tanto pela extensão Rust quanto pelo fallback Python introduzido no
JARVIS-004.

## Comportamento resultante

| Configuração | Resultado |
|---|---|
| `enabled = true`, `default_deny = false` | RBAC ativo em modo compatível |
| `enabled = true`, `default_deny = true` | capacidades não concedidas são negadas |
| `enabled = false` | política desativada explicitamente |
| `security.enabled = false` | camada de segurança inteira desativada |

Negações explícitas e políticas carregadas de arquivo passam a ser efetivas
na configuração padrão. O modo `default_deny = true` já funciona, mas não é
o padrão até que os perfis e grants necessários sejam concluídos.

## Commits no Jarvis

- configuração: `3b5064eca06779e3bba547137456d4cdd819f8fa`;
- inicialização: `b5ab009a607fea7f33bdc1e033a7b07be71c9928`;
- testes de configuração: `c95372806bed4388a1140adc6be958782ca5778d`;
- testes de integração: `8e2c8b56c2704439b5c376e08dbbe3b9c87dad3b`.

## Validação realizada

- sintaxe Python validada nos quatro arquivos alterados;
- nenhum arquivo alterado possui linha acima de 88 caracteres;
- testes adicionados para valores padrão;
- teste adicionado para leitura de `default_deny` pelo TOML;
- testes adicionados para modo compatível e deny-by-default;
- compatibilidade sem Rust preservada pelo fallback Python do JARVIS-004.

A suíte integral ainda precisa ser executada em um clone com todas as
dependências. Portanto, esta etapa está validada estaticamente e coberta por
testes versionados, sem alegação de execução integral local.

## Próxima etapa

Criar perfis de segurança e grants mínimos por tipo de agente. Somente depois
disso o projeto poderá avaliar a mudança segura do padrão global para
`default_deny = true`.
