# ULTRON - Validacao, Sandbox e Supply Chain

## Modelo de ameaca

ULTRON recebe manifests e artefatos de publishers. Eles sao nao confiaveis ate passarem por validacao. Popularidade nao substitui seguranca.

## Pipeline de seguranca

```text
submission
  -> identity/provenance
  -> schema
  -> integrity/signature
  -> dependency and license scan
  -> static inspection
  -> sandbox tests when allowed
  -> risk report
  -> publish/quarantine/reject
```

## Sandbox

- sem credenciais reais;
- filesystem efemero e confinado;
- rede bloqueada ou allowlist;
- CPU, memoria e tempo limitados;
- nenhum acesso ao host/consumer;
- saida e artifacts limitados;
- destruicao do ambiente apos teste.

## Permissoes

Manifest declara permissoes solicitadas. O consumidor pode negar, reduzir ou exigir aprovacao. Pack nao pode solicitar `approve-all` nem alterar policy engine.

## Supply chain

- hashes imutaveis;
- assinaturas quando suportadas;
- SBOM/dependencias;
- publisher verificavel;
- revogacao/quarentena;
- aviso de vulnerabilidade;
- lockfiles;
- reproducibilidade.

## Hooks

Hooks de install/update sao alto risco. Preferir declaracoes e migrations restritas. Codigo arbitrario nunca executa automaticamente no host durante instalacao.

## Incidentes

Registry deve permitir despublicar de novas instalacoes, marcar versao comprometida e notificar consumidores. Desativacao remota automatica precisa de politica explicita; nao pode conceder controle irrestrito ao registry.

