# Correção JARVIS-003 — cobertura central das ferramentas com efeitos

## Registro

- Data: 2026-08-24
- Repositório: `restoffkaua08-afk/Jarvis-ia`
- Branch: `main`
- Implementação: `8dc154baa6bb072d49f8e3383f0fdbe8944e80aa`
- Testes: `f38c367252fd0e10b2d4c5d01fe2ae6f1f8e1582`

## Objetivo

Completar a defesa introduzida em JARVIS-002. O executor passou a consultar `DEFAULT_TOOL_CAPABILITIES`, mas a tabela original continha somente parte das ferramentas embutidas. Ferramentas com efeitos equivalentes poderiam receber proteção diferente apenas por omissão de metadados.

## Cobertura adicionada

### Arquivos e Git

- leitura: `file_read`, `pdf_extract`, `audio_transcribe`, `git_status`, `git_diff`, `git_log`;
- escrita: `file_write`, `apply_patch`, `git_commit`.

### Rede e navegador

- `web_search`;
- `http_request`;
- `browser_navigate`;
- `browser_click`;
- `browser_type`;
- `browser_screenshot`;
- `browser_extract`;
- `browser_axtree`;
- `image_generate`.

### Execução

- `code_interpreter`;
- `code_interpreter_docker`;
- `shell_exec`;
- `docker_shell_exec`;
- `repl`;
- `db_query`.

### Memória e conhecimento

Foram classificados separadamente os recursos de leitura e escrita para armazenamento, retrieval, busca de conhecimento e knowledge graph.

### Comunicação e administração

- envio por canais;
- criação de agendamentos;
- criação, comunicação, listagem e encerramento de agentes;
- gerenciamento de skills.

`skill_manage` exige simultaneamente `system:admin` e `file:write`, porque pode alterar capacidades instaladas e arquivos persistentes.

## Decisão sobre docker_shell_exec

A ferramenta executa somente dentro do container ativo do TerminalBench e recusa operação sem esse contexto. Seu uso é voltado a avaliações automatizadas não interativas. Alterar `requires_confirmation` para verdadeiro faria o executor negar todas essas execuções quando não houvesse callback humano.

A configuração foi preservada, mas a capacidade `code:execute` passou a ter cobertura também no mapa central. O isolamento do container não substitui RBAC; ambos permanecem necessários.

## Testes

Os testes verificam uma amostra representativa de cada classe de risco e confirmam o requisito duplo de `skill_manage`.

## Compatibilidade

Nenhuma interface pública foi modificada. A alteração afeta somente execuções que usam uma `CapabilityPolicy`. Sem política configurada, o comportamento existente é preservado. Com política deny-by-default, ferramentas mapeadas exigem concessão explícita.
