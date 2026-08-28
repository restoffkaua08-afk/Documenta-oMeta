# ULTRON — U6 Fundacao Cloud Real

**Gate:** `ULTRON_CLOUD_DATA_READY`  
**Estado:** **EM ANDAMENTO — gate fechado**  
**Inicio:** 2026-08-28  
**Baseline de entrada:** `a34aeaf`  
**Ultimo commit de codigo publicado:** `45b82fa79ae8416ff3d62390fd6ff412be29fad2`

## 1. Objetivo

U6 transforma os blueprints locais em uma fundacao PostgreSQL/Supabase real,
reproduzivel, isolada por organizacao e recuperavel. Esta etapa nao altera a
fronteira do produto:

> Ultron cataloga, valida, protege e distribui. O consumer decide, autoriza e
> executa.

O banco cloud persiste catalogo, estado de lifecycle, grafo, auditoria e
revogacoes. Ele nao recebe autoridade para executar capabilities, agents,
skills, workflows ou packs.

## 2. Incremento U6.1 entregue

O primeiro incremento endureceu os candidatos de schema antes de qualquer
aplicacao em infraestrutura externa:

- revogacao dos privilegios padrao de tabelas, sequencias e funcoes no schema
  `public`;
- grants explicitos e especificos por tabela, separados para `anon`,
  `authenticated` e `service_role`;
- ator de auditoria ligado ao consumer da mesma organizacao por foreign key
  composta;
- owner de record ligado ao consumer da mesma organizacao;
- identidade unica do record em `organization_id + namespace + id`;
- lineage com foreign keys compostas para impedir arestas entre organizacoes
  ou namespaces;
- RLS obrigatoria para todas as tabelas expostas;
- seis testes de contrato dedicados aos limites multi-tenant e privilegios.

Arquivos principais no repositorio do Ultron:

- `supabase/schema.sql`;
- `supabase/u4-data-graph.sql`;
- `supabase/README.md`;
- `tests/contract/test_supabase_u6_foundation.py`;
- `docs/gates/U6-cloud-foundation.md`.

## 3. Evidencias reproduzidas

| Gate tecnico | Resultado |
|---|---|
| Suite completa | **214 testes aprovados** |
| Cobertura | **91,66%**; minimo exigido 85% |
| Ruff lint | Aprovado |
| Ruff format | Aprovado |
| MyPy strict | Aprovado em 33 arquivos de fonte |
| Build | Wheel e source distribution aprovados |
| Contratos U6 | 6 testes novos aprovados |

Os testes sao evidencia do desenho local. Eles nao substituem testes executados
no PostgreSQL remoto.

## 4. Estado da infraestrutura

Na sessao de 2026-08-28 havia apenas um projeto Supabase visivel, generico,
inativo e sem identificacao como ambiente do Ultron. Ele foi preservado sem
alteracoes. Nenhuma migration, policy, grant ou dado foi aplicado por
suposicao.

O Supabase CLI tambem nao estava disponivel no ambiente. Como migrations
oficiais devem ser geradas pelo fluxo do CLI, nenhum nome de migration foi
inventado e os arquivos SQL permanecem explicitamente classificados como
**candidatos de schema**, nao migrations oficiais.

## 5. Subetapas fechadas de U6

| Subetapa | Dependencia | Estado | Criterio de aceite |
|---|---|---|---|
| U6.1 Hardening local | U5 | **Concluida** | contratos multi-tenant, grants minimos e suite integral verdes |
| U6.2 Projeto de desenvolvimento | acesso Supabase dedicado | Bloqueada | projeto identificado como Ultron, regiao/plano registrados e segredos fora do Git |
| U6.3 Migrations oficiais | U6.2 + Supabase CLI | Pendente | migrations criadas pelo CLI, idempotentes e aplicadas em banco vazio |
| U6.4 Upgrade e dados | U6.3 | Pendente | upgrade de versao anterior e estrategia SQLite→Postgres testados |
| U6.5 RLS real | U6.3 + identidades de teste | Pendente | duas organizacoes provam isolamento positivo e negativo no Postgres |
| U6.6 Performance | U6.5 | Pendente | indices, planos, pooling, timeouts e Advisors sem achado critico |
| U6.7 Recuperacao | U6.3 | Pendente | backup/PITR aplicavel ao plano e restauracao consistente ensaiada |
| U6.8 Fechamento | U6.4–U6.7 | Pendente | evidencias publicadas, rollback aprovado e gate formalmente aberto |

## 6. Dependencias e decisoes

- U7 depende da persistencia e das fronteiras de tenant de U6.
- U8 pode continuar em modelagem local, mas nao recebe declaracao cloud antes
  de U6.
- nenhum consumer depende da disponibilidade do Ultron para executar suas
  capacidades nativas;
- `service_role` fica restrita a processos confiaveis e nunca e entregue ao
  browser ou a um consumer;
- grants e RLS sao camadas distintas e ambas sao obrigatorias;
- todas as operacoes administrativas devem produzir auditoria correlacionada;
- migrations destrutivas exigem plano de rollback ou restauracao comprovado.

## 7. Testes obrigatorios restantes

1. aplicar todas as migrations em banco vazio;
2. reaplicar sem divergencia e atualizar uma baseline anterior;
3. testar CRUD com duas organizacoes e dois namespaces;
4. tentar forjar `organization_id`, owner e extremos de lineage;
5. provar que `anon` e `authenticated` nao recebem privilegios excedentes;
6. provar que o `service_role` permanece somente no backend;
7. executar Advisors de seguranca e performance;
8. medir consultas criticas com volume representativo;
9. testar falha no meio da migration e recuperacao;
10. restaurar backup e comparar integridade, contagens e hashes de auditoria.

## 8. Criterio formal de aceite

`ULTRON_CLOUD_DATA_READY` somente sera aprovado quando todas as subetapas U6.1
a U6.8 estiverem concluidas em infraestrutura dedicada, com evidencias
reproduziveis. Ate la, o progresso global permanece **6/20 gates (30,0%)**.

## 9. Proxima acao autorizavel

Criar ou identificar explicitamente um projeto Supabase de desenvolvimento
dedicado ao Ultron. Depois disso: vincular o CLI, gerar as migrations oficiais,
aplicar o schema e executar a matriz real de isolamento entre organizacoes.
