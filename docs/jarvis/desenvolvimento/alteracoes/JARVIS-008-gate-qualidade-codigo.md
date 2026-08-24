# JARVIS-008 — roteamento de código e gate de qualidade

## Objetivo

Aumentar a confiabilidade do agente de programação com controles executáveis,
não apenas instruções textuais. O Jarvis deve usar o modelo configurado para
código, possuir orçamento suficiente para tarefas reais e verificar mudanças
antes de encerrar.

## Alterações

### Roteamento para `model_code`

O chat recebeu um parâmetro interno de variante de modelo. `jarvis code`
envia `model_variant = "code"`, fazendo a resolução obedecer à ordem:

1. modelo informado em `--model`;
2. `[intelligence].model_code`;
3. `[intelligence].default_model`;
4. primeiro modelo descoberto pela engine.

Isso impede que o modo de programação use acidentalmente o preset de conversa
quando existe um modelo especializado configurado.

### Orçamento de turnos

`jarvis code` usa 30 turnos por padrão, configuráveis entre 5 e 100:

```powershell
jarvis code --max-turns 40
```

O limite continua existindo para impedir loops e consumo ilimitado. Mais turnos
não significam automaticamente melhor raciocínio; eles apenas permitem que um
bom modelo conclua inspeção, edição, testes e revisão.

### Gate pós-edição

Quando o agente usa `file_write` ou `apply_patch` com sucesso, uma resposta
final somente é aceita após ações bem-sucedidas posteriores de:

- `shell_exec`, para testes, build ou validações aplicáveis;
- `git_diff`, para revisão das alterações resultantes.

Se o modelo tentar encerrar antes, o runtime injeta uma pendência objetiva e
continua o loop. Se o orçamento terminar, o resultado registra:

- `quality_gate_passed = false`;
- lista `quality_gate_missing`.

Tarefas somente de explicação não são forçadas a executar ferramentas.

## Limites honestos

O gate prova que houve uma execução de shell e uma revisão de diff após a
última edição. Ele não prova sozinho que:

- o comando escolhido era o teste correto;
- a cobertura é suficiente;
- o sistema atende todos os requisitos;
- o modelo compreendeu corretamente o domínio;
- o software funciona em outro sistema operacional.

Esses pontos exigem benchmarks de tarefas reais, suítes específicas de cada
projeto e validação operacional no computador de destino.

## Commits no Jarvis

- gate de verificação:
  `73ca22d85967e07fc35bd64da8c06b4dc1821bcb`;
- roteamento e orçamento no chat:
  `d39a967d31219c6e8fc0053657fbfe0ede9dbf87`;
- configuração do modo Code:
  `cf25c1bbb3f145603a11d1cd59383986d90b5ad5`;
- testes de parâmetros:
  `a0a84eb56ee8f75b6aaaa347a804d2b5cdb3e718`;
- testes do gate:
  `f0c7cecbcb512f65013f18ab019e61491018a00e`.

## Validação realizada

- sintaxe Python validada nos cinco arquivos alterados;
- nenhuma linha acima de 88 caracteres;
- teste para impedir conclusão prematura após edição;
- teste para permitir tarefas consultivas sem ferramentas;
- teste para registrar gate incompleto quando o orçamento termina;
- teste para roteamento `model_code`, 30 turnos e gate ativo.

A suíte completa e benchmarks com modelos reais ainda são pendências do gate
`JARVIS_STANDALONE_READY`.
