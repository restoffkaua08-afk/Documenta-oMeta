# Zane - Seguranca, Governanca e Multi-tenant

## Modelo de seguranca

Zane combina politica local, autorizacao de usuario e isolamento cloud. Nenhuma interface ou extensao contorna o Policy Engine.

## Niveis de efeito

- leitura interna;
- leitura pessoal/externa;
- mutacao local;
- efeito remoto/critico.

Cada ToolSpec declara efeito. A politica decide aprovacao, bloqueio, escopo e redacao.

## Approvals

Aprovacao referencia plano, tool, argumentos, alvo, impacto e expiracao. Mudanca de plano exige nova autorizacao. Uso unico por padrao.

## Multi-tenant

- tenant/user IDs validados no servidor;
- RLS e testes negativos;
- storage separado/logico;
- caches com namespace;
- jobs preservam escopo;
- administradores nao recebem acesso implicito ao conteudo;
- logs evitam dados sensiveis.

## Dispositivos

Registro, revogacao, chaves por dispositivo, sessoes e sincronizacao. Dispositivo comprometido pode ser removido sem invalidar toda a conta quando a arquitetura permitir.

## Prompt injection

Dados de web, documentos, repositorios e ULTRON sao nao confiaveis. Instrucoes externas nao alteram policy nem solicitam segredos.

## Segredos

Local: vault do sistema. Cloud: secret manager. Modelos recebem handles/resultados, nao credenciais brutas.

## Kill switch

Permite bloquear tools, providers, extensoes, jobs e execucao remota. A acao e auditada e reversivel por autoridade apropriada.

## Gates

- testes de isolamento entre ao menos tres tenants;
- IDOR e escalation;
- alteracao de argumentos apos approval;
- exfiltracao/prompt injection;
- secrets scanning;
- dependency/supply chain;
- backup/restore e incident response.

