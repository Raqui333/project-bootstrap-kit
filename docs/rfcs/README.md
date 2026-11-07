# RFCs

Um RFC é usado **antes** de quebrar em tasks quando o trabalho é grande ou
ambíguo o bastante para que errar a interpretação custe caro: feature que cruza
vários módulos, integração com sistema externo, mudança de modelo conceitual,
ou pedido de produto com alternativas em aberto. Criar com a skill `criar-rfc`.

## Fluxo

```
pedido/story ──► interpretar-story ──► RFC ──► spike ──► SPEC ──► tasks ──► ADRs
                 (leitura técnica)    (o quê)           (o como)
```

| Pasta | Pergunta que responde | Quando |
| --- | --- | --- |
| `docs/rfcs/` | **O que** vamos fazer e com que desenho? O que ainda não sabemos? | Antes de tudo |
| `docs/specs/` | **Como** implementar, em detalhe suficiente para virar tasks? | Depois do RFC aceito (e do spike) |
| `docs/decisions/` | **Por que** escolhemos isto? | Quando uma escolha é tomada (um RFC costuma gerar vários ADRs) |
| `docs/architecture/` | **Como** funciona hoje? | Depois de implementado |

## Regras

- Numeração sequencial de 4 dígitos; copiar [TEMPLATE.md](./TEMPLATE.md) para
  `NNNN-slug.md`. Nunca reutilizar número.
- **Um RFC só vira tasks quando não tiver pendência de produto bloqueante sem
  resposta.** Pendência não bloqueante fica aberta com o default declarado; a
  task que depende de uma pendência nasce `Bloqueada` e a cita.
- **Dúvida técnica não é "pergunta em aberto"**: vira hipótese + spike (somente
  leitura em sistema externo) ou se resolve lendo o código.
- Status: `Rascunho` → `Proposta` → `Aceita (data)` (só quando o usuário
  aceitar) → `Implementada (data)`; também `Abandonada` ou `Substituída por NNNN`.
  Atualizar o arquivo **e** este índice.
- Decisão posterior que muda o RFC entra como "Atualização" no topo, mantendo o
  texto original como registro.

## Índice

| RFC | Título | Status |
| --- | --- | --- |
| [0001](./0001-<slug>.md) | `<título>` | `<Rascunho / Proposta / Aceita / Implementada / Abandonada>` |
