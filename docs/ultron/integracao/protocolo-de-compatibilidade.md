# ULTRON - Protocolo de Compatibilidade

## Objetivo

Permitir que diferentes consumidores usem capacidades ULTRON sem acoplamento ao codigo interno do registry.

## Consumer Adapter

Interface conceitual:

```text
get_capabilities()
check_compatibility(manifest)
install(pack, version)
activate(id)
deactivate(id)
remove(id)
list_installed()
get_status(id)
```

## Negociacao

Consumidor informa:

- protocol version;
- runtimes suportados;
- schema versions;
- tool/skill APIs;
- politica e permissoes;
- plataformas/arquiteturas;
- limites.

ULTRON devolve compatibilidade, warnings, dependencias, permissoes e plano de instalacao. Nenhuma incompatibilidade critica deve ser ignorada silenciosamente.

## Adapters

### Rachel

Uso experimental para instalar/ativar capacidades sem afetar identidade, memoria pessoal ou Cyber. Rachel continua standalone.

### Jarvis

Registra agents/skills/workflows compativeis no runtime. Jarvis continua funcional com registry local/nativo.

### Zane

Consumidor principal futuro. Zane possui capacidades nativas e utiliza ULTRON apenas para extensao.

## Falhas

- ULTRON offline: usar capacidades ja instaladas conforme politica;
- artifact indisponivel: nao criar estado parcial;
- incompatibilidade: bloquear ativacao;
- dependencia ausente: resolver ou falhar claramente;
- pack comprometido: quarentena e aviso;
- adapter falha: preservar runtime nativo.

## Versionamento

Protocol, schemas e packages possuem versoes independentes. Consumers declaram intervalo suportado e devem rejeitar breaking changes desconhecidas.

