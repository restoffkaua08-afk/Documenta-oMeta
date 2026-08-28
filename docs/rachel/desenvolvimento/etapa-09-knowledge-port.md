# Rachel — Etapa 09: Knowledge Port real

**Estado:** Implementada; revalidacao final em andamento  
**Data de atualizacao:** 2026-08-28  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Conectar o Core da Rachel ao conhecimento documental governado indexado pelo runtime, preservando a separacao fundamental entre memoria pessoal e conhecimento/RAG.

Antes desta etapa, o bootstrap usava `NullKnowledgeAdapter`. A arquitetura atual usa um adapter SQLite real e entrega evidencias documentais ao `ChatService`.

## Implementacao consolidada

### SQLiteKnowledgeAdapter

Arquivo:

- `RACHEL_CORE/src/rachel_core/adapters/knowledge_sqlite.py`.

Garantias:

- implementa `KnowledgePort.search()`;
- leitura do banco governado de Bran/Visao;
- somente registros ativos;
- filtro estrito `metadata.kind=document_chunk`;
- preferencias, instrucoes e memorias pessoais nao sao expostas como RAG;
- ranking lexical com metadados de confianca/importancia;
- limites defensivos;
- banco ausente degrada para resultado vazio sem quebrar o chat;
- erros SQLite sao tratados como indisponibilidade do backend.

Commit base:

- `d22a2ecb829b52b076462589fbe0b9d39e19f196`.

### Bootstrap real

`RACHEL_CORE/src/rachel_core/bootstrap.py` constroi `SQLiteKnowledgeAdapter` em vez de `NullKnowledgeAdapter`.

Resolucao do banco:

1. `RACHEL_KNOWLEDGE_DB_PATH` quando configurado;
2. fallback para `bran-cognitive.db` no layout local da Rachel.

O `Container` expoe `knowledge`, tornando o backend auditavel.

Commit base:

- `7e017d5c22b7ed18dac91d071a4f14f72147d557`.

### Configuracao

`RACHEL_CORE/.env.example` documenta `RACHEL_KNOWLEDGE_DB_PATH`.

### Capability verdadeira

O Core possui teste dedicado para garantir que o status de conhecimento seja derivado do backend real e nao de um `knowledge=True` ficticio:

- `RACHEL_CORE/tests/test_knowledge_bootstrap.py`.

A sobreposicao historica enganosa no bridge foi removida durante a evolucao posterior. O gate final exige continuar sem capability hardcoded.

## Evidencias automatizadas

### Core

- `RACHEL_CORE/tests/test_knowledge_sqlite.py`;
- `RACHEL_CORE/tests/test_knowledge_chat_integration.py`;
- `RACHEL_CORE/tests/test_knowledge_bootstrap.py`.

Esses testes cobrem:

- recuperacao de documento relevante;
- isolamento de memoria pessoal;
- ranking;
- banco ausente;
- evidence injection no system prompt;
- bootstrap usando adapter real;
- status coerente com backend real.

### Runtime

- `RACHEL_PLATFORM/RUNTIME/TESTS/test_knowledge_port_integration.py`.

Esse contrato comprova o caminho de integracao documental pelo runtime sem transformar memoria pessoal em conhecimento.

## Reforco da CI em 2026-08-28

O workflow profissional `RACHEL CI` ja executava a suite completa do Core. Para tornar o gate da Etapa 09 explicitamente protegido tambem no Runtime, o teste:

```text
test_knowledge_port_integration.py
```

foi adicionado a `Critical runtime regression suite`.

Commit:

- `83214703f5058b8f71e3b770bbb066ed303ff8fd` — `ci(knowledge): incluir integração do Knowledge Port na regressão crítica`.

O workflow desse head foi disparado e permanece pendente no momento desta atualizacao. A etapa nao sera marcada `VALIDATED` antes da conclusao verde.

## Relacao com External Capabilities / Jina

A Etapa 09 possui uma baseline local funcional e nao depende de Jina, Supabase ou pgvector para existir.

Expansoes futuras, depois da fundacao da Etapa 12, poderao adicionar:

```text
knowledge.embed
knowledge.rerank
documents.extract
documents.ocr
```

Regras arquiteturais:

- Jina e provider substituivel, nao fonte da semantica interna;
- embeddings tambem sao dados e obedecem privacy policy;
- documento sensivel nao pode ser enviado a OCR/embedding cloud por fallback silencioso;
- vector backend e substituivel;
- memoria pessoal continua separada de RAG;
- ausencia de provider externo nao invalida a capacidade local existente.

## Gate atual

- KnowledgePort SQLite real: **PASS**;
- isolamento documento x memoria pessoal: **PASS**;
- bootstrap sem NullKnowledgeAdapter: **PASS**;
- evidence injection no chat: **PASS**;
- configuracao do banco documentada: **PASS**;
- capability derivada do backend real: **PASS**;
- integracao Runtime dedicada: **IMPLEMENTED / CI EXPLICITA EM VALIDACAO**;
- CI final do head com regressao dedicada: **PENDING**.

## Criterio para encerramento

Marcar `ETAPA 09 — VALIDATED` somente quando o head que inclui `test_knowledge_port_integration.py` na regressao critica concluir com sucesso em:

- Python Core + Runtime contracts;
- Desktop frontend build;
- Tauri Rust check.

Jina/RAG vetorial permanecem expansoes planejadas e nao devem bloquear o fechamento da baseline local correta.