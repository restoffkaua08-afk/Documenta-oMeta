# Resumo dos Quatro Projetos

## Rachel

Agente pessoal standalone e governado. Prioriza seguranca, memoria controlada, ferramentas com aprovacao, operacao local-first e experiencia desktop. Seu desenvolvimento profissional deve produzir capacidades verificaveis que poderao ser estudadas durante a consolidacao do Zane.

## Jarvis

Runtime standalone de agentes, modelos e ferramentas. Organiza orquestracao, model routing, skills, MCP e execucao observavel. Deve ser desenvolvido e validado como produto proprio antes que suas capacidades sejam selecionadas para o Zane.

## ULTRON

Plataforma independente e opcional para registrar, validar, versionar e distribuir agentes, skills, workflows e packs. Amplia o ecossistema, mas nao armazena a identidade central do Zane nem participa obrigatoriamente de seus fluxos essenciais.

## Zane

Produto final do Meta. Consolida as melhores capacidades comprovadas de Rachel e Jarvis em codigo, configuracao e arquitetura proprios. Nao e `Rachel + Jarvis` executados lado a lado e nao depende deles em producao.

## Relacao entre eles

| Origem | Destino | O que pode atravessar | O que nao deve atravessar automaticamente |
|---|---|---|---|
| Rachel | Zane | politicas, contratos, padroes de memoria, UX validada | identidade Rachel, acoplamentos e codigo sem auditoria |
| Jarvis | Zane | runtime, roteamento, ferramentas e padroes de agentes validados | dependencia do repositorio ou upstream sem adaptacao |
| ULTRON | Zane | componentes opcionais assinados e compativeis | autoridade central ou requisito de inicializacao |
| Zane | ULTRON | protocolo de compatibilidade e consumo de componentes | memoria privada ou segredos do usuario por padrao |
