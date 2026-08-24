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

## Estado atual

O trabalho permanece no gate `JARVIS STANDALONE_READY`. O RBAC está ativo
em modo compatível e o primeiro modo `jarvis code` está implementado. Ainda
faltam execução integral da suíte, teste com modelo real, perfis mínimos,
diagnóstico específico e empacotamento Windows antes de uma release.
