# Rachel - Memoria e Conhecimento

## Separacao fundamental

Memoria registra informacoes uteis sobre usuario, sessoes e decisoes. Conhecimento indexa fontes e documentos. Dataset serve a avaliacao ou treinamento mediante consentimento e pipeline proprio.

Essas categorias nao sao intercambiaveis.

## Tipos de memoria

| Tipo | Exemplo | Retencao esperada |
|---|---|---|
| Sessao | mensagens e estado da conversa atual | curta/persistencia configuravel |
| Episodica | decisao tomada em uma data | persistente com proveniencia |
| Semantica | preferencia ou fato estavel | persistente e corrigivel |
| Procedural | padrao de trabalho aprovado | versionado, nao autoexecutavel |
| Operacional | plano, job, cursor, cancelamento | temporaria e transacional |

## Pipeline de memoria

```text
conteudo
  -> classificacao e filtro de privacidade
  -> extracao de candidato
  -> decisao de persistencia
  -> proveniencia + escopo + retencao
  -> armazenamento
  -> indexacao/embedding quando permitido
```

## Recall

A recuperacao deve considerar usuario/tenant, projeto, conversa, recencia, relevancia, sensibilidade e permissao. O modelo recebe somente o contexto necessario.

## CognitiveMemory

As fontes registram `CognitiveMemory` com persistencia SQLite. O fechamento legado, porem, classificou o dominio dedicado de memoria como Reserved por insuficiencia combinada de runtime e testes. Isso indica que existiam implementacoes cognitivas, mas o dominio completo nao estava comprovado.

## Knowledge Port

Conhecimento deve fornecer resultados com fonte, trecho, data, score e permissao. O roadmap identificou `NullKnowledgeAdapter` como problema: uma porta vazia nao pode ser apresentada como recuperacao real.

## Obsidian

Pode atuar como Knowledge Vault humano em Markdown. Nao deve guardar credenciais nem substituir o banco transacional. A Rachel pode indexar e atualizar notas somente com escopos e regras claras.

## Cloud e pgvector

PostgreSQL/Supabase e pgvector podem armazenar memoria estruturada, embeddings e sincronizacao. O componente e substituivel. RLS/isolamento e obrigatorio no modo multiusuario.

## Privacidade

- memoria deve ser inspecionavel e corrigivel;
- exclusao deve propagar conforme politica;
- informacao sensivel recebe retencao minima;
- provider externo recebe apenas contexto autorizado;
- embedding tambem e dado e precisa de governanca;
- salvar memoria nao autoriza dataset.

## Pendencias

- confirmar schema atual e migrations;
- validar isolamento entre projetos/usuarios;
- implementar Knowledge Port real;
- definir retencao e exportacao;
- testar deduplicacao, correcao e exclusao;
- alinhar memoria local com sync futuro.

