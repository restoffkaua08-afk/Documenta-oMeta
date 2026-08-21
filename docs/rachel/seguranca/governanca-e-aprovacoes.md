# Rachel - Seguranca, Governanca e Aprovacoes

## Principios

- deny-by-default;
- menor privilegio;
- aprovacao proporcional ao efeito;
- argumentos fazem parte da autorizacao;
- autorizacao de uso unico por padrao;
- auditabilidade sem expor segredos;
- falha fechada em estados desconhecidos;
- explicacao em linguagem humana.

## Classificacao de efeitos

As fontes mencionam quatro niveis de risco no runtime legado, mas a nomenclatura exata deve ser confirmada. A politica funcional minima e:

| Classe | Exemplos | Regra |
|---|---|---|
| Leitura interna segura | status do runtime, leitura em workspace autorizado | pode ser automatica conforme politica |
| Leitura pessoal/externa | documentos pessoais, rede, APIs | exige escopo e possivelmente aprovacao |
| Mutacao local | escrever arquivo, stage/commit, iniciar processo | aprovacao contextual e verificacao |
| Critica/externa | excluir, publicar, push, credenciais, alterar permissoes | aprovacao explicita reforcada; algumas podem permanecer proibidas |

## Contrato de aprovacao

Uma solicitacao deve explicar:

- qual acao sera feita;
- em qual alvo/escopo;
- quais argumentos relevantes;
- qual impacto esperado;
- qual risco;
- se pode ser desfeita;
- qual plano e passo originaram o pedido.

## Ciclo

```text
proposta
  -> validacao de politica
  -> approval_id vinculado ao plano
  -> exibicao ao usuario
  -> approve | deny | expire
  -> consumo atomico se aprovado
  -> execucao exata
  -> verificacao e auditoria
```

## Prompt injection e entrada nao confiavel

Conteudo de web, documentos, repositorios, issues e ferramentas deve ser tratado como dado, nao como autoridade. Instrucoes encontradas nesses conteudos nao podem alterar politica, solicitar segredos ou conceder permissoes.

## Filesystem

- caminhos relativos a escopos nomeados;
- bloqueio de `..`, caminhos absolutos, NUL e escapes;
- politica explicita para symlinks;
- escrita atomica e backup quando aplicavel;
- delete de diretorio nao recursivo por padrao;
- dados pessoais recebem classificacao de efeito apropriada.

## Processos e comandos

- preferir perfis e tools tipadas;
- evitar shell para operacoes comuns;
- bloquear interpretadores perigosos na superficie generica;
- limitar argumentos, saida, duracao e diretorio;
- operar apenas processos registrados pela Rachel;
- nunca permitir kill arbitrario por PID na superficie comum.

## Segredos

Tokens e credenciais devem usar Credential Manager/vault equivalente. Logs, eventos, memoria, prompts e documentos devem aplicar redacao.

## Evidencias necessarias

- testes de autorizacao de uso unico;
- testes de alteracao de argumentos;
- testes de expiração e negacao;
- tentativas de escape de escopo;
- prompt injection em fontes externas;
- verificacao de redacao de segredos;
- auditoria de tools criticas.

