# Rachel - Indice Tecnico

## Visao e estado

- [`status-atual.md`](status-atual.md) — resumo curto do progresso corrente
- [`visao.md`](visao.md)
- [`arquitetura/arquitetura-atual.md`](arquitetura/arquitetura-atual.md)
- [`arquitetura/runtime-cognitivo.md`](arquitetura/runtime-cognitivo.md)
- [`arquitetura/membros-e-responsabilidades.md`](arquitetura/membros-e-responsabilidades.md)
- **[`arquitetura/revisao-estrategica-rachel-v1.md`](arquitetura/revisao-estrategica-rachel-v1.md)** — consolidacao atual do que a Rachel e, no que deve se tornar, melhorias obrigatorias, riscos e pontos fortes apos os ultimos planos.
- **[`arquitetura/external-capabilities-na-rachel.md`](arquitetura/external-capabilities-na-rachel.md)** — aplicacao especifica do Capability Registry/Provider Router, Jina/RAG, IA externa, weather, location, documents, voz e demais providers na Rachel.

## Tracker definitivo

- **[`desenvolvimento/roadmap-definitivo-professional-agent-ready.md`](desenvolvimento/roadmap-definitivo-professional-agent-ready.md)** — fonte principal para saber quanto falta, quais sao as 15 etapas e quando a Rachel pode ser declarada completa.

## Arquitetura transversal relacionada

- [`../integracao/external-capabilities-public-apis.md`](../integracao/external-capabilities-public-apis.md) — estrategia compartilhada de External Capabilities para Rachel, Jarvis, ULTRON e Zane.
- [`../integracao/contratos-entre-projetos.md`](../integracao/contratos-entre-projetos.md) — fronteiras e contratos entre os quatro projetos.

## Capacidades

- [`seguranca/governanca-e-aprovacoes.md`](seguranca/governanca-e-aprovacoes.md)
- [`ferramentas/tool-runtime.md`](ferramentas/tool-runtime.md)
- [`memoria/memoria-e-conhecimento.md`](memoria/memoria-e-conhecimento.md)
- [`modelos/model-router-e-rachel-model.md`](modelos/model-router-e-rachel-model.md)
- [`interface/estado.md`](interface/estado.md)

## Desenvolvimento e evidencias

- [`desenvolvimento/ciclos.md`](desenvolvimento/ciclos.md)
- [`desenvolvimento/roadmap-profissional.md`](desenvolvimento/roadmap-profissional.md) — historico; superado como tracker
- [`desenvolvimento/etapas-01-a-04.md`](desenvolvimento/etapas-01-a-04.md)
- [`desenvolvimento/etapa-06-model-router.md`](desenvolvimento/etapa-06-model-router.md)
- [`desenvolvimento/etapa-07-project-intelligence.md`](desenvolvimento/etapa-07-project-intelligence.md)
- [`desenvolvimento/etapa-08-dany-professional.md`](desenvolvimento/etapa-08-dany-professional.md)
- [`desenvolvimento/etapa-09-knowledge-port.md`](desenvolvimento/etapa-09-knowledge-port.md)
- [`desenvolvimento/etapa-10-web-research-professional.md`](desenvolvimento/etapa-10-web-research-professional.md)
- [`desenvolvimento/etapa-11-browser-governado.md`](desenvolvimento/etapa-11-browser-governado.md)
- [`evidencias/baseline-e-readiness.md`](evidencias/baseline-e-readiness.md)

## Regra de leitura

Para progresso atual, usar `status-atual.md` e o roadmap definitivo. Para External Capabilities, a especificacao transversal define a estrategia do Meta e `external-capabilities-na-rachel.md` define como ela e aplicada especificamente na Rachel.

A revisao estrategica da V1 serve como mapa conceitual da identidade atual/futura e nao substitui os gates do roadmap.

Documentos de etapas servem como evidencias detalhadas. O ciclo arquitetural antigo e o roadmap profissional antigo permanecem apenas como historico.

Em caso de divergencia, prevalecem: codigo atual -> CI/testes atuais -> roadmap definitivo -> arquitetura especifica atualizada -> documentos historicos.
