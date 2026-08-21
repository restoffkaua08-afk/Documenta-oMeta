# Zane - Desktop, Web e Mobile

## Design system

Tokens, componentes, estados, acessibilidade e linguagem sao compartilhados. Cada cliente adapta interacao sem divergir nos contratos.

## Desktop

Tauri e a direcao sugerida. Responsabilidades:

- runtime/local sidecar;
- filesystem e sistema operacional;
- tray, notificacoes e auto-update;
- vault de credenciais;
- chat, planos, approvals, diffs e jobs;
- modo offline/local-first.

## Web

Cliente para conversa, sincronizacao, jobs cloud e administracao autorizada. Next.js/React foi sugerido nas fontes, mas deve ser confirmado no bootstrap.

## Mobile

Fase futura. Inicialmente pode priorizar chat, notificacoes, approvals e acompanhamento de jobs. Capacidades de dispositivo exigem contratos proprios.

## Contrato de streaming

SSE ou WebSocket conforme bidirecionalidade. Eventos possuem sequencia, request/job ID, tipo e payload. Reconexao nao duplica efeitos.

## Estados obrigatorios

- offline/conectando/pronto;
- pensando/planejando;
- aguardando aprovacao;
- executando/verificando;
- pausado/cancelado/falhou/concluido;
- degradacao de memoria/provider/tool.

## Acessibilidade

Teclado, foco, contraste, leitores de tela, reducao de movimento, mensagens nao dependentes apenas de cor e layouts responsivos.

## Auto-update

Assinatura, canal, rollback, changelog e compatibilidade de schema. Update nao pode migrar dados sem backup/plano.

