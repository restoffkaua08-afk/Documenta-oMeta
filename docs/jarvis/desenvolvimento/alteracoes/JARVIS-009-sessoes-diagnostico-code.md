# JARVIS-009 — sessões por projeto e diagnóstico Code

## Objetivo

Permitir continuidade segura entre sessões do terminal e detectar problemas
operacionais antes de iniciar uma tarefa de programação.

## Sessões persistentes

Por padrão, `jarvis code` associa a conversa ao diretório atual. O caminho
canônico do projeto é convertido em SHA-256 e usado para determinar um arquivo
privado em:

```text
<OPENJARVIS_HOME>/code-sessions/<project-hash>.json
```

Comandos:

```powershell
jarvis code
jarvis code --new-session
jarvis code --no-resume
```

- padrão: restaura e salva a sessão do projeto;
- `--new-session`: remove somente a sessão do projeto atual;
- `--no-resume`: inicia sem ler ou salvar histórico.

### Proteções

- gravação JSON atômica;
- permissões de proprietário fornecidas por `secure_write_json`;
- versão explícita do formato;
- validação do caminho canônico do projeto;
- recusa de sessão pertencente a outro projeto;
- limite de 200 mensagens;
- limite total de 2 MiB;
- apenas mensagens de usuário e assistente são persistidas;
- o system prompt é reconstruído da versão atual em cada execução.

As sessões podem conter código e conteúdo conversacional sensível. Elas não
substituem um cofre de segredos, e credenciais não devem ser enviadas ao modelo.

## Diagnóstico

O comando abaixo executa preflight sem iniciar o modelo:

```powershell
jarvis code --check
```

Ele verifica:

- diretório atual;
- disponibilidade do Git;
- presença de repositório Git;
- configuração de `model_code` ou fallback;
- registro de todas as ferramentas obrigatórias;
- resolução e saúde da engine.

Qualquer falha produz exit code 1. Avisos informam configurações utilizáveis,
mas inferiores ao estado recomendado.

## Commits no Jarvis

- armazenamento de sessões:
  `e4d863148b8be09716ef46b80e1be9740f3f4834`;
- integração no chat:
  `ec407c97246cad5da368957243e4ff7bab4b8f40`;
- opções do modo Code:
  `951983d872c9d18f9be445ac76790e5429266471`;
- diagnóstico:
  `fd51a0008a7612c62d78a8252ea9b2986212b141`;
- exposição de `--check`:
  `310bdcdb5f379f400af9c881bcb30e6d680a9a1a`;
- testes das opções:
  `c7dedee6ce0387f00becc2db5dd24ecf981313de`;
- testes das sessões:
  `95be61ac4ab36df5c095ce4fd485bb582abbbce9`;
- testes do diagnóstico:
  `4078ba8494a859fbac3b3aac23a08fa720b1a737`.

## Validação

- sintaxe Python validada nos sete arquivos alterados;
- nenhuma linha acima de 88 caracteres;
- testes cobrem persistência, separação entre projetos, versão inválida e
  limpeza;
- testes cobrem modelo especializado, fallback, ferramentas e engine;
- opções do comando cobertas.

A execução integral desses testes e o preflight real no Windows continuam
pendentes antes da release instalável.
