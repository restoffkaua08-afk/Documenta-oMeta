# Jarvis - Agentes, Perfis e Orquestracao

## Agentes mencionados nas fontes

| Agente upstream | Capacidade planejada |
|---|---|
| `simple` | conversa direta e tarefas simples |
| `native_react` | raciocinio orientado a acoes/tools |
| `orchestrator` | delegacao entre agentes |
| `native_openhands` | trabalho com codigo |
| `deep_research` | pesquisa profunda multi-fonte |
| `morning_digest` | briefing e resumo periodico |
| `operative` | operacao de ferramentas |
| `monitor_operative` | acompanhamento de tarefas longas |

Esta lista veio do planejamento e deve ser validada contra a versao upstream importada.

## Camada de perfis

O usuario nao precisa conhecer nomes tecnicos dos agentes. Um `AgentProfile` descreve capacidades, ferramentas, modelos preferidos, limites e politica.

Durante testes com Rachel, os agentes podem ser mapeados para os dominios Rachel/Ned/Arya. Esse mapeamento e uma visao de experiencia e nao deve modificar irreversivelmente os agentes upstream.

## Mapeamento de referencia

| Perfil de experiencia | Capacidades candidatas |
|---|---|
| Rachel | conversa, orquestracao e briefing |
| Ned | pesquisa, codigo e planejamento |
| Arya | operacao e monitoramento |
| Cyber | camada transversal de politica; nao precisa corresponder a um agente upstream |

Documentos historicos divergiram sobre alguns agentes. A atribuicao final depende do inventario real e de testes, nao do nome.

## Router

O roteamento deve considerar:

- intencao;
- complexidade;
- tools necessarias;
- risco;
- contexto;
- duracao;
- modelo/provider;
- politica do usuario.

## Orchestrator

Responsabilidades:

- decompor objetivo;
- escolher agentes adequados;
- ordenar dependencias;
- propagar cancelamento e budgets;
- impedir ciclos infinitos;
- agregar resultados com proveniencia;
- solicitar aprovacao antes dos efeitos.

## Handoffs

Cada transferencia entre agentes deve carregar objetivo, contexto minimo, artefatos, estado, limites, ferramentas permitidas e criterio de conclusao.

## Anti-patterns

- agente aprovar sua propria acao;
- todos os agentes receberem todos os segredos;
- delegacao recursiva sem limite;
- responder sucesso quando um subagente falhou;
- esconder do usuario mudancas relevantes de plano;
- usar persona como controle de acesso.

