# ULTRON — Plano Mestre Definitivo de Conclusao

**Versao do plano:** 1.1
**Data-base:** 2026-08-28  
**Repositorio:** `restoffkaua08-afk/ultron`  
**Baseline auditada:** `45b82fa`
**Versao atual do produto:** `0.1.0` (`Alpha`)  
**Owner:** Kauã

## 1. Objetivo deste documento

Este e o tracker oficial para responder, sem ambiguidade:

- quanto do Ultron esta realmente pronto;
- o que ainda falta;
- qual e a proxima etapa;
- quais evidencias tornam uma etapa aprovada;
- quando o produto pode ser chamado de completo e pronto para producao.

O Ultron somente sera considerado **pronto, pronto** quando os 20 gates U0–U19
estiverem aprovados, o gate final `ULTRON_PRODUCTION_ACCEPTED` estiver verde e
nao houver pendencia critica, excecao temporaria vencida ou validacao simulada
substituindo infraestrutura real.

## 2. Diagnostico executivo

O nucleo arquitetural local esta forte e comprovado: registry, instalacao,
seguranca contratual, grafo, operacao offline e protocolo MCP passaram por 208
testes com 91,66% de cobertura. Isso conclui U0–U5.

Ainda nao e um produto de producao. O codigo continua em `0.1.0`, classificado
como Alpha, e os seguintes elementos dependem de entrega real: Supabase,
autenticacao, RLS, armazenamento de artefatos, workers de sandbox, portal
multiusuario, SDKs, observabilidade, CI/CD, recuperacao, testes de carga,
auditoria externa e aceite operacional.

| Indicador | Valor atual |
|---|---:|
| Etapas totais | 20 |
| Etapas aprovadas | 6 |
| Etapas restantes | 14 |
| Progresso por gates | **30,0%** |
| Gates do nucleo local | **6/6 aprovados** |
| Gates de producao real | **0/14 aprovados** |
| Estado honesto | **Nucleo local completo; produto em Alpha** |

O percentual mede gates aprovados, nao horas trabalhadas. Uma etapa so conta
depois de implementacao, validacao e evidencia publicadas.

## 3. Definicao formal de “completo”

O Ultron estara completo quando comprovar simultaneamente:

1. produto: os fluxos essenciais funcionam para usuarios reais;
2. dados: Postgres/Supabase real, migrations repetiveis, RLS e backup testados;
3. identidade: login GitHub, sessoes, organizacoes, RBAC e revogacao;
4. seguranca: supply chain, segredos e execucao isolada verificados;
5. integracao: MCP, REST, CLI e SDKs interoperam com consumers reais;
6. confiabilidade: observabilidade, alertas, SLOs, runbooks e rollback;
7. desempenho: carga, concorrencia, limites e custos conhecidos;
8. entrega: CI/CD, ambientes, releases assinadas e deploy reproduzivel;
9. operacao: backup/restauracao, incidentes e disaster recovery ensaiados;
10. aceite: E2E externo, beta controlado, documentacao e checklist final.

## 4. Regra universal de aprovacao

Cada etapa exige todas as evidencias aplicaveis:

- requisito e criterios de aceite versionados;
- codigo na `main`, identificado por commit;
- testes automatizados relevantes;
- lint, formatacao, tipagem e build verdes;
- teste E2E quando houver fronteira externa;
- evidencia da infraestrutura real quando a etapa for cloud;
- seguranca e isolamento negativos, nao apenas caminho feliz;
- documentacao tecnica, operacional e de usuario atualizada;
- riscos e decisoes registrados;
- rollback ou recuperacao testados.

Documento, mock, blueprint SQL, screenshot ou mensagem de conclusao nao aprovam
isoladamente um gate de producao.

## 5. Roadmap fechado U0–U19

### U0 — Escopo e contratos — APROVADO

**Gate:** `ULTRON_SCOPE_READY`  
**Evidencia:** `f240df6`; 76 testes; 92,71% de cobertura.

Entregou dominio imutavel, schemas estritos, politica deny-by-default e contrato
base de consumer.

