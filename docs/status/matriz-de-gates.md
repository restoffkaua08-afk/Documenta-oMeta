# Matriz de Gates do Projeto Meta

**Atualizado em:** 2026-08-24

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
| ULTRON - U2 Installation Ready | Sim | Nao | Nao | Planejado | — |
| ULTRON - U3 Security Ready | Sim | Nao | Nao | Planejado | — |
| ULTRON - U4 Graph Ready | Sim | Nao | Nao | Planejado | — |
| ULTRON - U5 Zane Compatibility | Sim | Nao | Nao | Planejado | — |
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
