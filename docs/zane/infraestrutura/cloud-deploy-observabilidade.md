# Zane - Cloud, Deploy e Observabilidade

## Componentes sugeridos

| Responsabilidade | Opcao documentada |
|---|---|
| Web | Vercel |
| API/workers | Railway ou infraestrutura container |
| Edge/rate limit | Cloudflare |
| Banco/auth/vector | Supabase/PostgreSQL/pgvector |
| Cache/filas | Redis ou alternativa |
| Observabilidade | OpenTelemetry + Sentry/stack equivalente |

Todas as opcoes sao substituiveis.

## Ambientes

Local, development, staging e production possuem contas, secrets, bancos e quotas separados. Dados reais nao devem ser copiados indiscriminadamente para dev.

## CI/CD

- lint/typecheck/test;
- security/dependency scan;
- migrations dry-run;
- builds web/desktop/backend;
- artifacts assinados;
- deploy de preview/staging;
- smoke/E2E;
- promocao e rollback.

## Observabilidade

Traces correlacionam cliente, API, planner, modelo, tool, worker e banco. Metricas incluem latencia, TTFT, erros, tool success, approvals, filas, custo e uso de fallback.

## Logs

Estruturados, com redacao e retencao. Prompt/conteudo completo nao e logado por padrao.

## Custos

O planejamento historico usa teto inicial aproximado de R$100/mes em producao. Esse valor e uma meta, nao garantia. Budgets, alertas e quotas devem impedir surpresa.

## Resiliencia

- health/readiness;
- retries com idempotencia;
- circuit breakers;
- filas duraveis;
- backup/restore;
- deploy gradual;
- runbooks e incidentes.

## Regra de provisionamento

Infraestrutura paga entra somente depois dos gates locais e de deployment readiness. Codigo, migrations, IaC/config e testes devem existir antes.

