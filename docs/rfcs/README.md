# RFCs

Uma RFC é usada **antes** de quebrar em tasks quando o trabalho é grande ou
ambíguo o bastante para que errar a interpretação custe caro: feature que
cruza vários módulos, integração com sistema externo, mudança de modelo
conceitual, ou pedido de produto com alternativas em aberto.

Diferença para as outras pastas:

| Pasta | Pergunta que responde | Quando |
|---|---|---|
| `docs/rfcs/` | **O que** vamos fazer e com que desenho? O que ainda não sabemos? | Antes das tasks |
| `docs/decisions/` | **Por que** escolhemos isto? | Quando uma escolha é tomada (uma RFC costuma gerar vários ADRs) |
| `docs/architecture/` | **Como** funciona hoje? | Depois de implementado |

Regras:

- Copiar `TEMPLATE.md` para `NNNN-slug.md` (numeração sequencial própria).
- **Uma RFC só vira tasks de implementação quando não tiver pergunta
  bloqueante sem resposta.** Pergunta que não bloqueia tudo pode ficar
  aberta, mas a task que depende dela nasce `Bloqueada` e cita a pergunta.
- Premissa sobre sistema externo/lib que o desenho depende → spike
  (`Resultado do spike`) antes de fechar o desenho.
- Respostas de produto entram em "Decisões validadas" com quem e quando.

| # | Título | Status |
| --- | --- | --- |
| [0001](./0001-<slug>.md) | `<título>` | `<Rascunho / Em validação / Aprovada / Implementada / Abandonada>` |