### U1 — Registry e portal base — APROVADO

**Gate:** `ULTRON_REGISTRY_READY`  
**Evidencia:** base `10ff301`; estabilizacao posterior; 111 testes.

Entregou Registry SQLite/FTS5, API versionada, auditoria e portal local.

### U2 — Lifecycle de instalacao — APROVADO

**Gate:** `ULTRON_INSTALLATION_READY`  
**Evidencia:** `65ff7a5`; 149 testes; 91,15% de cobertura.

Entregou resolver, store imutavel, lockfile, rollback, coleta e suite neutra de
conformidade.

### U3 — Seguranca do nucleo — APROVADO

**Gate:** `ULTRON_SECURITY_READY`  
**Evidencia:** `5627b1f`; 168 testes; 91,51% de cobertura.

Entregou validacao, assinatura, trust store, quarentena, revogacao propagada,
auditoria encadeada e contrato de sandbox deny-by-default.

### U4 — Dados e grafo local — APROVADO

**Gate:** `ULTRON_GRAPH_READY`  
**Evidencia:** `30e0698`; 186 testes; 92,61% de cobertura.

Entregou namespaces, lineage, grafo operacional, busca, retencao, API/portal e
teste local com 10.000 nos. O blueprint Supabase ainda nao equivale a banco real.

### U5 — Compatibilidade de consumers — APROVADO

**Gate:** `ULTRON_ZANE_COMPATIBLE`  
**Evidencia:** `a34aeaf`; 208 testes; 91,66% de cobertura.

Entregou operacao offline segura, equivalencia Python/REST/MCP, servidor MCP,
lifecycle persistente local e E2E com cliente MCP oficial sobre HTTP real.

### U6 — Fundacao cloud real — EM ANDAMENTO

**Objetivo:** substituir blueprints por infraestrutura Supabase executada e
reproduzivel.

**Entregaveis:**

- projetos Supabase separados para desenvolvimento, preview e producao;
- migrations oficiais para registry, consumers, installations, records,
  lineage, audit e revocation;
- RLS e grants minimos aplicados no banco real;
- pool de conexoes, transacoes, indices e timeouts configurados;
- seed seguro e estrategia de migracao SQLite → Postgres;
- backup, PITR conforme o plano escolhido e restauracao ensaiada;
- Advisors de seguranca e performance sem achado critico.

**Aceite:** migration sobe banco vazio, atualiza banco existente e reverte em
ambiente de teste; testes provam isolamento entre duas organizacoes no Postgres
real; backup e restauracao recuperam estado consistente.

**Gate:** `ULTRON_CLOUD_DATA_READY`.

**Incremento U6.1 publicado:** hardening dos candidatos SQL com grants
especificos, revogacao de defaults, foreign keys compostas para organizacao e
namespace e seis contratos estaticos. Suite completa: 214 testes, 91,66% de
cobertura, Ruff, MyPy e build aprovados. Evidencia: `45b82fa`. O gate permanece
fechado ate existir projeto Supabase dedicado, migrations oficiais, RLS real,
Advisors e restauracao ensaiada. Detalhes em
[`etapa-u6-fundacao-cloud.md`](etapa-u6-fundacao-cloud.md).

### U7 — Identidade, organizacoes e autorizacao — PENDENTE

**Objetivo:** garantir identidade real e autorizacao completa em todas as
fronteiras.

**Entregaveis:**

- login GitHub OAuth via Supabase Auth;
- vinculo seguro entre usuario, identidade GitHub e organizacao;
- roles `owner`, `admin`, `security_admin`, `publisher`, `member` e `viewer`;
- convites, troca de organizacao, remocao e transferencia de ownership;
- sessao, logout, refresh, expiracao e revogacao;
- autorizacao equivalente em REST, MCP, portal e jobs;
- protecao CSRF, validacao de redirect e rate limit de autenticacao.

**Aceite:** matriz positiva e negativa por role; usuario removido perde acesso
imediatamente; nenhuma rota ou ferramenta aceita `organization_id` confiando
somente no corpo da requisicao.

