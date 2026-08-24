# JARVIS-014 — conexão segura com modelos avançados

## Estado

Implementado na `main`. A conexão de rede real depende de uma credencial
válida configurada na máquina de execução e ainda precisa ser testada nela.

## Problema corrigido

O chat resolvia o engine antes de resolver o modelo. Quando
`intelligence.model_code` apontava para um modelo cloud e o engine padrão era
Ollama, a sessão podia permanecer no backend local, que não tinha capacidade de
servir aquele identificador.

Após resolver o modelo, o chat agora:

1. consulta `engine.can_serve(model)`;
2. se necessário, chama novamente `get_engine` passando o modelo;
3. seleciona apenas um engine saudável que declare atender ao modelo;
4. encerra com mensagem clara quando não existe provider compatível.

## Modo forte

Foi adicionado:

```powershell
jarvis code --strong
```

O comando seleciona um provider pela presença da variável de credencial, sem
ler, imprimir ou persistir seu valor.

Ordem atual da política:

| Variável encontrada | Provider | Modelo |
|---|---|---|
| `OPENAI_API_KEY` | OpenAI | `gpt-5.4` |
| `ANTHROPIC_API_KEY` | Anthropic | `claude-opus-4-6` |
| `GEMINI_API_KEY` ou `GOOGLE_API_KEY` | Google | `gemini-3.1-pro-preview` |
| `OPENROUTER_API_KEY` | OpenRouter | `openrouter/auto` |

A ordem é uma política configurada, não uma afirmação universal de que um
provider sempre supera os demais.

## Segurança das credenciais

- Valores das chaves nunca aparecem no resultado da seleção.
- A saída mostra somente provider, modelo e nome da variável.
- Falta de credencial produz instrução sem solicitar que a chave seja colada.
- Chaves não devem ser salvas em chat, código, commits ou logs.
- Nenhum segredo foi adicionado ao repositório durante esta etapa.

## Dependências Windows

O modo `-CliOnly` do instalador passa a instalar também o extra
`inference-cloud`, necessário para os SDKs dos providers suportados.

## Validação

Foram adicionados testes para:

- precedência determinística quando existem múltiplas credenciais;
- seleção OpenAI, Anthropic e Google;
- alias `GOOGLE_API_KEY`;
- rejeição de credencial vazia;
- garantia de que a dica não contém valores secretos.

Também foram executados:

- parsing AST dos módulos alterados;
- verificação de linhas com até 88 caracteres;
- inspeção programática das assinaturas do Click;
- confirmação de que `strong` pertence ao comando `code`, não ao helper.

Durante a revisão foi encontrado e corrigido um erro inicial de ligação do
parâmetro `strong`. A primeira correção textual não alterou a assinatura
correta; uma segunda correção orientada pelos nomes das funções resolveu o
problema e a assinatura final foi verificada programaticamente.

## Commits

- `623a5f3670cef8685cdf5a2c3cdabad8fa8ffb20` — seleção segura;
- `0f4d0fbe0f237e9149bf137c8bd88c4945771226` — opção `--strong`;
- `81da4501b70742a9f99c626f4eecab89821e6803` — roteamento por capacidade;
- `0bb047c75c3a046f858eab0c1839cbd608132bae` — testes;
- `7a575760d7fb229fa9c33afa6a55811033c39302` — assinatura corrigida;
- `6cc27ebba672f9d8afc28337732cafdd41f86d64` — dependências Windows.

## Gate restante

A implementação conecta o caminho de software, mas não comprova uma chamada
real. Na máquina final será necessário:

1. configurar uma credencial fora do repositório;
2. executar `jarvis code --strong`;
3. confirmar provider e modelo exibidos;
4. executar os benchmarks básico e multiarquivo;
5. registrar latência, uso, custo, nota e falhas;
6. escolher o provider padrão com base nos resultados reais.
