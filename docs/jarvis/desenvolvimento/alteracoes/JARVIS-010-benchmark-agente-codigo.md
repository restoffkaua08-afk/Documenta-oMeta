# JARVIS-010 — benchmark isolado do agente de código

## Estado

Implementado na branch `main` do repositório `Jarvis-ia`.

## Objetivo

Criar uma prova reproduzível e independente de julgamento por LLM para verificar
o fluxo mínimo de engenharia do `jarvis code`: modificar o arquivo correto,
passar nos testes e preservar o escopo permitido.

## Implementação

Foi adicionado `src/openjarvis/cli/code_benchmark.py`, responsável por:

- criar um projeto Git descartável em diretório temporário;
- incluir uma implementação pequena com defeito intencional;
- fornecer uma tarefa objetiva ao agente;
- executar testes com `unittest`;
- consultar o estado real do Git;
- verificar se apenas `calculator.py` foi alterado;
- produzir relatório JSON estável;
- retornar aprovação somente com nota 100.

A CLI recebeu duas operações:

```powershell
jarvis code --benchmark-prepare
jarvis code --benchmark-evaluate "CAMINHO_DO_WORKSPACE"
```

A primeira cria a fixture e imprime o caminho e a tarefa. Após o agente trabalhar
nesse diretório, a segunda calcula o resultado. Uma avaliação reprovada encerra
com código de saída 1, permitindo uso em scripts e CI.

## Critérios objetivos

| Critério | Peso |
|---|---:|
| Todos os testes passam | 70 |
| Existe alteração observável | 15 |
| Todas as alterações estão dentro do escopo | 15 |

A aprovação exige 100 pontos. O benchmark não usa outro modelo para julgar a
resposta e não aceita apenas uma explicação textual como prova.

## Segurança e isolamento

- O projeto é criado em diretório temporário.
- Nenhum repositório real do usuário é usado como fixture.
- O manifesto `BENCHMARK.json` vincula a versão e o escopo permitido.
- Artefatos transitórios do Python são ignorados; outros arquivos inesperados
  continuam causando reprovação.
- O benchmark não publica, não envia e não remove dados do usuário.

## Testes adicionados

`tests/cli/test_code_benchmark.py` cobre:

1. estado inicial reprovado;
2. correção válida com nota 100 e JSON;
3. rejeição de diretório não preparado;
4. detecção de mudança fora do escopo.

## Validação realizada

Foi realizada validação comportamental local do módulo final:

- fixture original: 0 pontos, reprovada;
- correção correta apenas em `calculator.py`: 100 pontos, aprovada;
- AST Python válido;
- nenhuma linha acima de 88 caracteres nos arquivos alterados.

Durante a validação, uma versão inicial contou `__pycache__` como mudança de
escopo. O problema foi detectado, corrigido com `.gitignore` restrito a
artefatos Python e o cenário foi executado novamente com sucesso.

## Commits do código

- `f6495f74a3a93269f94db425ddb725cced586a95` — fixture e avaliador;
- `e8ccb8e250de7b828658b47bda2b5b6169d2b5f0` — integração à CLI;
- `3dc742cbb0a382ff8beca54f747e850c9d70d694` — testes;
- `78bce79e2062d9eaad9cdf888de00ef19c87ebee` — correção da fixture de teste;
- `f41397f8c5564316ed7077c1b57c1eb074231511` — conformidade de estilo;
- `9563c71ab616df7cae13df4763d1ca9723517269` — artefatos transitórios.

## Limitações conhecidas

Este benchmark prova o contrato mínimo de edição, teste e escopo. Ele ainda não
prova qualidade em projetos grandes, arquitetura, frontend ou tarefas
multiarquivo. Essas categorias precisam de uma suíte progressiva posterior.

A preparação do instalador Windows é uma etapa separada: um instalador somente
será tratado como liberável após execução numa máquina Windows e verificação do
`jarvis code --check` e desta suíte.
