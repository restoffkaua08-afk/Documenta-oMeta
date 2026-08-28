# Jarvis — Plano mestre de conclusão

## Objetivo

Este documento define o caminho fechado para transformar o fork do OpenJarvis
em um produto standalone estável, instalável no Windows e capaz de atuar como
agente de engenharia de software no terminal.

O plano possui **24 etapas**. Quando todas estiverem aprovadas, o Jarvis poderá
ser classificado como `JARVIS_V1_STABLE`. Novas ideias depois disso serão
evolução de produto, não requisitos ocultos para considerar a versão 1 pronta.

“Pronto” não significa ausência absoluta de defeitos, algo que nenhum software
profissional pode garantir. Significa que o escopo definido aqui passou por
critérios objetivos, testes reproduzíveis e validação no computador de destino.

## Como o progresso é calculado

Cada etapa possui peso igual no indicador executivo:

- `CONCLUÍDA`: todos os critérios de aceite possuem evidência;
- `PARCIAL`: existe implementação relevante, mas falta ao menos um gate;
- `PENDENTE`: ainda não existe uma entrega suficiente para o gate;
- `BLOQUEADA`: depende de acesso, hardware, credencial ou decisão externa.

Somente etapas `CONCLUÍDAS` contam no percentual oficial. O progresso não é
calculado pelo número de commits ou de arquivos criados.

## Estado executivo em 28 de agosto de 2026

| Indicador | Estado |
|---|---:|
| Etapas concluídas | 4 de 24 |
| Etapas parciais | 8 de 24 |
| Etapas pendentes | 12 de 24 |
| Progresso oficial | 16,7% |
| Melhorias rastreadas | JARVIS-001 a JARVIS-014 |
| Gate atual | Fundação técnica e modo Code |
| Release estável | Ainda não autorizada |

O percentual é deliberadamente conservador. As entregas JARVIS-001 a
JARVIS-014 criaram partes importantes, mas várias só podem ser aprovadas após
execução integral da suíte, uso de modelos reais e teste no Windows final.

## Definição global de concluído

Uma etapa só pode mudar para `CONCLUÍDA` quando possuir, quando aplicável:

1. implementação integrada à `main`;
2. testes unitários e de integração aprovados;
3. validação nos sistemas operacionais declarados;
4. segurança e tratamento de falhas revisados;
5. documentação operacional e técnica atualizada;
6. commits e resultados de CI registrados como evidência;
7. nenhuma pendência crítica ou alta conhecida dentro do escopo;
8. procedimento de reversão ou recuperação documentado.

## Marco A — Fundação confiável

### Etapa 01 — Baseline, origem e licença

**Estado:** `CONCLUÍDA`

**Escopo:** preservar a origem do OpenJarvis, registrar o commit inicial,
licença, fork e condições de atualização com o upstream.

**Aceite:** baseline recuperável; origem e licença documentadas; alterações do
Meta distinguíveis do upstream.

**Evidência atual:** baseline
`759bfd0b98e9f0d391aa8482ca7b3c7dbd5bceea` e documentação de origem.

### Etapa 02 — Configuração reproduzível

**Estado:** `PARCIAL`

**Escopo:** Python suportado, lockfiles, extras, exemplos de configuração,
segredos fora do Git, instalação limpa e comandos de desenvolvimento.

**Falta para concluir:** repetir instalação do zero em Windows limpo; validar
dependências do modo CLI e cloud; registrar versões e tempo de instalação.

### Etapa 03 — Suíte e integração contínua

**Estado:** `PARCIAL`

**Escopo:** lint, tipos quando aplicável, testes unitários, integração, CLI,
frontend, instaladores e artefatos em CI.

**Falta para concluir:** obter todas as esteiras obrigatórias verdes no mesmo
commit; remover testes instáveis; publicar matriz de cobertura e exceções.

### Etapa 04 — Segurança de capacidades e RBAC

**Estado:** `CONCLUÍDA`

**Escopo:** mapa central de capacidades, fallback Python sem Rust, negações,
escopo de recursos e RBAC ativo/configurável.

**Evidência atual:** JARVIS-002 a JARVIS-005.

### Etapa 05 — Sandbox, aprovações e limites operacionais

**Estado:** `PARCIAL`

**Escopo:** confirmação de ações perigosas, isolamento de shell/código,
restrição de caminhos, rede, orçamento, timeout, cancelamento e kill switch.

**Falta para concluir:** matriz completa por ferramenta; testes de fuga de
diretório, comandos destrutivos, rede e cancelamento; perfil deny-by-default
pronto para uso real.

## Marco B — Agente de código competente

### Etapa 06 — Terminal interativo `jarvis code`

**Estado:** `CONCLUÍDA`

**Escopo:** REPL de código, ferramentas de arquivo, patch, shell e Git, prompt
especializado e limite controlado de turnos.

**Evidência atual:** JARVIS-001 e JARVIS-006.

### Etapa 07 — Protocolo obrigatório de engenharia

**Estado:** `CONCLUÍDA`

**Escopo:** inspecionar antes de editar; validar após edição; revisar o diff;
impedir conclusão prematura; relatar evidências e limites com honestidade.

