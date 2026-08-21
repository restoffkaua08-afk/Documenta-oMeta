# Metodo de Rastreabilidade Documental

## Objetivo

Permitir que cada afirmacao importante seja ligada a sua origem e ao nivel real de confianca, sem transformar material historico em verdade operacional.

## Identificacao de fontes

| Prefixo | Tipo |
|---|---|
| `SRC-CHAT` | conversa ou planejamento bruto |
| `SRC-PDF` | exportacao historica em PDF |
| `SRC-DOC` | documento tecnico ou de aceite |
| `SRC-REP` | relatorio de execucao ou auditoria |
| `SRC-CODE` | repositorio, arquivo ou branch |
| `SRC-COMMIT` | commit ou tag |
| `SRC-TEST` | saida de teste, build ou benchmark |
| `SRC-OWNER` | decisao explicita do proprietario |

## Registro minimo

Uma evidencia deve registrar, quando disponivel:

- identificador estavel;
- projeto e escopo;
- origem e nome do artefato;
- data de observacao;
- branch, commit ou versao;
- afirmacao sustentada;
- resultado verificavel;
- limitacoes;
- documento que a utiliza.

## Nivel de confianca

| Nivel | Definicao |
|---|---|
| Confirmado | evidencia primaria reproduzivel ou decisao atual explicita |
| Parcial | evidencia existe, mas nao cobre todo o gate |
| Reportado | documento afirma execucao sem validacao independente suficiente |
| Planejado | intencao, especificacao ou roadmap |
| Superado | material preservado que nao orienta mais a implementacao |
| A confirmar | contradicao, ausencia de identificador ou fonte insuficiente |

## Resolucao de conflito

1. Verificar se as fontes tratam da mesma branch, versao e etapa.
2. Preferir codigo e testes atuais a narrativas anteriores.
3. Preferir decisao recente explicita a arquitetura antiga.
4. Preservar a versao anterior como superada.
5. Registrar a pendencia quando nao houver evidencia para resolver o conflito.

## Regra para status

Nenhum status deve subir de `reportado` para `validado` apenas por repeticao em varios documentos derivados da mesma fonte. Evidencias independentes ou reproduziveis sao necessarias.
