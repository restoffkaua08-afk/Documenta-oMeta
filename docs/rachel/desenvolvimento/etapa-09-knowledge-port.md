# Rachel — Etapa 09: Knowledge Port real

**Estado:** Em implementacao incremental  
**Data:** 2026-08-26  
**Repositorio:** `restoffkaua08-afk/rachel-ia`  
**Branch oficial:** `main`

## Objetivo

Conectar o Core da Rachel ao conhecimento documental governado que a Visao ja indexa no runtime. Antes desta etapa, `build_container()` usava `NullKnowledgeAdapter`, portanto `ChatService._system_prompt()` consultava um port de conhecimento que sempre retornava vazio.

O objetivo nao e misturar memoria pessoal com RAG. O Knowledge Port deve expor somente chunks documentais aprovados e indexados pela Visao.

## Auditoria do estado existente

A auditoria confirmou:

- `KnowledgePort` do Core exige `search(query, limit)`;
- `ChatService` ja possui o ponto correto de injecao de evidencia em `_system_prompt()`;
- `VisaoIngestor` extrai documentos por `DocumentExtractor`;
- os chunks aprovados sao armazenados por `CognitiveMemory.remember()`;
- esses registros ficam na tabela `cognitive_memories`;
- chunks de documentos possuem `metadata.kind=document_chunk`;
- memorias pessoais, preferencias e decisoes compartilham a mesma tabela, portanto o adapter precisa filtrar explicitamente os chunks documentais;
- o bootstrap do Core ainda utilizava `NullKnowledgeAdapter`.

## Implementacao realizada

### SQLiteKnowledgeAdapter

Foi criado:

- `RACHEL_CORE/src/rachel_core/adapters/knowledge_sqlite.py`.

Commit:

- `d22a2ecb829b52b076462589fbe0b9d39e19f196` — `feat(knowledge): adicionar adapter SQLite para evidencias documentais`.

Caracteristicas:

- implementa o contrato `KnowledgePort.search()`;
- leitura somente do banco governado de Bran/Visao;
- filtra `status=active`;
- filtra estritamente `metadata.kind=document_chunk`;
- nao devolve preferencias, instrucoes ou outras memorias pessoais como evidencia de RAG;
- ranking lexical simples acrescido de confidence/importance;
- limite defensivo de resultados;
- banco inexistente retorna lista vazia sem quebrar chat;
- `status()` informa backend, existencia do banco e quantidade de chunks documentais;
- erro SQLite e tratado como indisponibilidade do adapter em vez de crash silencioso do processo.

### Testes do adapter

Foi criado:

- `RACHEL_CORE/tests/test_knowledge_sqlite.py`.

Commit:

- `4e497f8b0c2eeff4c4b0d73acb8a3536d9fbe79e` — `test(knowledge): validar busca documental SQLite`.

Os testes comprovam:

- busca retorna chunks documentais relevantes;
- memoria pessoal contendo os mesmos termos nao vaza para o Knowledge Port;
- ranking seleciona o chunk mais relevante;
- status conta somente documentos;
- ausencia do arquivo de banco e segura.

### Bootstrap conectado ao adapter real

`RACHEL_CORE/src/rachel_core/bootstrap.py` deixou de instanciar `NullKnowledgeAdapter` e passou a construir `SQLiteKnowledgeAdapter`.

Commit:

- `7e017d5c22b7ed18dac91d071a4f14f72147d557` — `feat(knowledge): conectar KnowledgePort SQLite ao Core`.

A resolucao do banco usa:

1. `RACHEL_KNOWLEDGE_DB_PATH` quando configurado;
2. caso contrario, o banco `bran-cognitive.db` irmao do `RACHEL_HOME/core`, que corresponde ao layout de estado usado pelo runtime.

O `Container` passou tambem a expor explicitamente `knowledge`, deixando o backend selecionado auditavel sem depender de internals do `ChatService`.

### Prova de evidencia chegando ao chat

Foi criado:

- `RACHEL_CORE/tests/test_knowledge_chat_integration.py`.

Commit:

- `484b4a1eb11bea79647dea1aee9b54e8270b1259` — `test(knowledge): provar evidencias documentais no chat`.

O teste cria um chunk documental no schema real de `cognitive_memories`, usa o `SQLiteKnowledgeAdapter` com um modelo de gravacao e executa `ChatService.chat()`.

O gate exige que o system prompt entregue ao modelo contenha:

- a secao `Evidencias recuperadas`;
- o conteudo do chunk relevante;
- a origem documental;
- nenhum documento irrelevante quando a consulta nao possui match.

## Trabalho ainda necessario

1. confirmar CI dos commits da etapa;
2. tornar `capabilities.knowledge` derivado do adapter real no status do Core, em vez de um valor fixo;
3. remover a sobreposicao enganosa `knowledge=True` no bridge cognitivo quando a fonte real nao estiver disponivel;
4. documentar/configurar `RACHEL_KNOWLEDGE_DB_PATH` em `.env.example`;
5. adicionar teste de bootstrap garantindo que o Core usa `SQLiteKnowledgeAdapter`;
6. validar o fluxo Visao -> CognitiveMemory -> KnowledgePort -> ChatService com um teste de integracao de runtime sempre que isso puder ser feito sem criar dependencia circular entre Core e Runtime;
7. somente entao marcar o gate da Etapa 09 como validado.

## Gate atual

- KnowledgePort SQLite real: **IMPLEMENTED / CI PENDING**;
- isolamento entre conhecimento documental e memoria pessoal: **IMPLEMENTED / TESTED**;
- bootstrap sem NullKnowledgeAdapter: **IMPLEMENTED / CI PENDING**;
- documentos recuperados como evidencia no chat: **IMPLEMENTED / TESTED / CI PENDING**;
- `capabilities.knowledge` refletindo verdade: **PENDING**;
- CI final da Etapa 09: **PENDING**.
