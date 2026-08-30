# JARVIS-015 — segurança efetiva no runtime de Chat e Code

## Resumo executivo

O comando `jarvis code` reutiliza o runtime de `jarvis chat`. A revisão técnica
identificou que esse caminho criava o agente e suas ferramentas sem executar
`setup_security()` e sem encaminhar a `CapabilityPolicy` ao executor de
ferramentas. Assim, configurações de RBAC, scanners e auditoria aplicadas em
outros entrypoints podiam não proteger a sessão interativa de código.

O JARVIS-015 elimina esse desvio. Chat e Code agora inicializam a mesma esteira
de segurança usada pelos demais entrypoints. Além disso, o modo Code com gate
de qualidade recebe uma política deny-by-default gerada a partir das
ferramentas efetivamente selecionadas para a sessão.

## Problema comprovado

Antes desta alteração, o runtime interativo seguia esta sequência:

1. carregava configuração, engine e modelo;
2. resolvia agente e ferramentas;
3. instanciava o agente diretamente;
4. iniciava o REPL.

Não havia chamada a `setup_security()` nesse caminho. Como consequência:

- o engine podia permanecer sem o wrapper de guardrails configurado;
- a política de capacidades podia não chegar ao `ToolExecutor`;
- `jarvis code`, por delegar a `jarvis chat`, herdava a mesma lacuna;
- a existência do mapa central de capacidades não bastava para garantir sua
  aplicação no runtime usado pelo agente de programação.

## Implementação

### Esteira compartilhada

Depois de resolver engine, modelo e opções de runtime, `chat_cmd.py` chama
`setup_security(config, engine, bus)`. O engine retornado pela esteira passa a
ser o engine efetivamente usado pela sessão.

Quando o agente aceita o parâmetro `capability_policy`, a política resolvida é
encaminhada ao construtor. Agentes simples continuam compatíveis e não recebem
argumentos que sua assinatura não declara.

### Política limitada às ferramentas selecionadas

Foi introduzido `build_tool_scoped_policy(agent_id, tools)`. O builder:

1. cria `CapabilityPolicy(default_deny=True)`;
2. lê `ToolSpec.required_capabilities` de cada ferramenta;
3. usa `DEFAULT_TOOL_CAPABILITIES` como fallback central;
4. concede cada capacidade somente para o nome exato da ferramenta.

O escopo exato evita propagação lateral. Selecionar `file_read`, por exemplo,
autoriza `file:read` para o recurso `file_read`, mas não autoriza
automaticamente `git_status` ou qualquer outra ferramenta que compartilhe a
mesma capacidade.

Ferramentas com múltiplas capacidades continuam exigindo todas elas. A
instalação de skills por repositório mantém `network:fetch`, `file:write` e
`system:admin`, restritas ao recurso `skill_repo_install` e combinadas com a
confirmação interativa já existente.

### Compatibilidade

- `jarvis chat` sem gate continua respeitando a política configurada pelo
  operador;
- `jarvis code` usa a política de sessão limitada ao conjunto de ferramentas;
- ferramentas não selecionadas permanecem negadas;
- agentes sem suporte a ferramentas permanecem funcionais;
- Rust continua opcional para a política, graças ao fallback Python entregue
  no JARVIS-004.

## Modelo de ameaça coberto

| Ameaça | Controle introduzido |
|---|---|
| Runtime Code ignora RBAC | política encaminhada ao agente |
| Chat ignora guardrails | `setup_security()` obrigatório no entrypoint |
| Capacidade ampla por categoria | grant restrito ao nome exato da ferramenta |
| Ferramenta omitida recebe acesso | `default_deny=True` no perfil Code |
| ToolSpec omite capacidade | fallback no mapa central |
| Skill remota amplia privilégios | três capacidades restritas ao instalador e confirmação |

## Validação executada

Commit do Jarvis: `0b6224c61297f4c51470e4121f4c2424e89084b3`.

Resultados do conjunto diretamente afetado:

- 96 testes aprovados;
- 1 teste ignorado por dependência opcional;
- Ruff aprovado;
- formatação Ruff aprovada;
- `git diff --check` aprovado.

Os testes cobrem:

- inicialização real da esteira compartilhada pelo comando Chat;
- preservação da política configurada no Chat comum;
- deny-by-default no modo com gate;
- grant por recurso exato;
- negação de ferramentas não selecionadas;
- ferramenta com múltiplas capacidades;
- confirmação de ferramenta perigosa;
- fluxo do agente ReAct e instalação segura de skills.

## Limitação encontrada na suíte ampliada

Uma execução ampliada produziu 471 aprovações, 14 skips e 50 falhas. As falhas
não partiram dos arquivos alterados: scanners, guardrails e rate limiter tentam
importar `openjarvis_rust` em um ambiente onde a extensão não está instalada;
um teste de `code doctor` também depende do registro global de ferramentas.

Esse resultado não autoriza declarar a suíte completa verde. Ele registra uma
dívida de portabilidade: componentes de segurança além do RBAC ainda precisam
de fallback, skip explícito ou instalação Rust garantida no job correspondente.

## Impacto no plano mestre

O JARVIS-015 fortalece as etapas 03 e 05, mas não as conclui:

- etapa 03 continua parcial até todas as esteiras obrigatórias passarem no
  mesmo commit e as dependências opcionais estarem corretamente matriciadas;
- etapa 05 continua parcial até completar testes de sandbox, caminhos, rede,
  timeout, cancelamento e kill switch;
- etapas 06 e 07 preservam o status concluído, agora com a segurança realmente
  conectada ao runtime que executa o protocolo de engenharia.

## Próximas dependências

1. definir matriz CI Python-only e Rust-enabled;
2. decidir fallback ou requisito explícito de Rust para scanners e rate limit;
3. testar deny-by-default com todas as ferramentas do perfil Code;
4. adicionar testes de travessia de diretório, rede, timeout e cancelamento;
5. validar o fluxo em Windows com modelo real antes de promover a etapa 05.
