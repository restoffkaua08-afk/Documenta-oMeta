# Plataforma cloud, identidade e integração universal

Status: aprovado para implementação incremental em 2026-08-25.

## Decisões

- portal Next.js hospedado na Vercel;
- núcleo FastAPI publicado com Vercel Functions;
- Supabase Postgres, Auth, Storage e Realtime no modo cloud;
- SQLite e package store local preservados para operação offline;
- entrada obrigatória por conta ULTRON, inicialmente com GitHub OAuth via Supabase Auth;
- GitHub App separada para acesso opcional e granular a repositórios;
- RLS e isolamento por organização em toda tabela exposta;
- REST versionado e MCP remoto OAuth 2.1 para Claude, Codex/ChatGPT, Zane e outras IAs;
- nenhuma IA recebe integração privilegiada;
- grafo visual vivo como projeção do estado operacional real.

## Interface

A direção visual oficial é escura, técnica e limpa, com painéis de vidro fosco,
movimento flutuante sutil, clusters físicos e profundidade 2.5D. O grafo representa
agents, skills, tools, MCPs, consumers, packs e workflows. Instalações, dependências,
grants, falhas e remoções precisam alterar a visualização a partir de eventos reais.

A implementação deverá possuir redução de movimento, navegação por teclado,
níveis de detalhe e renderização Canvas/WebGL para redes grandes.

## Segurança

Login social identifica o usuário, mas não concede permissões de capability nem
acesso automático a repositórios. Instalar continua separado de ativar e conceder
grants. Tokens de provider, GitHub App e service role nunca são enviados ao cliente.

## Evidência

Contratos técnicos publicados no ULTRON no commit `8351589`. O núcleo continua
com 126 testes aprovados, lint, formatação, tipagem e build verdes.


## Preparação executável — etapa 1 (`0c57ab4`)

- blueprint Supabase multi-tenant criado;
- RLS habilitada em todas as tabelas expostas;
- papéis owner/admin/developer/viewer aplicados nas policies de escrita;
- bootstrap de profile e organização pessoal preparado para o primeiro login;
- consumers, grants, installations e auditoria modelados;
- arquivo de variáveis sem secrets e readiness cloud implementados;
- 130 testes aprovados, 90,44% de cobertura e build verde.

O schema ainda não foi aplicado: ele deverá ser promovido a migration em uma branch
Supabase, receber testes negativos de isolamento e passar pelos advisors antes da produção.
