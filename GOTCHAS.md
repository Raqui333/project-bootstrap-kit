# GOTCHAS.md

Registro consolidado de comportamentos não óbvios de bibliotecas, frameworks,
runtimes e sistemas externos descobertos neste projeto. Consulte **antes** de
investigar um bug que "não faz sentido" e **antes** de planejar uma task que
toque a área (a skill `refinar-task` faz isso).

Regras:

- Este arquivo é o **único** lugar onde o gotcha é descrito. Comentário de
  código pode apontar para cá (`// ver GOTCHAS.md #N`); ADR registra o *porquê*
  da decisão tomada por causa dele, mas referencia a entrada daqui em vez de
  duplicar o fato.
- Só entra o que foi **confirmado com evidência** (teste, repro, resposta real
  capturada). Hipótese não confirmada vai como premissa `A verificar` na task,
  não aqui.
- Se um gotcha se mostrar errado ou for resolvido upstream, atualizar a
  entrada (não apagar): o histórico evita que alguém "redescubra" o erro.

---

## 1. `<Título afirmativo: o que acontece de surpreendente>` (`<lib/sistema>`, `<task de origem>`)

**Onde:** `<arquivos afetados>`
**Refs:** `<ADR, task, doc de arquitetura>`

**O que se esperava:** `<a premissa natural — o que quase todo mundo assumiria>`

**O que acontece de fato:** `<comportamento real>`

**Como foi verificado:** `<teste/repro/resposta real — caminho e nome>`

**Como lidar:** `<solução adotada, e o padrão a evitar para não reintroduzir>`

**Varredura:** `<o mesmo padrão existe em outros pontos do código? Onde foi
checado — gotcha achado num lugar costuma existir em outros>`