**Gate:** `ULTRON_IDENTITY_READY`.

### U8 — Capability Registry e Provider Registry — PENDENTE

**Objetivo:** transformar capabilities e providers em recursos formais,
versionados, pesquisaveis e distribuiveis sem acoplar consumers a fornecedores.

**Entregaveis:**

- `CapabilityManifest` com schemas logicos, side effects, dados e cache policy;
- `ProviderManifest` com auth abstrata, hosts, health, quota, custo e privacidade;
- novos kinds strict/frozen, migrations SQLite/Postgres e busca dedicada;
- relacoes formais com agents, skills, workflows e packs;
- estados de lifecycle, compatibilidade, deprecacao e revogacao;
- Provider Router de referencia substituivel, Health Manager, Rate Limit Manager
  e Usage Ledger;
- snapshots locais assinados para operacao offline;
- documentacao descrita em
  [`../arquitetura/external-capabilities-provider-registry.md`](../arquitetura/external-capabilities-provider-registry.md).

**Aceite:** capability e provider possuem contratos versionados; dois providers
intercambiaveis passam a mesma suite; health degradado causa fallback somente
quando policy, privacidade e idempotencia permitem; consumer inicia e usa
adapter instalado com Ultron offline.

**Gate:** `ULTRON_CAPABILITY_PROVIDER_READY`.

### U9 — OpenAPI discovery e adapters gerados — PENDENTE

**Objetivo:** descobrir APIs e gerar candidatos de tools/adapters sem converter
conteudo externo nao confiavel em codigo ativo.

**Entregaveis:**

- fontes APIs.guru, URL/spec do usuario e Git fixado por commit;
- manifests de OpenAPI definition e generated adapter;
- parser com limites para OpenAPI 3.0/3.1/3.2 e Swagger/OpenAPI 2.0;
- protecoes SSRF, redirects, references, tamanho, profundidade e schema bombs;
- inferencia revisavel de capability e efeitos;
- geracao deterministica de adapter, tool schema, testes e proveniencia;
- static analysis, sandbox contract tests, quarentena e aprovacao;
- publicacao assinada e desabilitada por padrao;
- explicabilidade de aprovacao, rejeicao e quarentena.

**Aceite:** E2E `discover → parse → generate → validate → publish disabled`
passa; specs maliciosas falham fechadas; nenhuma tool e executada/ativada antes
de autorizacao; output gerado e reproduzivel pelo mesmo digest.

**Gate:** `ULTRON_OPENAPI_GENERATION_READY`.

### U10 — API e MCP de producao — PENDENTE

**Objetivo:** tornar os contratos externos seguros, estaveis e operaveis.

**Entregaveis:**

- autenticacao e scopes em REST e MCP;
- idempotency keys e correlation IDs em mutacoes;
- paginacao, limites, timeouts e erros padronizados;
- rate limiting por identidade e organizacao;
- versionamento e politica de deprecacao;
- OpenAPI completa, exemplos e contract tests publicados;
- protecao contra payload excessivo, replay e abuso de ferramentas;
- compatibilidade comprovada com clientes oficiais suportados.

**Aceite:** suites de contrato e concorrencia passam; retry nao duplica mutacao;
clients antigos recebem janela de compatibilidade documentada.

**Gate:** `ULTRON_PROTOCOL_PRODUCTION_READY`.

### U11 — Artefatos, publicacao e supply chain real — PENDENTE

**Objetivo:** publicar, armazenar e distribuir agents/skills de modo verificavel.

**Entregaveis:**

- object storage privado e enderecado por hash;
- upload multipart com limites, MIME allowlist e checksum;
- assinatura de publicacao e verificacao no download;
- SBOM, proveniencia, licenca e politica de secrets;
- scanner de malware, segredos e dependencias vulneraveis;
- quarentena automatica e workflow de revisao;
- garbage collection segura e retencao de versoes referenciadas;
- importacao Git por commit imutavel e instalacao por pacote reproduzivel.

