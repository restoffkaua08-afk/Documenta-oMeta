# Histórico de alterações do Jarvis

Este documento é o registro cronológico central das modificações realizadas no fork `restoffkaua08-afk/Jarvis-ia`. Cada item aponta para sua especificação técnica detalhada.

## Baseline

- Data: 2026-08-24
- Commit original: `759bfd0b98e9f0d391aa8482ca7b3c7dbd5bceea`
- Backend, CLI e frontend validados no Windows.
- Modelo local inicial: `qwen3.5:2b`.
- Extensão Rust não instalada devido à falha externa no download do toolchain.
- Desenvolvimento posterior autorizado diretamente na `main`.

## JARVIS-001 — finalização após limite de turnos

- Código: `9fa74fb4c2f972f75e4c4c321daf5ba3e0adc821`
- Teste: `3e4ee53df937995f17c2f4bace207b253844e5dd`
- Resultado: o orquestrador tenta sintetizar uma resposta sem ferramentas após esgotar `max_turns`.
- Documento: [JARVIS-001](alteracoes/JARVIS-001-finalizacao-limite-turnos.md)

## JARVIS-002 — aplicação efetiva das capacidades

- Executor: `7c7b71d62caca9a7aff995aed0a3431f922a9330`
- File read: `c411b417e83ddd35a6ae71cf32956c8343de9358`
- Testes: `ce45d4ae1c53950b37c42e3a802932d272f93c2a`
- Resultado: `ToolExecutor` aplica o mapa central como fallback e `file_read` declara `file:read`.
- Documento: [JARVIS-002](alteracoes/JARVIS-002-capacidades-ferramentas.md)

## JARVIS-003 — cobertura das ferramentas com efeitos

- Implementação: `8dc154baa6bb072d49f8e3383f0fdbe8944e80aa`
- Testes: `f38c367252fd0e10b2d4c5d01fe2ae6f1f8e1582`
- Resultado: cobertura central para arquivos, Git, rede, navegador, código, shell, banco, memória, canais, scheduler, agentes e skills.
- Documento: [JARVIS-003](alteracoes/JARVIS-003-cobertura-capacidades.md)

## JARVIS-004 — fallback RBAC sem Rust

- Implementação: `c113e0d43a6c4f134822caa574dc6afa9c40d6eb`
- Testes: `c5c29e7f501e050575c822a6a7dc4136afebb2cf`
- Resultado: ambientes sem extensão Rust utilizam a política Python em vez de desativar RBAC.
- Documento: [JARVIS-004](alteracoes/JARVIS-004-rbac-sem-rust.md)

## JARVIS-005 — RBAC ativo e configurável

- Configuração: `3b5064eca06779e3bba547137456d4cdd819f8fa`
- Inicialização: `b5ab009a607fea7f33bdc1e033a7b07be71c9928`
- Testes de configuração: `c95372806bed4388a1140adc6be958782ca5778d`
- Testes de integração: `8e2c8b56c2704439b5c376e08dbbe3b9c87dad3b`
- Resultado: RBAC passa a ser criado por padrão em modo compatível e aceita
  `default_deny` configurável.
- Documento: [JARVIS-005](alteracoes/JARVIS-005-rbac-ativo-configuravel.md)

## JARVIS-006 — agente de código no terminal

- Agente ReAct: `b7ac61eb8f1c81139c1fd64e84a884451d1d1386`
- Integração com chat: `18d054b4990ab8460c71ef40ae910fb63cc7fd11`
- Comando: `e06592f605aa7ac2946eda8f16cc2416b7bdc387`
- Registro na CLI: `6a43be6732e35daa3e3b6ac8c0360bd5bd61c923`
- Testes: `36972be137900212e4697bc8027ed719ab649a2d`
- Resultado: `jarvis code` reúne o agente ReAct, prompt de engenharia,
  ferramentas de arquivos, patch, shell e Git em um REPL especializado.
- Documento: [JARVIS-006](alteracoes/JARVIS-006-agente-codigo-terminal.md)

