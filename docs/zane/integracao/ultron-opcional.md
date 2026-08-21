# Zane - Integracao Opcional com ULTRON

## Principio

Zane possui agentes, tools e skills nativos suficientes para cumprir seu escopo. ULTRON adiciona capacidades, mas nao fornece o nucleo do produto.

## Fluxo

```text
Zane Extension Manager
  -> consulta ULTRON
  -> verifica manifest/compatibilidade
  -> apresenta permissoes
  -> instala artefato
  -> valida localmente
  -> ativa no namespace
  -> runtime usa sob policy Zane
```

## Offline

Zane inicia sem conexao ULTRON. Capacidades instaladas podem continuar somente se politica, licenca e integridade permitirem.

## Seguranca

- registry nao concede approvals;
- pack nao acessa memoria por padrao;
- tools importadas recebem nova classificacao Zane;
- updates com novas permissoes exigem revisao;
- versoes comprometidas podem ser bloqueadas;
- desinstalacao preserva dados conforme contrato.

## UX

Usuario ve publisher, versao, permissoes, compatibilidade, risco, dependencias e changelog. Instalacao e ativacao sao acoes distintas.

## Testes

- ULTRON indisponivel;
- pack invalido/comprometido;
- conflito de namespace;
- rollback de update;
- remocao com dependentes;
- tentativa de elevar permissao;
- independencia de capacidades nativas.

