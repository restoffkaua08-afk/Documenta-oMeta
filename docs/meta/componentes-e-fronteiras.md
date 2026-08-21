# Componentes e Fronteiras

## Objetivo

Este documento impede que responsabilidades sejam duplicadas ou atribuídas ao projeto errado durante a evolucao do Meta.

## Matriz de propriedade

| Dominio | Rachel standalone | Jarvis standalone | ULTRON | Zane final |
|---|---|---|---|---|
| Persona | Implementa e valida | Nao e dono | Nao e dono | Implementa de forma propria |
| Memoria pessoal | Implementa e valida | Estado temporario apenas | Metadados de packs | Implementa de forma propria |
| Governanca | Implementa e valida | Fornece hooks | Valida instalacao | Autoridade final |
| Planner | Capacidade nativa limitada/evolutiva | Capacidade principal | Pode distribuir workflows | Runtime proprio consolidado |
| Tools | Superficie governada | Superficie ampla agentic | Distribui especificacoes | Registry nativo governado |
| Skills | Pode consumir | Implementa/consome | Publica e versiona | Implementa/consome |
| Model Router | Evolui no roadmap | Valida providers | Nao e dono | Autoridade final |
| UI | Laboratorio desktop/web | UI tecnica quando aplicavel | Portal/graph | Experiencia principal |
| Marketplace | Fora do escopo | Fora do escopo | Responsabilidade principal | Cliente opcional |

## Contratos de fronteira

### Solicitacao de runtime

Deve carregar identificadores, usuario/tenant quando aplicavel, conversa, intencao, conteudo, contexto autorizado, preferencia de modelo, limites e metadados de rastreio.

### Resposta de runtime

Deve distinguir texto, eventos, plano, ferramentas propostas/executadas, evidencias, metricas, estado final, erro e necessidade de aprovacao.

### Especificacao de ferramenta

Deve declarar nome, versao, schema de entrada, schema de saida, efeitos, nivel de risco, escopos, timeout, idempotencia, politica de retry e requisitos de aprovacao.

### Evento

Deve conter `event_id`, correlacao, tipo, timestamp, origem, estado e payload sanitizado. Eventos internos dos predecessores nao devem vazar como dependencia imutavel do Zane.

## Antiacoplamentos

- UI nao acessa banco diretamente para contornar politicas.
- Modelo nao chama executores sem Tool Coordinator e Cyber/politica equivalente.
- Memoria nao recebe automaticamente todo conteudo bruto.
- ULTRON nao ganha permissao de execucao apenas porque um pack foi instalado.
- Adapter nao se torna local definitivo de regra de negocio.
- Nome de membro/persona nao deve substituir contratos tipados.

## Criterio para absorcao no Zane

Uma capacidade de Rachel ou Jarvis so deve ser absorvida quando possui:

1. comportamento entendido;
2. valor claro para o produto final;
3. contrato documentado;
4. testes ou plano de validacao;
5. analise de seguranca e dados;
6. decisao entre reutilizar, reimplementar, adaptar ou descartar.