**Aceite:** adulteracao e pacote malicioso de teste sao bloqueados; versao
publicada e imutavel; instalacao reproduz o mesmo digest em ambientes distintos.

**Gate:** `ULTRON_SUPPLY_CHAIN_PRODUCTION_READY`.

### U12 — Workers e sandbox isolado real — PENDENTE

**Objetivo:** executar validacoes e tarefas fora da aplicacao web em isolamento
comprovado.

**Entregaveis:**

- fila duravel e workers separados;
- containers ou microVMs efemeros fixados por digest;
- rede, filesystem e processos deny-by-default;
- quotas de CPU, memoria, tempo, saida e concorrencia;
- segredos com escopo minimo e descarte apos execucao;
- cancelamento, retry, dead-letter queue e idempotencia;
- logs vinculados ao audit trail sem vazar segredos;
- testes de fuga e abuso em ambiente controlado.

**Aceite:** web nunca executa pacote; tentativa de rede/filesystem nao concedida
falha; timeout mata toda a arvore; repeticao nao duplica efeitos.

**Gate:** `ULTRON_SANDBOX_RUNTIME_READY`.

### U13 — Portal de produto completo — PENDENTE

**Objetivo:** entregar a experiencia multiusuario final, responsiva e acessivel.

**Entregaveis:**

- onboarding, login, organizacoes e perfis;
- catalogo, busca, filtros, detalhes, grafo e lineage;
- publicacao, instalacao, ativacao, remocao e rollback governados;
- catalogo de capabilities/providers, OpenAPI, adapters, health, quota e lineage;
- telas de permissoes, aprovacoes, quarentena, revogacao e auditoria;
- estados de loading, vazio, erro, offline e conflito;
- responsividade mobile/desktop, acessibilidade WCAG 2.2 AA e navegacao por teclado;
- eliminacao de dependencia CDN critica ou fallback realmente funcional;
- analytics de produto com privacidade e consentimento aplicaveis.

**Aceite:** jornadas principais passam em testes de navegador, axe e dispositivos
alvo; nenhuma mutacao sensivel ocorre sem confirmacao e feedback auditavel.

**Gate:** `ULTRON_PORTAL_READY`.

### U14 — CLI, SDKs e integracoes reais — PENDENTE

**Objetivo:** permitir adocao consistente fora do portal.

**Entregaveis:**

- CLI autenticada para catalogo, publish, install, lifecycle e diagnostico;
- SDK Python versionado e documentado;
- SDK TypeScript ou cliente gerado quando necessario ao ecossistema;
- configuracoes verificadas para Claude, Codex e Zane;
- exemplos executaveis e quickstarts;
- matriz de versoes e testes de compatibilidade;
- modo offline documentado e comportamento de falha consistente.
- quickstarts de capability/provider e tool gerada sem SDK de vendor no agent.

**Aceite:** um ambiente limpo instala o cliente e conclui quickstart; E2E real
passa para cada consumer oficialmente suportado.

**Gate:** `ULTRON_ECOSYSTEM_READY`.

### U15 — Observabilidade e operacao — PENDENTE

**Objetivo:** tornar o sistema diagnosticavel antes de receber uso real.

**Entregaveis:**

- logs estruturados, metricas e traces com correlation ID;
- dashboards de disponibilidade, latencia, erros, filas e banco;
- metricas de health, fallback, quota, custo e freshness por provider;
- SLI/SLO e error budget iniciais;
- alertas acionaveis, sem ruido excessivo;
- health, readiness e dependency checks;
- runbooks para auth, banco, fila, storage, MCP e sandbox;
- redacao de dados sensiveis e politica de retencao;
- auditoria exportavel e verificacao automatica da cadeia.

**Aceite:** falhas injetadas sao detectadas, alertadas e diagnosticadas pelos
runbooks; nenhum segredo aparece nos logs.

**Gate:** `ULTRON_OPERATIONS_READY`.

### U16 — CI/CD, ambientes e release — PENDENTE

**Objetivo:** entregar mudancas com seguranca e reproducibilidade.

