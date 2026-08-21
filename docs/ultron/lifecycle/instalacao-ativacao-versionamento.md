# ULTRON - Instalacao, Ativacao e Versionamento

## Estados

```text
discovered
  -> resolved
  -> downloaded
  -> verified
  -> installed
  -> activated
  -> deactivated
  -> removed
```

Estados de falha devem registrar motivo e permitir limpeza/rollback.

## Instalar nao e ativar

Instalacao somente disponibiliza artefatos verificados. Ativacao registra a capacidade no runtime do consumidor. Execucao continua sujeita a politica e aprovacao.

## Resolucao de dependencias

- semver;
- deteccao de ciclos;
- conflitos explicitos;
- lockfile por instalacao/ambiente;
- arvore reproduzivel;
- nenhuma atualizacao transitiva silenciosa.

## Ativacao

Antes de ativar:

- confirmar compatibilidade do runtime;
- revisar permissoes;
- registrar namespaces;
- detectar colisao de tools/skills;
- validar configuracao;
- criar checkpoint para rollback.

## Atualizacao

Atualizacoes mostram changelog, mudancas de permissao, migrations e impacto. Major versions nao sao ativadas automaticamente.

## Remocao

Remover deve verificar dependentes, desativar primeiro, preservar dados conforme politica e nunca apagar memoria compartilhada de outros namespaces.

## Rollback

Mantem lockfile anterior, artefatos necessarios e migrations reversiveis quando possivel. Se rollback nao for seguro, a atualizacao deve informar antes da autorizacao.

## Telemetria

Registrar install ID, versao, consumer, resultado de verificacao, ativacao, falhas e rollback sem coletar conteudo pessoal desnecessario.

