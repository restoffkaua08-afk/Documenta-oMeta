# ULTRON - Indice Tecnico

## Documentos de design e especificacao

- [`visao.md`](visao.md)
- [`arquitetura/registry-e-componentes.md`](arquitetura/registry-e-componentes.md)
- [`schemas/agents-skills-workflows-packs.md`](schemas/agents-skills-workflows-packs.md)
- [`lifecycle/instalacao-ativacao-versionamento.md`](lifecycle/instalacao-ativacao-versionamento.md)
- [`dados/memoria-compartilhada-e-grafo.md`](dados/memoria-compartilhada-e-grafo.md)
- [`seguranca/validacao-sandbox-e-supply-chain.md`](seguranca/validacao-sandbox-e-supply-chain.md)
- [`integracao/protocolo-de-compatibilidade.md`](integracao/protocolo-de-compatibilidade.md)
- [`desenvolvimento/roadmap-e-gates.md`](desenvolvimento/roadmap-e-gates.md)
- [`desenvolvimento/plano-mestre-conclusao.md`](desenvolvimento/plano-mestre-conclusao.md) — **tracker definitivo U0–U17**

## Status e implementacao (a partir de 2026-08-24)

- [`status/status-atual.md`](status/status-atual.md) — estado por gate, o que foi feito, o que falta
- [`gates/U0.md`](gates/U0.md) — prova do gate U0 (Escopo e Contratos) ✅
- [`gates/U1.md`](gates/U1.md) — prova do gate U1 (Registry Ready) ✅
- [`decisoes/registro.md`](decisoes/registro.md) — DEC-U-001 a DEC-U-009 (decisoes arquiteturais)
- [`design/portal-obsidian.md`](design/portal-obsidian.md) — design UI/UX do portal (gate U1+)
- [`necessidades/adicionais-design-arquitetura.md`](necessidades/adicionais-design-arquitetura.md) — gaps entre spec e implementacao

## Estado

ULTRON possui o nucleo local U0–U5 aprovado. O plano definitivo possui 18 gates;
6 estao aprovados e 12 permanecem necessarios para producao real:

- **Gate U0 (Escopo e Contratos)** — ✅ Aprovado. 76 testes, 92.71% cobertura, ruff+mypy limpos.
- **U0–U5** — aprovados na baseline `a34aeaf`; suite final com 208 testes e 91,66% de cobertura.
- **U6–U17** — pendentes; proxima etapa: fundacao cloud real.
- **Progresso oficial:** 6/18 gates, ou **33,3%**.

Repositorio oficial: https://github.com/restoffkaua08-afk/ultron
