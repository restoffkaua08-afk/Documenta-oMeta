# Jarvis - Tools, Skills e MCP

## Tool Registry

Cada tool deve possuir schema, efeito, risco, timeout, escopo, idempotencia, politica de retry e executor. A existencia de uma funcao Python nao e suficiente para publica-la ao modelo.

## Capability e Provider

Uma capability representa a operação lógica pedida pelo agente. Uma tool ou
provider representa uma implementação possível. O planner não deve construir
HTTP arbitrário quando existir um adapter aprovado.

- Capability Registry: contrato, versão, schemas, efeitos e policy;
- Provider Registry: implementação, auth, domínios, health, custo e quota;
- Provider Router: seleção por capacidade, privacidade, disponibilidade e custo;
- Provider Gateway: execução, erros normalizados, retry, ledger e redaction.

Especificação completa:
[Capability/Provider](../integracao/capability-provider-public-apis.md).

## Dominios planejados

- filesystem;
- terminal/processos;
- Git;
- browser;
- pesquisa web;
- codigo e sandbox;
- MCP;
- APIs externas;
- bancos e conhecimento;
- desktop/sistema operacional.

## Terminal

Terminal e superficie critica. Deve usar sandbox/perfis, allowlists quando possivel, diretorio confinado, limites, captura de saida e aprovacao. Comandos compostos e shells exigem politica mais forte.

## Browser

Navegacao de leitura e acoes com efeito devem ser distintas. Login, envio, compra, publicacao, upload e alteracao de dados precisam de autorizacao contextual.

## Skills

Tipos possiveis:

- prompt skill;
- tool skill;
- pipeline skill;
- agent skill;
- workflow declarativo.

Uma skill nao ganha privilegios adicionais. Ela somente compoe capacidades ja autorizadas.

## MCP

O runtime MCP deve controlar:

- registry de servidores;
- transporte e lifecycle;
- schemas importados;
- namespaces e colisao de nomes;
- timeouts/cancelamento;
- credenciais;
- risco de cada tool remota;
- auditoria de invocacoes;
- isolamento de servidor nao confiavel.

## Descoberta

Tools e skills podem ser descobertas dinamicamente, mas ativacao e permissoes devem ser separadas. Instalar nao significa ativar; ativar nao significa aprovar cada efeito.

## Resultados

Tool results precisam de estado, dados sanitizados, evidencias, duracao, erro tipado e metadados de origem. Conteudo retornado por tools permanece nao confiavel para fins de instrucoes.

## Integracao futura com ULTRON

ULTRON podera fornecer manifests e packs. Jarvis deve validar compatibilidade e seguranca antes de registrar capacidades. O runtime continua funcional sem ULTRON.

Manifests vindos do ULTRON ou de OpenAPI entram desativados. Descobrir, baixar,
instalar, registrar, ativar e autorizar efeitos são decisões separadas.
