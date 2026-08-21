# Jarvis - Origem, Fork e Upstream

## Origem planejada

Jarvis sera derivado do projeto OpenJarvis. O repositorio de trabalho foi referido nas fontes como `zane-jarvis-runtime`, mas o nome final do repositorio ainda deve ser confirmado no momento da criacao.

## Regras de importacao

1. Registrar URL oficial do upstream.
2. Registrar commit/tag exatos importados.
3. Preservar licenca, notices e atribuicoes.
4. Importar a arvore completa necessaria, incluindo backend, agentes, tools, CLI, API, frontend e desktop quando presentes.
5. Executar baseline antes de alterar comportamento.
6. Separar codigo upstream, adapters e extensoes proprias de forma rastreavel.
7. Evitar reescrita prematura baseada apenas no README.

## Remotes

Configuracao conceitual:

```text
origin   -> repositorio Jarvis do Projeto Meta
upstream -> repositorio oficial OpenJarvis
```

## Politica de sincronizacao

- sincronizacao nao deve ser automatica sem revisao;
- mudancas upstream passam por changelog, testes e analise de seguranca;
- conflitos em contratos, credenciais, eventos, tools e agentes exigem revisao manual;
- cada sincronizacao recebe identificador e registro de compatibilidade;
- versoes liberadas do Jarvis devem apontar para a baseline upstream correspondente.

## Estrategias de manutencao

| Estrategia | Uso |
|---|---|
| Merge upstream | preservar historico e incorporar conjuntos maiores |
| Cherry-pick | trazer correcoes isoladas |
| Reimplementacao | somente quando contrato/seguranca exigirem; manter referencia |
| Patch local | correcao temporaria com issue de acompanhamento |

## Inventario inicial obrigatorio

Antes da primeira evolucao, registrar:

- linguagens e manifests;
- agentes realmente executaveis;
- engines/providers;
- tools e skills;
- mecanismos de credenciais;
- eventos e registries;
- memoria/learning existentes;
- API e CLI;
- frontend e componentes Tauri;
- testes e cobertura;
- dependencias e vulnerabilidades;
- licenca e restricoes.

## Proibicoes

- remover autoria/licenca;
- afirmar que uma capacidade funciona sem executa-la;
- substituir o Core upstream antes de entender seus contratos;
- misturar regras especificas de Rachel no nucleo do fork;
- expor o nome OpenJarvis ao usuario final quando estiver operando como Jarvis, salvo em informacoes de licenca/diagnostico.

