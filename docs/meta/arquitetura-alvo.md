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

## Planos logicos do produto final

### Control Plane

Responsavel por identidade da conta, dispositivos, politicas, permissoes, auditoria, configuracao, custos, kill switch e administracao.

### Execution Plane

Responsavel por sessoes, roteamento, planejamento, modelos, agentes, tools, skills, MCP, jobs e cancelamento.

### Data Plane

Responsavel por memoria, conhecimento, embeddings, arquivos, sincronizacao, backups, retencao e dados de auditoria.

Os tres planos sao fronteiras logicas. Eles podem compartilhar processo em desenvolvimento e ser separados em producao, desde que os contratos sejam preservados.

## Fluxo de uma solicitacao

```text
Cliente
  -> autenticacao e contexto
  -> classificacao da intencao
  -> fast path de conversa OU planejamento
  -> avaliacao de risco
  -> aprovacao quando necessaria
  -> execucao de tools/modelo
  -> verificacao do resultado
  -> resposta e eventos
  -> memoria e auditoria filtradas
```

## Modos de implantacao

| Modo | Execucao | Dados | Uso esperado |
|---|---|---|---|
| Local standalone | dispositivo do usuario | local | privacidade, baixa dependencia externa |
| Hibrido | local + servicos opcionais | local + sync autorizado | uso principal multi-dispositivo |
| Cloud | servicos remotos | tenant isolado | web, jobs longos e modelos maiores |

## Falhas e degradacao

- provider indisponivel: selecionar fallback permitido ou informar indisponibilidade;
- cloud indisponivel: preservar operacao local e enfileirar sync com limites;
- ULTRON indisponivel: manter capacidades nativas;
- tool falha: registrar resultado real e nunca sintetizar sucesso;
- memoria indisponivel: continuar somente se a politica permitir e sinalizar degradacao;
- perda de conexao do cliente: permitir cancelamento, retomada segura ou encerramento determinado.

