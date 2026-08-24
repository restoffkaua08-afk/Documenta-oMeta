# Correção JARVIS-002 — aplicação efetiva das capacidades de ferramentas

## Registro

- Data: 2026-08-24
- Repositório alterado: `restoffkaua08-afk/Jarvis-ia`
- Branch: `main`
- Executor: commit `7c7b71d62caca9a7aff995aed0a3431f922a9330`
- Especificação file_read: commit `c411b417e83ddd35a6ae71cf32956c8343de9358`
- Testes: commit `ce45d4ae1c53950b37c42e3a802932d272f93c2a`

## Problema

O módulo de segurança definia `DEFAULT_TOOL_CAPABILITIES`, incluindo o requisito `file:read` para `file_read`. Entretanto, `ToolExecutor` consultava somente `ToolSpec.required_capabilities`.

A ferramenta `file_read` não declarava a capacidade em seu `ToolSpec`. Portanto, mesmo com uma política configurada, a verificação podia ser ignorada silenciosamente.

## Risco

Uma ferramenta embutida sem metadados locais de capacidade poderia executar sem passar pelo RBAC. O problema era especialmente relevante para leitura de arquivos, mas o mecanismo afetava qualquer ferramenta presente na tabela central cuja especificação omitisse o campo.

## Correção

O executor agora resolve as capacidades nesta ordem:

1. utiliza `ToolSpec.required_capabilities`, quando declarado;
2. caso a lista esteja vazia, consulta `DEFAULT_TOOL_CAPABILITIES` pelo nome da ferramenta;
3. aplica todas as capacidades encontradas à política configurada;
4. bloqueia a execução quando qualquer requisito for negado.

A ferramenta `file_read` também passou a declarar explicitamente `file:read`. A redundância é intencional: mantém a especificação autocontida e preserva uma defesa central contra omissões futuras.

## Testes adicionados

O arquivo `tests/tools/test_capability_enforcement.py` cobre:

- declaração explícita da capacidade por `FileReadTool`;
- bloqueio de uma ferramenta mapeada que omite capacidades locais;
- garantia de que a implementação da ferramenta não é chamada após negação;
- execução normal quando a política concede a capacidade;
- argumentos enviados à política: agente, capacidade e recurso.

## Compatibilidade

- Nenhuma assinatura pública foi alterada.
- Ferramentas sem política continuam com o comportamento anterior.
- Ferramentas fora da tabela e sem declaração local permanecem sem requisito implícito.
- Declarações específicas da ferramenta continuam tendo precedência sobre o fallback.

## Evidência pendente

A estrutura e os diffs foram revisados. A conclusão operacional do item depende da execução da suíte automatizada do repositório em ambiente com as dependências do Jarvis.
