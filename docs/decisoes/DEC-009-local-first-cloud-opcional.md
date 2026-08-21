# DEC-009 - Local-first com cloud opcional

**Estado:** ativa  
**Data de consolidacao:** 2026-08-21

## Contexto

O produto precisa preservar privacidade, autonomia local e continuidade, mas algumas capacidades futuras exigem sincronizacao, acesso remoto, modelos maiores ou jobs longos.

## Decisao

As funcoes essenciais devem possuir modo local. A cloud e uma extensao autorizada, nao a fonte exclusiva da identidade do produto.

## Consequencias

- dados locais precisam de estrategia de migracao e backup;
- sincronizacao deve lidar com conflitos;
- cada integracao cloud exige opt-in e politica clara;
- falhas remotas devem produzir degradacao explicita;
- segredos usam armazenamento seguro, nunca arquivos de notas.