**Entregaveis:**

- CI obrigatoria para testes, cobertura, lint, format, MyPy e build;
- scans de dependencias, segredos, SAST, containers e SBOM;
- ambientes dev, preview, staging e production isolados;
- migrations coordenadas e estrategia backward-compatible;
- deploy Vercel e workers automatizados;
- artefatos e releases assinados, changelog e SemVer;
- rollout gradual, smoke tests e rollback automatizavel;
- branch protection e revisao exigida para areas criticas.

**Aceite:** release candidata percorre pipeline inteira; rollback recupera versao
anterior e migrations nao quebram a janela de compatibilidade.

**Gate:** `ULTRON_DELIVERY_READY`.

### U17 — Confiabilidade, escala e disaster recovery — PENDENTE

**Objetivo:** comprovar limites e recuperacao do sistema completo.

**Entregaveis:**

- metas de carga e capacidade documentadas;
- testes de carga de API, MCP, busca, grafo, banco, fila e storage;
- carga de discovery, health checks e roteamento/fallback multi-provider;
- concorrencia de instalacao/publicacao e race conditions testadas;
- soak test e analise de vazamento de recursos;
- degradacao controlada e circuit breakers;
- plano de continuidade, RPO e RTO;
- restauracao de banco/storage e retomada de jobs ensaiadas;
- modelo e alertas de custo.

**Aceite:** metas SLO sob carga sao atingidas; restauracao cumpre RPO/RTO; falha
de dependencia nao corrompe estado nem derruba capacidades nativas do consumer.

**Gate:** `ULTRON_RELIABILITY_READY`.

### U18 — Seguranca, privacidade e conformidade final — PENDENTE

**Objetivo:** fechar riscos antes do lancamento.

**Entregaveis:**

- threat model final e data-flow diagram atualizados;
- revisao OWASP de API, autenticacao, SSRF, supply chain e multi-tenant;
- revisao especifica de OpenAPI hostil, geracao de codigo e provider substitution;
- teste de penetracao ou auditoria independente proporcional ao risco;
- triagem e correcao de vulnerabilidades criticas/altas;
- inventario de dados, minimizacao, exportacao e exclusao;
- politicas de privacidade, termos, retencao e resposta a incidente;
- rotacao de chaves/segredos e procedimento de comprometimento;
- divulgacao de vulnerabilidade e contatos de seguranca.

**Aceite:** zero achado critico ou alto aberto sem excecao formal, prazo e
mitigacao; exclusao/exportacao e resposta a incidente sao ensaiadas.

**Gate:** `ULTRON_SECURITY_ACCEPTED`.

### U19 — Beta, aceite e lancamento — PENDENTE

**Objetivo:** provar que o produto completo resolve o uso real e pode ser
operado com seguranca.

**Entregaveis:**

- staging equivalente a producao;
- matriz E2E cobrindo todos os fluxos essenciais e falhas principais;
- beta controlado com usuarios/consumers reais;
- coleta, classificacao e fechamento de feedback bloqueante;
- documentacao de usuario, administrador, integrador e operador;
- checklist de lancamento, ownership e suporte;
- tag `v1.0.0`, release notes e baseline de producao;
- auditoria final da documentacao contra codigo e infraestrutura.

**Aceite:** todos os gates U0–U18 permanecem verdes; zero bug bloqueante ou
critico aberto; smoke test de producao passa; rollback final e suporte estao
prontos; owner assina o aceite.

**Gate final:** `ULTRON_PRODUCTION_ACCEPTED`.

## 6. Ordem obrigatoria e dependencias

1. U6 antes de qualquer declaracao cloud.
2. U7 antes de expor lifecycle multiusuario.
3. U8 depende dos contratos de U6 e da identidade/policy de U7.
4. U9 depende de U8; discovery nunca antecede os schemas confiaveis.
5. U10 depende de U6–U9 para expor contratos de producao.
6. U11 e U12 protegem e isolam specs/adapters antes de uso real.
7. U13 e U14 podem avancar em paralelo depois dos contratos de U10–U12.
8. U15 e U16 devem existir antes de staging final.
9. U17 depende do sistema integrado U6–U16.
10. U18 audita o sistema completo.
11. U19 e exclusivamente aceite, nao local para empurrar pendencias tecnicas.

