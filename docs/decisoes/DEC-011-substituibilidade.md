# DEC-011 - Substituibilidade de componentes externos

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Decisao

Supabase, Vercel, Railway, Redis, Obsidian, Ollama, vLLM, OpenJarvis, modelos e providers sao componentes substituiveis. Nenhum deles define sozinho a identidade do Meta ou do Zane.

## Aplicacao

- contratos internos precedem SDKs de fornecedores;
- adapters isolam detalhes de provider;
- dados possuem formato de exportacao;
- fallback nao contorna politica de seguranca;
- troca de componente deve ser testavel sem reescrever o produto inteiro.

