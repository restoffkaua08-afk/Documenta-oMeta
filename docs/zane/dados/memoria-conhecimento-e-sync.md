# Zane - Memoria, Conhecimento e Sincronizacao

## Memoria da aplicacao

Armazena sessoes, fatos, preferencias, decisoes, projetos e estado operacional com proveniencia, escopo e retencao.

## Conhecimento

Indexa notas, arquivos e documentos. Resultados incluem fonte, data, trecho e score. Obsidian pode ser uma fonte local, nao o banco interno obrigatorio.

## Storage sugerido

- SQLite/local storage para modo offline;
- PostgreSQL/Supabase como opcao cloud;
- pgvector ou alternativa para embeddings;
- object storage para documentos/backups;
- Redis apenas para cache, filas e estado temporario.

Tecnologias sao substituiveis por contratos.

## Sync

Sincronizacao deve lidar com:

- identificadores globais;
- versao e timestamps;
- conflitos;
- operacoes offline;
- tombstones/exclusao;
- criptografia e transporte;
- retry e idempotencia;
- dispositivos revogados.

## Multi-tenant

Cada consulta inclui tenant/user/project conforme aplicavel. RLS no banco cloud e defesa adicional, nao substituto da autorizacao na API.

## Privacidade

- minimizacao de contexto;
- consentimento para cloud;
- redacao de segredos;
- exportacao e exclusao;
- retencao configuravel;
- memoria separada de dataset;
- embeddings tratados como dados.

## Recuperacao

Backups precisam de teste de restauracao, versao de schema e criptografia. Sync nao e backup por si so.