## 7. Tracker oficial

| Etapa | Gate | Estado | Progresso acumulado |
|---|---|---|---:|
| U0 | `ULTRON_SCOPE_READY` | Aprovado | 5,0% |
| U1 | `ULTRON_REGISTRY_READY` | Aprovado | 10,0% |
| U2 | `ULTRON_INSTALLATION_READY` | Aprovado | 15,0% |
| U3 | `ULTRON_SECURITY_READY` | Aprovado | 20,0% |
| U4 | `ULTRON_GRAPH_READY` | Aprovado local | 25,0% |
| U5 | `ULTRON_ZANE_COMPATIBLE` | Aprovado | **30,0%** |
| U6 | `ULTRON_CLOUD_DATA_READY` | Em andamento | 30,0% |
| U7 | `ULTRON_IDENTITY_READY` | Pendente | 30,0% |
| U8 | `ULTRON_CAPABILITY_PROVIDER_READY` | Pendente | 30,0% |
| U9 | `ULTRON_OPENAPI_GENERATION_READY` | Pendente | 30,0% |
| U10 | `ULTRON_PROTOCOL_PRODUCTION_READY` | Pendente | 30,0% |
| U11 | `ULTRON_SUPPLY_CHAIN_PRODUCTION_READY` | Pendente | 30,0% |
| U12 | `ULTRON_SANDBOX_RUNTIME_READY` | Pendente | 30,0% |
| U13 | `ULTRON_PORTAL_READY` | Pendente | 30,0% |
| U14 | `ULTRON_ECOSYSTEM_READY` | Pendente | 30,0% |
| U15 | `ULTRON_OPERATIONS_READY` | Pendente | 30,0% |
| U16 | `ULTRON_DELIVERY_READY` | Pendente | 30,0% |
| U17 | `ULTRON_RELIABILITY_READY` | Pendente | 30,0% |
| U18 | `ULTRON_SECURITY_ACCEPTED` | Pendente | 30,0% |
| U19 | `ULTRON_PRODUCTION_ACCEPTED` | Pendente | 30,0% |

## 8. Proxima acao unica

Continuar **U6 — Fundacao cloud real**. O hardening local U6.1 esta concluido.
A proxima acao e identificar um projeto Supabase de desenvolvimento dedicado,
gerar migrations oficiais pelo CLI, aplicar o schema e executar testes reais de
RLS entre duas organizacoes. Nenhum novo recurso visual deve preceder essa
fundacao.

## 9. Politica de manutencao documental

Ao concluir qualquer incremento:

1. atualizar o documento da etapa com decisao, implementacao e testes;
2. registrar commit, metricas e riscos neste tracker;
3. atualizar `status-atual.md` e a matriz geral do Meta;
4. manter o gate como pendente ate todos os criterios da etapa passarem;
5. registrar pendencias reais — nunca rebatiza-las como “melhoria futura”;
6. recalcular o percentual somente quando um gate inteiro for aprovado.

## 10. Anti-gates finais

O Ultron nao pode ser declarado completo se qualquer condicao abaixo existir:

- ambiente real substituido apenas por SQLite, mock ou blueprint;
- RLS, OAuth, backup ou restore nao testados no servico real;
- codigo de capability executado em funcao web;
- mutacao sem identidade, autorizacao, confirmacao e auditoria;
- pacote mutavel, nao assinado ou nao verificavel;
- segredo em codigo, log ou artefato;
- deploy manual nao reproduzivel;
- ausencia de observabilidade, runbook ou rollback;
- vulnerabilidade critica/alta sem tratamento formal;
- bug critico/bloqueante aberto;
- consumer deixa de iniciar quando o Ultron fica indisponivel;
- documentacao contradiz codigo ou infraestrutura;
- gate aprovado apenas por declaracao.
