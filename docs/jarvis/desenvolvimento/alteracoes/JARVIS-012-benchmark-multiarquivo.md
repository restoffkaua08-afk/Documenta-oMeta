# JARVIS-012 — benchmark multiarquivo de engenharia

## Estado

Implementado e validado comportamentalmente na `main`.

## Objetivo

Evoluir o benchmark básico do JARVIS-010 para medir uma tarefa que exige
compreensão conjunta de contrato, implementação e testes em mais de um arquivo.

## Perfil `multifile`

O perfil cria um repositório temporário com:

- `validators.py`;
- `user_service.py`;
- testes de normalização e contrato de retorno;
- manifesto versionado com arquivos permitidos e obrigatórios.

A tarefa exige normalizar e-mail removendo espaços e convertendo para
minúsculas, além de corrigir o campo `active` para booleano. A solução coerente
precisa alterar os dois arquivos de implementação.

## Uso

```powershell
jarvis code --benchmark-prepare --benchmark-profile multifile
jarvis code --benchmark-evaluate "CAMINHO_GERADO"
```

## Avaliação determinística

| Critério | Peso |
|---|---:|
| Testes completos aprovados | 60 |
| Existe alteração | 10 |
| Alterações somente no escopo permitido | 15 |
| Todos os arquivos obrigatórios foram alterados | 15 |

A aprovação continua exigindo 100 pontos.

O relatório JSON agora informa:

- perfil executado;
- arquivos alterados;
- arquivos obrigatórios ausentes;
- estado dos testes;
- limpeza do escopo;
- nota e aprovação final.

## Compatibilidade

O manifesto foi elevado para a versão 2. Workspaces antigos do benchmark são
rejeitados explicitamente, evitando avaliar uma fixture antiga com regras novas.

## Testes adicionados

- perfil desconhecido é rejeitado;
- correção de apenas um arquivo continua reprovada;
- arquivos obrigatórios ausentes aparecem no relatório;
- correção coerente dos dois arquivos alcança 100/100.

## Validação realizada

Execução comportamental local do avaliador final:

| Cenário | Resultado |
|---|---:|
| Fixture original | 0/100 |
| Somente normalização corrigida | 25/100 |
| Normalização e contrato corrigidos | 100/100 |

Também foram validados:

- AST Python dos módulos e testes alterados;
- ausência de linhas acima de 88 caracteres;
- escopo final exatamente `user_service.py` e `validators.py`.

## Commits

- `95f0f53f41539ce82d230598895de216bf40e56c` — perfil multiarquivo;
- `1c2ed83af41402887f9c82c444d965d13c2d6ddb` — seleção pela CLI;
- `5320ce385bd9a403cd9e22e4087c0b222b75e4e7` — cobertura de testes.

## Limitações

O perfil verifica resultado e disciplina de escopo, não a qualidade textual do
raciocínio interno. Também não cobre ainda frontend, banco de dados,
concorrência, segurança ofensiva ou projetos grandes. Novos perfis devem ser
adicionados progressivamente, sem transformar uma única nota em alegação de
competência universal.
