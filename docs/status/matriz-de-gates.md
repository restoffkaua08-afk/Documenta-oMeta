# Matriz de Gates do Projeto Meta

**Atualizado em:** 2026-08-28

Esta matriz separa existencia documental, implementacao e validacao. O estado mais fraco prevalece quando as evidencias divergem.

| Projeto ou marco | Especificado | Implementado | Validado | Estado do gate | Evidencia ou pendencia |
|---|---:|---:|---:|---|---|
| Rachel - ciclo arquitetural legado | Sim | Sim | Parcial | Fechado apenas como arquitetura | Relatorios 15/15; nao equivale a producao |
| Rachel - Etapa 1 CI | Sim | Sim | Sim | Aprovado | Commit `0d27273`; registro de 372 testes aprovados e 11 xfail |
| Rachel - Etapa 2 | Sim | Reportado | A confirmar | Pendente | Auditar codigo e executar gate definido |
| Rachel - Etapa 3 | Sim | Reportado | A confirmar | Pendente | Confirmar implementacao, testes e integracao |
| Rachel - interface paralela | Sim | Reportado | Parcial | Pendente | Commit `aec1989`; validar web, desktop e Tauri |
| Rachel - Etapa 4 Tool Runtime | Sim | Reportado | A confirmar | Pendente | Documento de aceite nao substitui auditoria da branch |
| Rachel - Etapa 5 Agent Loop | Sim | Nao confirmado | Nao | Bloqueado | Depende do fechamento das Etapas 2 a 4 |
| Jarvis - baseline standalone | Sim | A inventariar | Nao | Nao aberto | Inspecionar repositorio, versao upstream, testes e licenca |
| **ULTRON - U0 Escopo e Contratos** | Sim | Sim | Sim | **Aprovado** | **Repo `restoffkaua08-afk/ultron`; commit `f240df6`; 76 testes, 92.71% cobertura, ruff+mypy limpos** |
| **ULTRON - U1 Registry Ready** | Sim | Sim | Sim | **Aprovado e estabilizado** | **Base `10ff301`; 111 testes, 89,18% cobertura; lint, format, mypy e build aprovados; portal + API + Registry SQLite/FTS5 entregues** |
| **ULTRON - U2 Installation Ready** | Sim | Sim | Sim | **Aprovado** | `65ff7a5`; 149 testes, 91,15% |
| **ULTRON - U3 Security Ready** | Sim | Sim | Sim | **Aprovado** | `5627b1f`; 168 testes, 91,51% |
| **ULTRON - U4 Graph Ready** | Sim | Sim | Sim local | **Aprovado local** | `30e0698`; 186 testes, 92,61%; validacao cloud em U6 |
| **ULTRON - U5 Zane Compatibility** | Sim | Sim | Sim | **Aprovado** | `a34aeaf`; 208 testes, 91,66% |
| **ULTRON - U6 a U7 Fundacao cloud/identidade** | Sim | Nao | Nao | **Planejado — 2 gates** | Proxima etapa U6 |
| **ULTRON - U8 Capability/Provider Registry** | Sim | Nao | Nao | **Planejado** | Schemas, registries, routing, health, quota e ledger |
| **ULTRON - U9 OpenAPI/Generated Adapters** | Sim | Nao | Nao | **Planejado** | Discovery controlado, parser, geracao, sandbox e aprovacao |
| **ULTRON - U10 a U19 Producao/aceite** | Sim | Nao | Nao | **Planejado — 10 gates** | Plano mestre definitivo U0–U19 |
| Zane - Z0 requisitos | Sim | Nao | Nao | Aguardando | Depende dos inventarios finais de Rachel e Jarvis |

## Evidencias aceitas

- commit ou tag identificavel;
- codigo presente na branch auditada;
- teste reproduzivel com resultado registrado;
- build ou pacote gerado e verificavel;
- verificacao de seguranca ou contrato;
- decisao arquitetural aprovada quando o gate for documental.

## Evidencias insuficientes isoladamente

- plano de implementacao;
- mensagem dizendo que uma etapa terminou;
- checklist sem saida de execucao;
- documento de aceite sem correspondencia no codigo;
- interface visual sem fluxo funcional validado.
