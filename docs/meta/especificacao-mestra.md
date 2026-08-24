# Projeto Meta - Especificacao Mestra

**Estado:** especificacao arquitetural ativa  
**Versao documental:** 1.0  
**Atualizada em:** 2026-08-21

## 1. Finalidade

Esta especificacao define o Projeto Meta como programa de engenharia, estabelece o papel dos quatro projetos, fixa suas fronteiras, descreve a ordem de construcao e cria criterios para distinguir predecessores, produto final e plataforma de extensao.

Ela deve ser usada por desenvolvedores, agentes de IA, auditores e pelo proprietario para responder cinco perguntas:

1. O que esta sendo construido?
2. Qual projeto e responsavel por cada capacidade?
3. O que precisa existir antes da proxima fase?
4. Qual evidencia autoriza uma declaracao de conclusao?
5. Quais ideias antigas deixaram de orientar o produto?

## 2. Objetivo do produto

O objetivo final e construir o Zane: um agente pessoal completo, independente e extensivel, capaz de combinar conversa, memoria, contexto, planejamento, ferramentas, execucao governada, conhecimento e operacao em diferentes interfaces.

Zane deve preservar as melhores propriedades comprovadas durante o desenvolvimento de Rachel e Jarvis, mas nao deve depender dos dois repositorios para funcionar.

ULTRON complementa o produto ao permitir descoberta, instalacao, validacao e distribuicao de capacidades adicionais. Essa conexao e opcional e deve falhar de maneira segura.

## 3. Escopo do programa

### 3.1 Dentro do escopo

- construcao e validacao standalone da Rachel;
- construcao e validacao standalone do Jarvis;
- definicao de contratos e inventarios dos predecessores;
- construcao independente do ULTRON;
- consolidacao arquitetural no Zane;
- desktop, web e evolucao futura para outros clientes;
- memoria local e sincronizacao opcional;
- ferramentas, skills, MCP e integracoes governadas;
- seguranca, permissoes, auditoria e observabilidade;
- roteamento entre modelos substituiveis;
- CI, testes, builds, empacotamento e readiness de producao;
- documentacao historica, executiva e tecnica.

### 3.2 Fora do escopo inicial

- dependencia obrigatoria de um unico provedor de IA;
- autonomia irrestrita ou aprovacao global permanente;
- dependencia operacional do ULTRON;
- infraestrutura paga antes dos gates locais;
- apagamento dos predecessores apos a consolidacao;
- declaracoes de inteligencia ou aprendizado sem evidencia reproduzivel;
- promocao automatica de modelos sem avaliacao e autorizacao.

## 4. Produtos do programa

### 4.1 Rachel

Rachel prova identidade, contexto pessoal, memoria, governanca, seguranca e experiencia local-first. Deve encerrar seu proprio roadmap com um produto standalone instalavel e testado.

### 4.2 Jarvis

Jarvis prova runtime agentic, planejamento, execucao, tools, skills, MCP, pesquisa e integracao de modelos. Sera derivado do OpenJarvis com origem e licenca preservadas.

### 4.3 ULTRON

ULTRON prova um ecossistema independente de capacidades versionadas. Ele nao substitui o runtime nativo do Zane e nao pode controlar sua identidade.

### 4.4 Zane

Zane e o produto definitivo. Ele recebe uma arquitetura propria criada apos a analise dos predecessores. Seu codigo nao deve ser apenas uma colecao de imports da Rachel e do Jarvis.

## 5. Invariantes arquiteturais

Uma implementacao so esta alinhada ao Meta se respeitar todos os invariantes abaixo:

1. Rachel funciona sem Jarvis.
2. Jarvis funciona sem Rachel.
3. Zane funciona sem iniciar os predecessores.
4. Zane funciona sem ULTRON.
5. Capacidades sensiveis passam por politica e auditoria.
6. Segredos nao sao armazenados em texto aberto na documentacao ou em repositorios.
7. Provedores de modelo sao substituiveis.
8. Memoria pessoal e dataset de treinamento permanecem conceitualmente separados.
9. Estados desconhecidos ou incompletos nao sao tratados como sucesso.
10. Cada gate exige evidencia reproduzivel.

