# RFC NNNN — Título

## Status

`Rascunho` · `Em validação` · `Aprovada` · `Implementada` · `Abandonada`

## Origem

- **Pedido original:** `<citação literal / link versionado, quem, quando>`
- **Fontes de requisito consultadas:** `<PRD vX §N, Linear, conversa com Y —
  todas. Divergências entre elas listadas em "Perguntas em aberto".>`

## Problema

O problema real por trás do pedido, em linguagem do negócio. Quem sofre, como
sofre hoje, como saberemos que foi resolvido.

## Glossário / modelo conceitual

Termos do domínio que este desenho usa e o que significam **aqui** (ex.:
"autorização é por agente dentro de um processo, não por processo"). Validar
com produto — interpretação errada do modelo conceitual foi a causa de
retrabalho mais cara nos projetos anteriores.

## Estado atual (verificado no código)

| Afirmação | Verificado? | Evidência |
|---|---|---|
| `<o que o código faz hoje>` | `Sim` / `A verificar` | `<arquivo:linha, teste>` |

## Objetivos e não-objetivos

- **Objetivos:** ...
- **Não-objetivos (fora de escopo):** ...

## Desenho proposto

...

## Alternativas consideradas

| Alternativa | Por que não |
|---|---|

## Casos negativos e de borda

Matriz por operação (criar / ler / listar / atualizar / excluir) × ator
(dono, outro tenant/setor/papel, sem permissão, sistema externo): o que deve
acontecer em cada um. Foi aqui que lacunas de autorização escaparam de RFCs
anteriores ("o produto não tem pendências" — e depois 7 tasks de correção).

## Dependências externas e spikes

| Dependência / premissa | Quem destrava | Resultado do spike |
|---|---|---|
| `<acesso à API X, comportamento de lib Y>` | `<TI, fornecedor>` | `<o que a chamada real mostrou — data>` |

## Perguntas em aberto

| # | Pergunta (com opções e consequências) | Quem responde | Bloqueia | Resposta (data, quem) |
|---|---|---|---|---|
| 1 | | | `Tudo` / `Task NNNN` / `Não (default: ...)` | |

## Decisões validadas

- `<decisão>` — `<quem confirmou, quando>`

## Fases e tasks

`<Quebra em tasks, indicando quais dependem de quais perguntas.>`

## Plano de testes

`<Que testes provam o desenho, incluindo os casos negativos acima.>`

## Riscos e quando reconsiderar

...
