# Governanca Documental

## Fonte oficial

O diretorio `docs/` deste repositorio e a fonte oficial consolidada. Chats, PDFs, ZIPs e relatorios externos sao fontes de entrada e evidencia historica, nao a versao final por si so.

## Tipos de documento

| Tipo | Finalidade | Local sugerido |
|---|---|---|
| Visao | explicar produto e fronteiras | pasta do projeto |
| Especificacao | definir comportamento e contratos | `arquitetura/` ou `especificacoes/` |
| Roadmap | ordenar trabalho e gates | `desenvolvimento/` |
| Status | registrar estado atual | `status/` |
| Decisao | explicar escolha e consequencias | `decisoes/` |
| Evidencia | registrar testes, commits e builds | `evidencias/` |
| Resumo | leitura acessivel ao proprietario | `resumos/` |
| Historico | preservar material superado | `historico/` |

## Metadados obrigatorios

Documentos sensiveis a versao devem informar estado, data, escopo e fonte. Decisoes devem adicionar contexto, alternativas, consequencias e relacoes.

## Processo de alteracao

1. Identificar a mudanca real.
2. Atualizar a especificacao afetada.
3. Atualizar status e proxima acao.
4. Criar ou alterar a decisao quando houver escolha arquitetural.
5. Vincular evidencia tecnica.
6. Marcar a orientacao anterior como superada quando necessario.
7. Revisar links e contradicoes.
8. Publicar por branch e PR.

## Linguagem normativa

- `deve`: requisito obrigatorio;
- `nao deve`: proibicao;
- `pode`: opcao permitida;
- `recomenda-se`: preferencia nao obrigatoria;
- `planejado`: ainda nao implementado;
- `validado`: possui evidencia suficiente para o escopo declarado.

## Politica contra perda de contexto

Nenhuma decisao relevante deve existir somente em conversa. Ao final de uma sessao que altere arquitetura, escopo, prioridade, seguranca ou estado de etapa, a documentacao correspondente deve ser atualizada.

## Politica contra excesso de certeza

Quando fontes divergem, o documento deve expor a divergencia e escolher uma das classificacoes: a confirmar, superada ou decidida. Nunca combinar duas versoes incompatíveis em uma unica afirmacao.

