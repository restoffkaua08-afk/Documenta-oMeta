# Arquitetura Alvo do Ecossistema

## Evolucao

```text
Rachel standalone ----\
                       > consolidacao + redesign + evolucao --> Zane
Jarvis standalone ----/                                      |
                                                              | capacidades opcionais
                                                              v
                                                           ULTRON
```

## Fase de construcao

Rachel e Jarvis possuem identidades, repositorios, instalacao, testes e gates independentes. Essa separacao permite avaliar o que cada sistema realmente entrega antes da consolidacao.

## Fase de consolidacao

O desenvolvimento do Zane deve:

- inventariar capacidades comprovadas dos predecessores;
- selecionar contratos que merecem ser preservados;
- remover duplicacoes e acoplamentos desnecessarios;
- unificar configuracao, identidade, runtime e experiencia;
- melhorar as limitacoes descobertas durante os projetos standalone;
- validar que o Zane inicializa e opera sem os repositorios Rachel e Jarvis.

## Fase operacional

| Componente | Obrigatorio | Responsabilidade |
|---|---:|---|
| Zane | Sim | Produto principal completo e independente |
| ULTRON | Nao | Instalacao e distribuicao de capacidades adicionais |
| Rachel | Nao | Predecessora e referencia tecnica |
| Jarvis | Nao | Predecessor e referencia tecnica |

## Regra de independencia

Nenhum componente externo deve ser confundido com a identidade do produto. Modelos, providers, bancos, runtimes e plataformas de hospedagem devem poder ser substituidos por adapters e contratos estaveis.

