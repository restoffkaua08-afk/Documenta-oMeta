# JARVIS-007 — instalação auditável de skills pelo chat

## Objetivo

Permitir que o usuário envie ao `jarvis code` um link de repositório GitHub
contendo uma ou mais skills. O agente deve acessar, analisar e instalar a skill
sem executar cegamente conteúdo externo.

## Fluxo obrigatório

1. o usuário envia uma URL pública `https://github.com/owner/repo`;
2. o agente chama `skill_repo_inspect`;
3. o repositório é sincronizado em cache, sem instalação;
4. cada `SKILL.md` é analisado;
5. o Jarvis apresenta commit, scripts, capacidades e erros de manifest;
6. o usuário escolhe a skill quando houver mais de uma;
7. `skill_repo_install` solicita confirmação interativa;
8. a importação registra proveniência e não executa scripts automaticamente.

## Ferramentas

### `skill_repo_inspect`

- aceita somente repositórios públicos no domínio `github.com` via HTTPS;
- recusa credenciais, portas, query strings, fragments e caminhos inválidos;
- identifica todas as skills do repositório;
- registra o commit Git;
- detecta diretório `scripts/`;
- valida o manifest;
- lista capacidades requeridas e capacidades classificadas como perigosas;
- não instala a skill.

### `skill_repo_install`

- exige confirmação interativa do executor;
- instala uma skill exata;
- exige `skill_name` quando o repositório contém várias;
- mantém `with_scripts = false` por padrão;
- mantém `allow_dangerous = false` por padrão;
- recusa capacidades perigosas sem autorização explícita;
- registra origem, commit, trust tier, ferramentas traduzidas e avisos;
- exige capacidades RBAC de rede, escrita e administração.

## Proteção adicional de supply chain

O importador agora recusa links simbólicos dentro de conteúdo externo antes de
copiar arquivos. Isso impede que uma skill use um symlink para fazer o
importador ler ou copiar conteúdo fora do clone analisado.

## Integração com Jarvis Code

As duas ferramentas foram adicionadas ao conjunto padrão de `jarvis code`.
O prompt especializado obriga inspeção antes da instalação e impede ativação
automática de scripts ou capacidades perigosas.

Exemplo de conversa esperada:

> Instale as skills deste repositório:
> https://github.com/example/skills

O Jarvis deve primeiro apresentar o relatório. Se houver múltiplas skills, ele
deve pedir a escolha. A instalação ocorre somente depois da confirmação no
terminal.

## Commits no Jarvis

- ferramentas de repositório:
  `d12284309c5be50c6f11fe6bd923330d762a1509`;
- registro das ferramentas:
  `4cf47ccb2a3bda8927ad3984ab007aeeb30f317f`;
- integração com `jarvis code`:
  `f645eedf1b550f1d8b4d43cfb27db15dc07e2c9b`;
- bloqueio de symlinks:
  `d01ff03d822627590052fdd7cf95ff9ad5b03e81`;
- testes das ferramentas:
  `9ce30178b103a90b232215ed84d63ff1e1f4cfc4`;
- teste de symlink:
  `a049aeeeef2f68ae4f9e28224eb185b912091b5f`.

## Validação

- sintaxe Python validada nos seis arquivos alterados;
- nenhuma linha acima de 88 caracteres;
- testes versionados para validação e normalização de URL;
- confirmação obrigatória e capacidades RBAC verificadas por testes;
- regressão de symlink coberta;
- existência e registro das ferramentas confirmados.

Ainda falta executar a suíte completa e uma instalação real controlada no
Windows. A funcionalidade não deve ser declarada como release validada antes
desse teste operacional.
