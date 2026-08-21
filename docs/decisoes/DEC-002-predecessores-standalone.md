# DEC-002 - Rachel e Jarvis devem funcionar standalone

**Estado:** ativa  
**Data de confirmacao:** 2026-08-21

## Contexto

Rachel e Jarvis fornecerao capacidades candidatas ao Zane. Se forem desenvolvidos apenas como fragmentos de uma integracao futura, nao sera possivel medir sua maturidade nem distinguir capacidade comprovada de promessa arquitetural.

## Decisao

Rachel e Jarvis devem atingir seus respectivos criterios de produto standalone antes da consolidacao final do Zane. Cada um deve iniciar, executar seus fluxos essenciais, ser testado e produzir evidencias sem depender do outro ou do futuro repositorio do Zane.

## Alternativas consideradas

- Integrar imediatamente e corrigir durante a construcao do Zane: rejeitada por acumular incerteza e acoplamento.
- Concluir apenas Rachel e usar Jarvis como biblioteca interna: rejeitada porque impede validar o runtime como predecessor independente.

## Consequencias

- cada predecessor mantem repositorio, configuracao e releases proprios;
- interfaces e capacidades precisam ser inventariadas antes da migracao;
- codigo reutilizado deve passar por auditoria de licenca, seguranca e compatibilidade;
- os produtos podem continuar existindo historicamente depois do Zane, sem participar de sua operacao.

## Criterio de aceite

Um predecessor e considerado standalone somente quando instalacao limpa, testes, operacao essencial, observabilidade, documentacao e rollback estiverem demonstrados no escopo definido por seu gate final.
