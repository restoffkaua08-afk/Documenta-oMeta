# Jarvis - Model Router

## Objetivo

Expor uma interface unica para modelos locais e cloud, escolhendo a opcao adequada sem acoplar agentes a SDKs especificos.

## Capacidades do provider

- chat/text generation;
- streaming;
- tool calling;
- structured output;
- embeddings quando aplicavel;
- limites de contexto;
- usage/custo;
- cancelamento;
- health/readiness.

## Providers candidatos

As fontes citam Ollama, llama.cpp/vLLM e providers cloud como OpenAI-compatible, Anthropic e Gemini. Suporte real depende do upstream e deve ser inventariado.

## Politica de selecao

O router avalia tarefa, capacidade, privacidade, contexto, latencia, custo, disponibilidade e preferencia. Agentes podem expressar requisitos, mas nao forcar provider proibido.

## Tiers conceituais

| Tier | Uso |
|---|---|
| Fast/local | conversa simples, classificacao, baixa latencia |
| Balanced | planejamento e tools comuns |
| Advanced | pesquisa/codigo/contexto maior |
| Specialized | embeddings, visao ou outra capacidade especifica |

## Fallback

Fallback deve ser ordenado, observavel e compativel com privacidade. Uma falha local nao autoriza automaticamente envio de dados para cloud.

## Custos e budgets

Registrar tokens/usage quando fornecidos, latencia, provider/modelo e custo estimado. Budgets podem limitar solicitacao, sessao, usuario ou job.

## Testes

- selecao por capacidade;
- provider indisponivel;
- timeout/cancelamento;
- structured output invalido;
- fallback bloqueado por privacidade;
- contabilizacao de usage;
- reproducao com mocks e providers reais opcionais.

