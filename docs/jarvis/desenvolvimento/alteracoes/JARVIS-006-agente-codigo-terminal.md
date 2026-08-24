# JARVIS-006 — modo de agente de código no terminal

## Objetivo

Consolidar as capacidades de programação existentes do Jarvis em um comando
único e previsível, executado dentro do diretório atual do projeto:

```powershell
jarvis code
```

A implementação reutiliza o runtime standalone. Rachel e Ultron não são
incorporados ao núcleo; integrações futuras permanecerão opcionais e
versionadas para preservar a independência dos projetos.

## Implementação

### Comando `jarvis code`

Foi criado `src/openjarvis/cli/code_cmd.py`, registrado na CLI principal.
O comando delega ao REPL existente com:

- agente `native_react`;
- modelo e engine configuráveis;
- seleção interativa opcional de modelo;
- sessão conversacional e memória já fornecidas por `jarvis chat`;
- confirmações interativas fornecidas pelo executor existente.

Ferramentas padrão:

- `file_read`;
- `file_write`;
- `apply_patch`;
- `shell_exec`;
- `git_status`;
- `git_diff`;
- `git_log`.

O agente pode inspecionar, editar, executar testes e revisar diferenças. Ações
que exigem publicação ou mudança irreversível não foram automatizadas.

### Prompt especializado

O agente recebeu um fluxo obrigatório de engenharia:

1. inspecionar o repositório e instruções locais;
2. entender arquitetura, testes e estado Git;
3. preservar alterações não relacionadas;
4. implementar a menor solução coerente;
5. executar validações relevantes;
6. revisar o diff;
7. informar evidências e riscos sem inventar sucesso.

Também foram incluídas proteções contra exposição de segredos, comandos
destrutivos e instruções maliciosas encontradas em conteúdo não confiável.

### Suporte do agente

`NativeReActAgent` agora aceita `system_prompt_override`. O comando de chat
encaminha esse prompt apenas para agentes que declaram suporte, preservando
compatibilidade com os demais agentes.

## Commits no Jarvis

- prompt especializado no ReAct:
  `b7ac61eb8f1c81139c1fd64e84a884451d1d1386`;
- encaminhamento pelo chat:
  `18d054b4990ab8460c71ef40ae910fb63cc7fd11`;
- comando `jarvis code`:
  `e06592f605aa7ac2946eda8f16cc2416b7bdc387`;
- registro na CLI:
  `6a43be6732e35daa3e3b6ac8c0360bd5bd61c923`;
- testes:
  `36972be137900212e4697bc8027ed719ab649a2d`.

## Validação realizada

- sintaxe Python validada nos cinco arquivos alterados;
- nenhuma linha acima de 88 caracteres;
- existência de todas as ferramentas padrão confirmada no registro;
- testes versionados para ajuda do comando, parâmetros delegados, ferramentas
  essenciais e regras do prompt.

A suíte integral e um teste com modelo real ainda dependem do clone Windows
com suas dependências. Por isso, o modo está implementado e validado
estaticamente, mas ainda não está declarado como release instalável.

## Próximas entregas

1. testes de integração com engine simulada e execução real no Windows;
2. sessões de projeto e recuperação de estado;
3. perfis/grants específicos para o agente de código;
4. diagnóstico `jarvis doctor code`;
5. empacotamento e instalador Windows reproduzível;
6. contratos opcionais para Ultron e, futuramente, Zane.