**Evidência atual:** JARVIS-008 e JARVIS-013.

### Etapa 08 — Modelos fortes e roteamento resiliente

**Estado:** `PARCIAL`

**Escopo:** modelo especializado em código, provedores cloud e local, seleção
por capacidade, fallback, retries, limites de custo e contexto.

**Falta para concluir:** chamadas reais para cada provedor suportado; fallback
testado sob falha; diagnóstico de credenciais; orçamento configurável; matriz
de modelos recomendados validada por benchmark.

### Etapa 09 — Contexto de repositório e recuperação inteligente

**Estado:** `PENDENTE`

**Escopo:** indexação incremental, busca semântica e lexical, mapa de símbolos,
priorização de arquivos, compactação de histórico e invalidação por mudanças.

**Aceite:** trabalhar em repositório grande sem enviar toda a árvore ao modelo;
recuperar arquivos relevantes com métricas de precisão; não usar contexto
obsoleto depois de alterações.

### Etapa 10 — Planejamento hierárquico e checkpoints

**Estado:** `PENDENTE`

**Escopo:** decompor sistemas em épicos, tarefas e critérios de aceite;
checkpoint por etapa; retomada após falha; replanejamento baseado em evidência.

**Aceite:** interromper e retomar tarefa multietapa sem perder estado; detectar
dependências e bloquear conclusão enquanto critérios obrigatórios faltarem.

### Etapa 11 — Revisão independente e agentes especializados

**Estado:** `PENDENTE`

**Escopo:** agente implementador, revisor e testador com papéis isolados;
consenso ou arbitragem; limites de paralelismo e custo.

**Aceite:** revisão não reutiliza cegamente a justificativa do implementador;
falhas encontradas geram correção e nova validação; loops possuem limite.

### Etapa 12 — Qualidade de código e arquitetura

**Estado:** `PENDENTE`

**Escopo:** detectar linguagem e framework; executar lint, tipos, testes,
build, análise de dependências e verificações específicas do projeto.

**Aceite:** presets comprovados para Python, JavaScript/TypeScript, React ou
Next.js, C#/.NET e Java; comandos nunca são inventados sem inspecionar o repo.

## Marco C — Ferramentas, skills e integrações

### Etapa 13 — Skills auditáveis por repositório

**Estado:** `PARCIAL`

**Escopo:** inspecionar URL GitHub, commit, manifests, scripts e capacidades;
confirmar instalação; registrar origem; atualizar e remover com segurança.

**Falta para concluir:** teste fim a fim com repositórios reais; assinatura ou
hash de integridade; política de atualização; rollback e remoção comprovados;
tratamento de repositório privado por credencial segura.

### Etapa 14 — MCP e ferramentas externas

**Estado:** `PENDENTE`

**Escopo:** cliente MCP compatível, descoberta, autorização por servidor,
timeouts, cancelamento, schemas, auditoria e isolamento de falhas.

**Aceite:** conectar ao menos dois servidores de referência; uma falha externa
não derruba a sessão; permissões são exibidas antes do uso.

### Etapa 15 — Navegador e pesquisa governados

**Estado:** `PENDENTE`

**Escopo:** pesquisa web, navegação, downloads, proteção contra prompt
injection, lista de domínios e rastreabilidade das fontes.

**Aceite:** tarefas públicas reais aprovadas; conteúdo web não altera políticas
do agente; downloads e credenciais respeitam o sandbox.

### Etapa 16 — Integração Ultron

**Estado:** `PENDENTE`

**Escopo:** autenticação, catálogo de agentes/skills, execução remota, eventos,
versionamento e degradação quando o Ultron estiver indisponível.

**Aceite:** Jarvis permanece standalone; conexão e desconexão não corrompem
sessões; contratos e permissões possuem testes de compatibilidade.

### Etapa 17 — Contratos Rachel e futuro Zane

**Estado:** `PENDENTE`

**Escopo:** adapters opcionais e versionados, sem incorporar prematuramente os
núcleos; eventos, memória, identidade e governança com limites claros.

**Aceite:** nenhuma dependência circular; Jarvis funciona sem Rachel; adapter
possui testes; decisões de fusão ficam reservadas ao projeto Zane.

## Marco D — Memória, operação e experiência

### Etapa 18 — Sessões e memória por projeto

**Estado:** `PARCIAL`

**Escopo:** retomada privada por projeto, limites, migração de schema, resumo,
backup, exclusão e separação entre conversa e memória duradoura.

**Falta para concluir:** testes de migração e corrupção; comando de inspeção;
política de retenção; memória longa com consentimento e escopo explícito.

### Etapa 19 — Observabilidade, auditoria e custos

**Estado:** `PENDENTE`

**Escopo:** logs estruturados, trace por tarefa, uso de tokens, latência,
custos, chamadas de ferramentas, decisões de aprovação e dados sensíveis.

**Aceite:** diagnóstico exportável sem segredos; correlação ponta a ponta;
limites e alertas configuráveis; rotação e retenção documentadas.

### Etapa 20 — UX profissional do terminal

**Estado:** `PENDENTE`