## JARVIS-007 — skills GitHub pelo chat

- Ferramentas: `d12284309c5be50c6f11fe6bd923330d762a1509`
- Registro: `4cf47ccb2a3bda8927ad3984ab007aeeb30f317f`
- Integração no Code: `f645eedf1b550f1d8b4d43cfb27db15dc07e2c9b`
- Proteção contra symlink: `d01ff03d822627590052fdd7cf95ff9ad5b03e81`
- Testes: `9ce30178b103a90b232215ed84d63ff1e1f4cfc4`,
  `a049aeeeef2f68ae4f9e28224eb185b912091b5f`
- Resultado: links públicos do GitHub podem ser analisados pelo chat e uma
  skill pode ser instalada após relatório e confirmação.
- Documento: [JARVIS-007](alteracoes/JARVIS-007-skills-github-chat.md)

## JARVIS-008 — gate de qualidade do agente de código

- Gate pós-edição: `73ca22d85967e07fc35bd64da8c06b4dc1821bcb`
- Roteamento no chat: `d39a967d31219c6e8fc0053657fbfe0ede9dbf87`
- Configuração Code: `cf25c1bbb3f145603a11d1cd59383986d90b5ad5`
- Testes: `a0a84eb56ee8f75b6aaaa347a804d2b5cdb3e718`,
  `f0c7cecbcb512f65013f18ab019e61491018a00e`
- Resultado: o modo Code usa `model_code`, 30 turnos por padrão e exige
  validação por shell mais revisão de diff após a última edição.
- Documento: [JARVIS-008](alteracoes/JARVIS-008-gate-qualidade-codigo.md)

## JARVIS-009 — sessões e diagnóstico Code

- Sessões: `e4d863148b8be09716ef46b80e1be9740f3f4834`
- Persistência no chat: `ec407c97246cad5da368957243e4ff7bab4b8f40`
- Opções de sessão: `951983d872c9d18f9be445ac76790e5429266471`
- Diagnóstico: `fd51a0008a7612c62d78a8252ea9b2986212b141`,
  `310bdcdb5f379f400af9c881bcb30e6d680a9a1a`
- Testes: `c7dedee6ce0387f00becc2db5dd24ecf981313de`,
  `95be61ac4ab36df5c095ce4fd485bb582abbbce9`,
  `4078ba8494a859fbac3b3aac23a08fa720b1a737`
- Resultado: conversas são retomadas por projeto e `jarvis code --check`
  valida o ambiente antes de iniciar o modelo.
- Documento:
  [JARVIS-009](alteracoes/JARVIS-009-sessoes-diagnostico-code.md)

## Estado atual

O trabalho permanece no gate `JARVIS_STANDALONE_READY`. O modo Code possui
roteamento especializado, gate pós-edição, skills auditáveis, sessões privadas
por projeto e preflight. Ainda faltam suíte integral, benchmarks reais,
validação no Windows e empacotamento antes da release.

## JARVIS-010 — benchmark isolado do agente de código

- Adicionada fixture Git descartável com defeito intencional e tarefa objetiva.
- Adicionado avaliador determinístico por testes, Git diff e escopo permitido.
- Adicionados `jarvis code --benchmark-prepare` e
  `jarvis code --benchmark-evaluate`.
- Relatório JSON e código de saída permitem integração futura em CI.
- Cobertura adicionada para sucesso, falha inicial, diretório inválido e mudança
  fora do escopo.
- Validação comportamental final: estado inicial 0/100; correção válida 100/100.
- Documento técnico:
  `alteracoes/JARVIS-010-benchmark-agente-codigo.md`.

## JARVIS-011 — instalação reproduzível no Windows

- Instalador direcionado por padrão ao repositório `Jarvis-ia`.
- Adicionados modos `-CliOnly`, `-SkipOllama` e repositório configurável.
- Acrescentada validação pós-instalação do CLI e do benchmark Jarvis Code.
- Criado desinstalador com confirmação, proteção contra caminhos amplos,
  limpeza do PATH e backup opcional de dados.
