# Correção JARVIS-001 — resposta final após limite de turnos

## Registro

- Data: 2026-08-24
- Repositório alterado: `restoffkaua08-afk/Jarvis-ia`
- Branch: `main`
- Código: commit `9fa74fb4c2f972f75e4c4c321daf5ba3e0adc821`
- Teste: commit `3e4ee53df937995f17c2f4bace207b253844e5dd`
- Componente: `OrchestratorAgent`
- Arquivos:
  - `src/openjarvis/agents/orchestrator.py`
  - `tests/agents/test_orchestrator.py`

## Problema observado

Ao atingir `max_turns`, o agente encerrava o fluxo imediatamente. Quando a última geração continha apenas uma chamada de ferramenta e nenhum texto, o usuário recebia a mensagem interna `Maximum turns reached without a final answer.`, mesmo que as ferramentas já tivessem produzido informações suficientes para uma resposta útil.

Esse comportamento foi reproduzido na interface durante o baseline com `qwen3.5:2b`.

## Decisão

O limite de chamadas de ferramentas permanece obrigatório para impedir loops. Após esgotá-lo, o orquestrador agora realiza exatamente uma tentativa adicional de síntese sem disponibilizar ferramentas ao modelo.

A instrução final exige:

- não chamar ferramentas;
- usar somente informações já presentes na conversa;
- produzir a melhor resposta completa possível;
- declarar explicitamente o que não pôde ser verificado.

## Comportamento implementado

1. O loop normal permanece limitado por `max_turns`.
2. Ao esgotar o limite, é adicionada uma instrução de finalização.
3. `_generate()` é chamado sem o argumento `tools`.
4. Tokens da finalização são contabilizados.
5. Exceções da tentativa final não apagam o resultado original.
6. Metadados novos:
   - `finalization_attempted`;
   - `finalization_succeeded`;
   - `finalization_error`, somente quando houver exceção.
7. Se a síntese continuar vazia, o fallback anterior é preservado.

## Teste automatizado

O teste `test_max_turns_attempts_tools_disabled_finalization` verifica:

- duas iterações de ferramenta com `max_turns=2`;
- terceira geração dedicada à resposta final;
- conteúdo final devolvido ao usuário;
- preservação de `max_turns_exceeded=true`;
- sucesso registrado nos metadados;
- ausência de `tools` na chamada final.

## Compatibilidade e riscos

A assinatura pública do agente e o formato `AgentResult` foram preservados. O comportamento adiciona no máximo uma inferência quando o limite é atingido. Isso aumenta o custo somente em um caminho excepcional e reduz encerramentos sem resposta.

A alteração ainda depende da execução do pipeline completo de CI e de validação real posterior com um modelo local.