**Escopo:** onboarding, ajuda contextual, progresso, streaming, diff legível,
aprovações, cancelamento, atalhos, acessibilidade e mensagens acionáveis.

**Aceite:** fluxo completo sem editar TOML manualmente; erros dizem causa e
próxima ação; Ctrl+C recupera o terminal e preserva estado consistente.

### Etapa 21 — Diagnóstico e autorrecuperação

**Estado:** `PARCIAL`

**Escopo:** `jarvis code --check`, saúde de modelo, engine, Git, ferramentas,
configuração, armazenamento, rede e sugestões seguras de reparo.

**Falta para concluir:** códigos de saída documentados; modo JSON; diagnóstico
de provedores e Ultron; testes de falhas comuns e reparos reversíveis.

## Marco E — Prova de competência

### Etapa 22 — Benchmark completo de engenharia

**Estado:** `PARCIAL`

**Escopo:** tarefas basic e multifile, backend, frontend, banco, integração,
depuração, segurança, refatoração e projeto iniciado do zero.

**Falta para concluir:** executar o próprio Jarvis com modelos reais; definir
amostra repetida; medir sucesso, regressão, custo e tempo; impedir vazamento das
soluções esperadas; publicar resultados reproduzíveis.

**Meta mínima:** pelo menos 80% de aprovação global e 100% nos casos críticos
de segurança e preservação de dados, em três execuções por cenário prioritário.

### Etapa 23 — Testes de sistema e estabilidade prolongada

**Estado:** `PENDENTE`

**Escopo:** instalação limpa, upgrade, desinstalação, interrupção, retomada,
rede instável, provider indisponível, repositórios grandes e sessões longas.

**Aceite:** zero perda de dados conhecida; zero falha crítica; consumo de
recursos dentro dos limites documentados; execução prolongada registrada.

## Marco F — Distribuição e aceite final

### Etapa 24 — Instalador, máquina final e release V1

**Estado:** `PENDENTE`

**Escopo:** artefato Windows versionado, verificação de integridade, instalação,
upgrade, rollback, desinstalação, manual e teste de aceite no computador final.

**Pré-requisitos:** etapas 01 a 23 concluídas.

**Aceite final:**

- workflow Windows verde no commit da release;
- instalação em Windows limpo aprovada;
- `jarvis code --check` aprovado;
- modelo local e ao menos um provedor forte comprovados;
- benchmark completo dentro da meta;
- projeto real criado, testado e revisado pelo Jarvis;
- Ultron opcional conecta sem comprometer o modo standalone;
- documentação de instalação, uso, segurança e recuperação revisada;
- release assinada, versionada e acompanhada de changelog;
- nenhuma vulnerabilidade crítica ou alta conhecida sem mitigação.

Ao cumprir esses itens, o commit recebe o gate `JARVIS_V1_STABLE`.

## Ordem de execução recomendada

A sequência oficial é:

1. concluir 02, 03 e 05 para estabilizar a fundação;
2. concluir 08 e implementar 09 a 12 para elevar competência;
3. concluir 13 e implementar 14 a 17 para integrações;
4. concluir 18 e 21, implementar 19 e 20 para operação;
5. concluir 22 e 23 com evidência real;
6. executar 24 somente depois de todos os gates anteriores.

Etapas independentes podem avançar em paralelo, mas nenhuma será marcada como
concluída fora da definição global de concluído.

## Painel resumido

| Etapa | Nome | Estado |
|---:|---|---|
| 01 | Baseline, origem e licença | CONCLUÍDA |
| 02 | Configuração reproduzível | PARCIAL |
| 03 | Suíte e integração contínua | PARCIAL |
| 04 | Segurança de capacidades e RBAC | CONCLUÍDA |
| 05 | Sandbox, aprovações e limites | PARCIAL |
| 06 | Terminal `jarvis code` | CONCLUÍDA |
| 07 | Protocolo obrigatório de engenharia | CONCLUÍDA |
| 08 | Modelos fortes e roteamento | PARCIAL |
| 09 | Contexto de repositório | PENDENTE |
| 10 | Planejamento e checkpoints | PENDENTE |
| 11 | Revisão independente | PENDENTE |
| 12 | Qualidade e arquitetura | PENDENTE |
| 13 | Skills por repositório | PARCIAL |
| 14 | MCP e ferramentas externas | PENDENTE |
| 15 | Navegador e pesquisa | PENDENTE |
| 16 | Integração Ultron | PENDENTE |
| 17 | Contratos Rachel/Zane | PENDENTE |
| 18 | Sessões e memória | PARCIAL |
| 19 | Observabilidade e custos | PENDENTE |
| 20 | UX do terminal | PENDENTE |
| 21 | Diagnóstico e recuperação | PARCIAL |
| 22 | Benchmark completo | PARCIAL |
| 23 | Estabilidade prolongada | PENDENTE |
| 24 | Instalador e release V1 | PENDENTE |

## Regra de manutenção

Toda entrega futura deve atualizar este painel quando alterar evidências ou
estado de uma etapa. O histórico cronológico continuará registrando commits e
decisões; este plano permanecerá como a fonte de verdade do progresso até a V1.