- Criado workflow Windows para parsing PowerShell e instalação isolada real.
- Documento técnico:
  `alteracoes/JARVIS-011-instalacao-windows.md`.
- Gate pendente: conclusão verde do workflow e validação na máquina de destino.

## JARVIS-012 — benchmark multiarquivo de engenharia

- Criado perfil `multifile` com contrato distribuído em dois módulos.
- Manifesto elevado para versão 2 com arquivos permitidos e obrigatórios.
- Relatório passou a expor perfil e arquivos obrigatórios ausentes.
- Pesos separados para testes, existência de diff, escopo e cobertura exigida.
- CLI recebeu `--benchmark-profile basic|multifile`.
- Validação comportamental: 0/100 inicial, 25/100 parcial e 100/100 coerente.
- Documento técnico:
  `alteracoes/JARVIS-012-benchmark-multiarquivo.md`.

## JARVIS-013 — gate obrigatório de inspeção antes da edição

- Detectado que o gate anterior validava o pós-edição, mas não o pré-editação.
- `file_write` e `apply_patch` são bloqueados antes de leitura e inspeção Git.
- O bloqueio não executa a mutação e retorna orientação recuperável ao modelo.
- Mantidas as exigências posteriores de `shell_exec` e `git_diff`.
- Testes cobrem tentativa prematura, recuperação e fluxo completo.
- Validação estática: AST válido e conformidade com 88 caracteres.
- Documento técnico:
  `alteracoes/JARVIS-013-gate-inspecao-previa.md`.

## JARVIS-014 — conexão segura com modelos avançados

- Corrigido o roteamento que escolhia o engine antes de conhecer o modelo.
- O chat agora revalida `can_serve` e seleciona backend compatível.
- Adicionado `jarvis code --strong`.
- Seleção por presença de credencial sem imprimir ou persistir seu valor.
- Suporte inicial a OpenAI, Anthropic, Google e OpenRouter.
- Instalador CLI do Windows inclui o extra `inference-cloud`.
- Testes cobrem seleção, precedência, credenciais vazias e mensagens seguras.
- Erro de assinatura encontrado durante revisão e corrigido antes da entrega.
- Documento técnico:
  `alteracoes/JARVIS-014-modelos-avancados.md`.

## DOC-JARVIS-001 — plano mestre de conclusão

- Data: 2026-08-28.
- Consolidado o escopo fechado da V1 em 24 etapas profissionais.
- Definidos estados, critérios globais de conclusão e seis marcos.
- Registrados 4 itens concluídos, 8 parciais e 12 pendentes.
- Definido o gate final `JARVIS_V1_STABLE` com aceite em Windows, benchmark,
  segurança, documentação, release e integração opcional com o Ultron.
- O número de commits deixou de ser usado como aproximação de progresso.
- Documento: [Plano mestre de conclusão](plano-mestre-conclusao.md).

## DOC-JARVIS-002 — incorporação de External Capabilities

- Data: 2026-08-28.
- Fonte transversal estudada integralmente:
  `docs/integracao/external-capabilities-public-apis.md`.
- O plano mestre passou de 24 para 29 etapas.
- Estado reclassificado para 4 concluídas, 10 parciais e 15 pendentes.
- Definidos gates próprios para núcleo Capability/Provider, operação resiliente
  de providers, research/evidências, threat intelligence e OpenAPI.
- Registradas dependências com RBAC, sandbox, modelos, contexto, skills,
  observabilidade, diagnóstico e benchmarks.
- Preservadas as fronteiras: Jarvis executa; ULTRON cataloga/distribui; Rachel
  governa a experiência pessoal; Zane consolida futuramente.
- APIs de domínio não essenciais permanecem fixtures ou packs opcionais, sem
  inflar o core do Jarvis.
- Documento técnico:
  [Capability/Provider](../integracao/capability-provider-public-apis.md).
- Roadmap: [Plano mestre](plano-mestre-conclusao.md).
