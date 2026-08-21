# Contratos entre os Projetos do Meta

**Estado:** especificacao arquitetural  
**Escopo:** fronteiras de desenvolvimento, consolidacao e integracao

## Principio

Os quatro projetos possuem ciclos de vida independentes. Integracao deve ocorrer por contratos versionados, nunca por acesso acidental a tabelas, arquivos internos, variaveis globais ou detalhes privados de outro projeto.

## Fronteiras

| Projeto | Responsabilidade propria | Contrato exportavel |
|---|---|---|
| Rachel | governanca pessoal, memoria, desktop e ferramentas aprovadas | inventario de capacidades, contratos de politica e artefatos de migracao |
| Jarvis | runtime de agentes, modelos, skills e tools | contratos de execucao, eventos, adapters e benchmark |
| ULTRON | registry e distribuicao segura de componentes | manifests, schemas, assinaturas e protocolo de compatibilidade |
| Zane | produto final, identidade e runtime unificado | APIs publicas, SDKs, eventos e protocolo ULTRON |

## Envelope minimo de evento

Todo evento entre componentes do ecossistema deve poder representar:

| Campo | Funcao |
|---|---|
| `event_id` | idempotencia e rastreio |
| `event_type` | semantica versionada |
| `schema_version` | compatibilidade |
| `occurred_at` | ordem temporal |
| `producer` | origem verificavel |
| `correlation_id` | rastreio ponta a ponta |
| `tenant_id` | isolamento quando aplicavel |
| `actor` | usuario, agente ou servico responsavel |
| `payload` | dados especificos do evento |
| `classification` | sensibilidade e politica de retencao |

## Regras de compatibilidade

1. Contratos publicos devem ser versionados semanticamente.
2. Mudanca incompativel exige nova versao principal e plano de migracao.
3. Consumidores devem rejeitar versoes desconhecidas de forma segura.
4. Operacoes repetiveis devem declarar chave de idempotencia.
5. Chamadas privilegiadas devem propagar identidade, escopo e trilha de auditoria.
6. Segredos nao podem constar em eventos, logs ou manifests.
7. Falha de ULTRON deve degradar apenas capacidades opcionais.

## Consolidacao para Zane

Rachel e Jarvis nao se comunicam indefinidamente com Zane como servicos legados. Durante a consolidacao, cada capacidade candidata recebe uma decisao: reimplementar, adaptar, substituir ou descartar. A aprovacao exige contrato-alvo, testes de paridade quando pertinentes e remocao da dependencia de origem.

## Criterio de aceite

Uma integracao e aceita quando possui schema, versao, autenticacao, autorizacao, tratamento de erro, observabilidade, testes de contrato e estrategia de evolucao documentados.
