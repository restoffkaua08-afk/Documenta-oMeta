# JARVIS-013 — gate obrigatório de inspeção antes da edição

## Estado

Implementado na `main` e validado estaticamente.

## Problema identificado

O gate de qualidade do JARVIS-008 exigia testes e revisão do diff após uma
alteração, mas não impedia que o agente executasse `file_write` ou
`apply_patch` antes de conhecer os arquivos e o estado do repositório.

Isso permitia um fluxo tecnicamente validado no final, porém mais propenso a
sobrescrever decisões existentes, ignorar mudanças do usuário ou editar o
arquivo errado.

## Solução

O `NativeReActAgent`, quando iniciado com `quality_gate=True`, agora exige
antes da primeira mutação bem-sucedida:

1. pelo menos um `file_read` bem-sucedido;
2. pelo menos um `git_status` ou `git_diff` bem-sucedido.

As mutações controladas são:

- `file_write`;
- `apply_patch`.

Se o modelo tentar editar antes dessas inspeções:

- a ferramenta de escrita não é executada;
- o resultado é registrado como falha;
- a observação informa exatamente quais inspeções faltam;
- o modelo pode executar as inspeções e tentar novamente.

## Fluxo obrigatório

```text
file_read
    ↓
git_status ou git_diff
    ↓
file_write ou apply_patch
    ↓
shell_exec após a última edição
    ↓
git_diff após a última edição
    ↓
resposta final
```

O gate não é aplicado a conversas explicativas sem mutação.

## Testes

A suíte `tests/agents/test_native_react_quality.py` foi atualizada para:

- executar o fluxo completo de inspeção, edição, teste e revisão;
- preservar o comportamento de respostas somente consultivas;
- confirmar metadados de verificação incompleta no fim do orçamento;
- tentar uma edição prematura e confirmar que ela é bloqueada;
- confirmar que a segunda tentativa é permitida após as inspeções;
- confirmar aprovação final somente depois dos testes e do diff.

## Validação realizada

- AST válido no agente e na suíte alterada;
- nenhuma linha acima de 88 caracteres;
- revisão da sequência simulada de sete turnos;
- verificação de que a tentativa bloqueada permanece no histórico como falha.

A suíte completa ainda precisa ser executada pelo CI ou pelo ambiente local com
as dependências de desenvolvimento instaladas. Validação estática não é
registrada como equivalente a execução do pytest.

## Commits

- `f622bb783d7005596bc4f5dfcbbd032f335f449c` — gate de pré-inspeção;
- `29bdc56f5dc50c263915fabf554753ea04ed6bb7` — atualização e expansão dos testes.

## Impacto arquitetural

O comportamento é ativado apenas nas sessões que solicitam o gate, incluindo
`jarvis code`. Outros agentes e chats gerais continuam compatíveis.

A mudança reforça a governança que será necessária no Zane: primeiro observar,
depois alterar, e somente declarar conclusão com evidências posteriores.