## 6. Capacidades transversais

| Capacidade | Predecessor de validacao | Dono no produto final |
|---|---|---|
| Identidade e persona | Rachel | Zane |
| Memoria e contexto | Rachel | Zane |
| Seguranca e autorizacao | Rachel | Zane |
| Planejamento agentic | Jarvis e Rachel | Zane |
| Execucao de ferramentas | Jarvis e Rachel | Zane |
| Skills e MCP | Jarvis | Zane |
| Packs e marketplace | ULTRON | ULTRON, consumido opcionalmente pelo Zane |
| Interface principal | Rachel como laboratorio | Zane |
| Modelo proprio condicional | Rachel como experimento | Decisao futura do Zane |

## 7. Estrategia local-first e cloud opcional

O modo local deve preservar as funcoes essenciais: inicializacao, conversa, configuracao, memoria local, seguranca, tools locais permitidas e auditoria basica.

A cloud pode adicionar sincronizacao, acesso remoto, multi-dispositivo, modelos maiores, jobs longos, backup e colaboracao. A indisponibilidade da cloud deve resultar em degradacao explicita, nao em corrupcao silenciosa ou perda da identidade local.

## 8. Dados e armazenamento

Tres categorias nao devem ser misturadas:

| Categoria | Exemplos | Tratamento |
|---|---|---|
| Conhecimento humano | notas, projetos, estudos, documentos | arquivos legiveis e indexaveis; Obsidian e uma opcao |
| Memoria da aplicacao | conversas, fatos, sessoes, embeddings, preferencias | armazenamento local e/ou PostgreSQL/Supabase conforme o modo |
| Segredos | tokens, chaves, credenciais | vault seguro do sistema operacional ou servico equivalente |

Redis, quando usado, serve para cache, eventos e estado temporario. Ele nao e fonte permanente de memoria.

## 9. Seguranca

O sistema deve operar deny-by-default para efeitos nao reconhecidos. Cada ferramenta declara efeito, risco, argumentos aceitos, escopo e necessidade de aprovacao. A aprovacao e contextual e preferencialmente de uso unico.

As seguintes acoes exigem tratamento explicito: escrita e exclusao de arquivos, comandos, Git remoto, navegador com efeitos, credenciais, publicacao externa, alteracao de configuracao e operacao em dados pessoais.

## 10. Observabilidade e auditoria

Cada execucao relevante deve ser rastreavel por identificadores correlacionados. O registro deve permitir reconstruir roteamento, planejamento, provider, tool calls, aprovacoes, duracoes, resultados, falhas e cancelamentos sem expor segredos.

## 11. Qualidade e evidencia

Os seguintes artefatos podem sustentar um gate:

- commit identificavel;
- testes automatizados com resultado registrado;
- build reproduzivel;
- relatorio de seguranca;
- demonstracao E2E;
- matriz de readiness;
- verificacao de portabilidade;
- auditoria direta do codigo.

Uma frase em um documento nao substitui esses artefatos.

## 12. Estado atual do programa

- Rachel: em evolucao profissional; etapas iniciais possuem implementacoes registradas.
- Jarvis: planejamento consolidado, desenvolvimento aguardando gate da Rachel.
- ULTRON: U0 aprovado; U1 aprovado e estabilizado com codigo, testes e build reproduzidos; U2-U5 pendentes.
- Zane: especificacao e decisoes iniciais; construcao aguardando predecessores.

## 13. Criterio de sucesso do Meta

O programa atinge seu objetivo quando:

1. Rachel e Jarvis possuem releases standalone verificadas.
2. ULTRON possui contrato independente e integracao opcional.
3. Zane incorpora capacidades selecionadas em arquitetura propria.
4. Testes provam que Zane nao depende dos predecessores nem do ULTRON.
5. Seguranca, observabilidade, instalacao e recuperacao foram validadas.
6. A documentacao permite que outra equipe continue o desenvolvimento sem depender do chat original.
