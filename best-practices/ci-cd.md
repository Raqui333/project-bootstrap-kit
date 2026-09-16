# CI/CD — princípios agnósticos de ferramenta

## Pipeline mínimo em todo PR e push para a branch principal

O mesmo conjunto de verificações que roda localmente antes de commitar
(ver `AGENTS.md` — "Ordem de verificação") roda em CI, na mesma ordem, em
**todo** PR e todo push para a branch principal — não só depois do merge, e
não um subconjunto "rápido" que deixa passar mais e valida o resto depois.
Um teste (unitário ou e2e) quebrado deve bloquear merge, não só aparecer
como aviso posterior.

Se e2e é lento (ex.: sobe infraestrutura real via container efêmero), rodar
em job separado e paralelo ao job de verificação rápida (lint/typecheck/
format/test/build) — não serializado atrás dele — para não pagar o custo de
tempo duas vezes sem necessidade.

## Cache de dependências

Cachear o diretório de cache do gerenciador de pacotes, chaveado pelo hash
do lockfile — evita reinstalar do zero em cada execução sem arriscar cache
desatualizado quando o lockfile muda.

## Lockfile sempre respeitado

CI instala dependências em modo "não resolver de novo" (`--frozen-lockfile`
ou equivalente da ferramenta) — nunca deixa o CI silenciosamente resolver
uma árvore de dependência diferente da que roda localmente.

## Cancelamento de execução obsoleta

Configurar concorrência por branch/PR de forma que um novo push cancele a
execução anterior em andamento da mesma branch — evita gastar
tempo/recursos validando um estado já superado.

## Estrutura de referência

```yaml
on:
  pull_request:
    branches: [<branch-principal>]
  push:
    branches: [<branch-principal>]

concurrency:
  group: ci-<workflow>-<branch>
  cancel-in-progress: true

jobs:
  verify:            # lint · typecheck · format · test · build
    steps: [...]

  e2e:                # testes de integração/e2e, em paralelo ao verify
    steps: [...]
```

## Segredos em CI

Toda credencial usada em CI (chave de API, token) vem do mecanismo de
segredo da plataforma de CI (nunca hardcoded no workflow) e nunca é
impressa em log — revisar output de step de erro/debug antes de considerar
o pipeline "pronto".
