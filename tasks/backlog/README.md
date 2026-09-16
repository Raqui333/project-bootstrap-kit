# Backlog

Índice das fases/épicos do backlog do `<nome do projeto>`. Cada fase é uma
pasta própria com seu `README.md` (objetivo, valor de entrega, o que fica fora
de escopo) e suas tasks numeradas (`NNNN-slug.md`), seguindo `tasks/TEMPLATE.md`.

> Se o projeto não tiver o conceito de "fase", simplifique para uma lista
> única de tasks direto em `tasks/backlog/NNNN-slug.md`, sem subpastas — a
> convenção de numeração, template e ciclo (backlog → done) continua igual.

Ordem de execução recomendada: `<explicitar se as fases são sequenciais por
dependência técnica ou podem ser reordenadas — e citar exceções task a
task>`.

## Fases pendentes

| Fase | Pasta | Foco | Tasks | Decisão bloqueante |
|---|---|---|---|---|
| `<N>` — `<nome>` | [`<pasta>/`](./<pasta>/README.md) | `<resumo>` | `<n>` | `<-- ou referência de ADR/decisão pendente>` |

## Fases já concluídas

| Fase | Pasta | Foco | Tasks |
|---|---|---|---|
| `<N>` — `<nome>` | [`<pasta>/`](../done/<pasta>/README.md) | `<resumo>` | `<n>` |

## Decisões em aberto que impactam o backlog

- `<Bloqueios conhecidos: escolha de biblioteca/infra ainda não decidida,
  dependência externa etc. — referenciar a task ou ADR correspondente.>`

## Convenções

- Toda task nasce em `tasks/backlog/<fase>/` (ou raiz de `tasks/backlog/` se
  não houver fases), nunca direto em `tasks/done/`.
- Numeração sequencial de 4 dígitos por pasta de fase (não é global entre
  fases, se houver fases).
- Ao concluir uma task, usar a skill `concluir-task` (prefixo `(DONE) ` no
  nome do arquivo, com a ordem de verificação do `AGENTS.md` verde antes de
  marcar).
- Ao concluir a última task de uma fase, mover a pasta inteira para
  `tasks/done/` (ou `tasks/pendentes/` se restar algo travado por motivo
  externo ao código) e atualizar este arquivo.
