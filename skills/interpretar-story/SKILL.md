---
name: interpretar-story
description: Lê uma story de produto (Linear/Jira, arquivo .md ou texto colado) e a traduz numa leitura técnica — separando o que é núcleo reutilizável do que é particularidade de um cliente (que vira configuração/dado), apontando contradições, hipóteses não validadas e levantando perguntas para o time de produto. Use quando o usuário pedir para "ler a story", "interpretar a história", "analisar a US-XX", colar um link/ID de story ou um arquivo de stories, ou antes de criar RFC/tasks/implementar a partir de uma story.
---

# Interpretar Story

Stories costumam chegar do time de produto escritas por alguém **não técnico,
muitas vezes com apoio de IA**. Descrevem bem o **problema e o caso real**, mas
costumam:

- descrever a solução amarrada a **um cliente específico** (redes, lojas, códigos,
  URLs, horários, limites numéricos);
- conter **contradições** entre si ou com a regra do próprio produto;
- trazer **detalhe de implementação inventado** (tabela, visão, camada, formato)
  que não conversa com a arquitetura real;
- afirmar como **fato** comportamento de sistema externo que ninguém validou.

**A story não é fonte da verdade.** É entrada de requisito. A fonte da verdade
técnica é o código, os ADRs (`docs/decisions/`), a arquitetura
(`docs/architecture/`), o `AGENTS.md`/`project-rules.md` e o documento de produto
vigente.

## Princípio central: separar o genérico do particular

Se o produto atende vários clientes/tenants, toda implementação tem que funcionar
para **qualquer** um que ative a mesma capacidade, não só o que originou a
demanda. (Produto de cliente único: o princípio vale mesmo assim para o que é
configurável vs. fixo.)

| A story diz… | A implementação faz… |
| -- | -- |
| Valor específico de um cliente (código, URL, loja, tipo) | **Configuração/dado** — nunca enum, constante ou `if` no código |
| "Para o cliente X faça A, para Y faça B" | Um único comportamento parametrizado; a diferença vira configuração |
| Número mágico (26 h, 06:00, teto de 20 itens) | Valor **padrão configurável**, com o número da story como default |
| Lista de exclusão por nome/código do cliente | Mecanismo genérico de regra/filtro; os valores são dados iniciais (seed/config) |
| Fluxo descrito em termos do negócio do cliente | Abstrair o conceito (ex.: "rede" → instância/conexão; "loja" → identificador de unidade) |
| Dado real de cliente como exemplo | **Fixture de teste**, nunca regra |
| Nome de tabela/camada/visão sugerido | Sugestão; seguir o padrão de persistência e nomenclatura do projeto |

Particularidade de cliente é **bem-vinda como caso de teste e valor default**;
é **proibida como estrutura de código**.

## Passos

1. **Obter a story.** Link/ID do Linear/Jira: usar o MCP correspondente se
   estiver disponível (autenticar se necessário); se não, pedir ao usuário que
   cole o conteúdo — não inventar. Arquivo ou texto colado: ler inteiro,
   incluindo stories irmãs referenciadas, porque contradições aparecem **entre**
   stories. Registrar o pedido **literalmente** (vai para a "Origem" do RFC/task).

2. **Ler o contexto técnico real antes de concluir qualquer coisa:** integrações
   e módulos parecidos já existentes (como resolveram cliente, credencial,
   paginação, limites); modelos e campos que a story assume existir (conferir no
   schema — a story frequentemente afirma algo sobre o código que não é verdade);
   ADRs relacionados; `GOTCHAS.md`; regras não negociáveis do `AGENTS.md`.

3. **Classificar cada requisito:**
   - **Núcleo genérico** — o que a plataforma precisa saber fazer para qualquer cliente.
   - **Particularidade do cliente → configuração/dado** — vira campo de
     configuração, seed, lista configurável ou fixture.
   - **Detalhe inventado/opinativo** — sugestão de implementação que pode ser
     substituída pelo padrão do projeto (dizer qual).
   - **Hipótese não validada** — afirmação sobre sistema externo que precisa de
     teste real antes de virar código (marcar "a validar").
   - **Fora do escopo / pertence a outra story.**

4. **Caçar contradições**, explicitamente: story × princípio de produto; entre
   stories irmãs; story × código/ADRs; internas (critério de aceite que
   contradiz o "o que entra").

5. **Decidir o que trava.** Se dá para resolver de forma genérica sem perder o
   requisito → **resolver** e registrar a decisão (é decisão técnica). Se **não
   existe** forma genérica que atenda, ou a escolha muda o comportamento visível
   ao cliente → **não decidir sozinho**: vira pergunta para produto, com o
   padrão assumido enquanto não houver resposta.

6. **Entregar a leitura técnica** no formato abaixo (direto, sem repetir a story).

## Formato da saída

```markdown
## Leitura técnica — <ID/título da story>

### O que é, em uma frase (genérico)
<o problema que a plataforma resolve, sem nome de cliente>

### Núcleo genérico a implementar
- …

### Particularidades do cliente → configuração/dado
| Na story | Vira | Default |
| -- | -- | -- |

### Detalhes da story que serão substituídos pelo padrão do projeto
- <o que a story sugere> → <o que o projeto já usa> (ref. ADR/arquivo)

### Hipóteses a validar antes de codar
- …

### Contradições encontradas
- <onde> — <o que contradiz o quê> — <resolvido como… | vai para produto>

### Perguntas para o time de produto (bloqueantes)
1. <pergunta objetiva, com as opções e o impacto de cada uma>

### Fora do escopo desta story
- …
```

As perguntas para produto são escritas para alguém **não técnico**: curtas, com
opções concretas e o efeito de cada uma para o cliente — nunca em termos de
tabela, classe ou endpoint.

## Uso com subagentes e documentos derivados (RFC, SPEC, tasks)

- Subagente **não conversa com o usuário**. A leitura técnica e as perguntas são
  feitas na sessão principal; subagente só recebe decisões já tomadas (para ler
  muito código ou redigir um documento) e devolve pendências como lista, nunca
  decide por conta própria.
- RFC/SPEC/task gerados a partir de uma story partem da **leitura técnica**, não
  da story crua. Todo valor específico de cliente nesses documentos aparece como
  exemplo de configuração ou fixture.

## Não fazer

- Não implementar nada específico de um cliente (enum, `if` por código, URL
  fixa, lista de exclusão no código) só porque a story descreveu assim.
- Não tratar número, nome de tabela ou fluxo da story como requisito fechado sem
  passar pelo passo 3.
- Não "resolver" sozinho uma contradição que muda comportamento de produto — ela
  vai para a lista de perguntas.
- Não tratar como fato comportamento de API externa descrito na story sem
  evidência (teste real, fixture gravada, documentação oficial).
- Não criar tasks direto da story crua: primeiro a leitura técnica; depois, se o
  usuário pedir, `criar-task` (ou `criar-rfc` para trabalho grande).
