# DEC-008 - A fonte documental oficial reside em docs

**Estado:** ativa  
**Data de confirmacao:** 2026-08-21

## Contexto

O material de origem esta distribuido entre PDFs, ZIPs, chats, relatorios e documentos de diferentes ciclos. O repositorio foi criado especificamente para manter documentacao organizada, sem README como documento principal.

## Decisao

Todo documento oficial do Projeto Meta deve residir abaixo de `docs/`. A raiz do repositorio nao deve conter README. Fontes brutas continuam externas ou referenciadas por inventario e nao substituem os documentos consolidados.

## Alternativas consideradas

- Usar README na raiz como portal: rejeitada por decisao do proprietario.
- Copiar integralmente todas as fontes brutas: rejeitada por duplicacao, volume, possiveis segredos e baixa confiabilidade operacional.

## Consequencias

- `docs/indice.md` e a entrada principal;
- cada projeto possui pasta propria;
- resumos ficam separados das especificacoes detalhadas;
- mudancas sao publicadas por branch e PR;
- links internos devem ser relativos e verificaveis;
- arquivos novos fora de `docs/` exigem decisao formal que substitua esta.

## Criterio de verificacao

Antes de integrar um PR documental, verificar caminhos alterados, ausencia de README e atualizacao dos indices afetados.
