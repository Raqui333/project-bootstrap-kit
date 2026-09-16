---
name: setup-projeto
description: Instala o kit project-bootstrap-kit num repositório novo ou existente (cópia da estrutura, preenchimento interativo dos marcadores de AGENTS.md/overview.md) e mantém em sincronia depois — reaplicando atualizações do kit sem sobrescrever o que já foi customizado. Use quando o usuário pedir para "configurar esse projeto com o bootstrap", "instalar a metodologia aqui", "atualizar as skills desse repo com a versão mais nova do bootstrap" ou "sincronizar com o project-bootstrap-kit".
---

# Setup de Projeto (bootstrap)

Aplica este kit (`project-bootstrap-kit`) a um repositório **alvo** — que pode
ser o repositório atual (se não for o próprio `project-bootstrap-kit`) ou um
caminho informado pelo usuário. Tem dois modos: **instalação inicial** (kit
ainda não existe no projeto alvo) e **sincronização** (kit já existe e
recebeu atualizações aqui em `project-bootstrap-kit`, ou o usuário quer
conferir/reforçar o que já está instalado).

## Pré-requisito: identificar a origem e o destino

1. **Confirmar onde está a cópia deste kit** (`project-bootstrap-kit`) — por
   padrão, o diretório de trabalho atual desta sessão, a menos que o usuário
   informe outro caminho.
2. **Confirmar o repositório alvo.** Nunca aplicar o setup dentro do próprio
   `project-bootstrap-kit` (identificável por ter este `README.md` e a pasta
   `skills/` na raiz sem estarem dentro de `.claude/`). Se o diretório atual
   for o próprio kit, perguntar ao usuário o caminho do projeto alvo.
3. **Detectar o modo**: se o alvo já tiver `AGENTS.md` **e** `.claude/skills/`
   na raiz, é sincronização (seção "Modo sincronização"). Caso contrário, é
   instalação inicial (seção "Modo instalação inicial").

## Modo instalação inicial

1. **Copiar a estrutura** do kit para a raiz do projeto alvo:
   ```bash
   cp <bootstrap>/AGENTS.md <alvo>/AGENTS.md
   cp -r <bootstrap>/docs <alvo>/docs
   cp -r <bootstrap>/tasks <alvo>/tasks
   cp -r <bootstrap>/skills <alvo>/.claude/skills
   cp -r <bootstrap>/best-practices <alvo>/docs/best-practices
   ```
   Se o alvo já tiver algum desses diretórios com conteúdo (ex.: `docs/` de
   produto pré-existente), **não sobrescrever** — copiar só o que falta e
   avisar o usuário sobre o conflito para ele decidir merge manual.

2. **Levantar as informações do projeto real** perguntando ao usuário (não
   inventar) o que não for óbvio a partir do código/README já existente no
   alvo:
   - Nome/descrição do produto e domínio de negócio.
   - Stack real (linguagem, framework, banco, fila, observabilidade).
   - Comandos reais de lint/typecheck/format/test/test:e2e/build (ler
     `package.json`/`Makefile`/equivalente do alvo antes de perguntar — só
     perguntar o que não conseguir inferir).
   - Ordem de verificação antes de commitar (mais rápido → mais lento).
   - Idioma de comentários/nomes/docs vs. mensagens visíveis ao usuário
     final.
   - Se o domínio tem multi-tenancy/multi-cliente/dado sensível e qual o
     mecanismo real de isolamento.
   - Se há operações que exigem auditoria.
   - Se o projeto gera doc de API a partir de decorators/tipos runtime (afeta
     a seção de contratos de resposta do `AGENTS.md`).

3. **Preencher os marcadores `<...>` de `AGENTS.md`** no alvo com as
   respostas do passo 2, removendo a nota de "Template" no topo do arquivo e
   qualquer seção cujo princípio genuinamente não se aplique ao projeto
   (deixar claro no resumo final quais seções foram removidas e por quê, não
   apagar silenciosamente).

4. **Preencher `docs/architecture/overview.md`** no mínimo com a seção
   "Contexto" e a lista inicial de módulos existentes, se já houver código no
   alvo. Se o alvo for um repositório vazio, deixar os marcadores como estão
   para a primeira task preencher.

5. **Criar/atualizar `CLAUDE.md`** na raiz do alvo apontando para `AGENTS.md`
   via import (`@AGENTS.md`), sem duplicar conteúdo. Se o alvo já tiver um
   `CLAUDE.md` com conteúdo próprio, adicionar o import no topo em vez de
   sobrescrever.

6. **Ajustar `tasks/backlog/README.md`** com as fases reais do roadmap do
   projeto, se o usuário já tiver uma visão de fases; caso contrário deixar o
   template como ponto de partida e avisar que precisa ser preenchido antes
   da primeira `criar-task`.

7. **Simplificar o que não fizer sentido** (ex.: projeto sem conceito de
   "fase" no backlog → achatar `tasks/backlog/` para lista única).

8. **Não fazer `git add`/`commit` no alvo automaticamente** — reportar os
   arquivos criados/alterados e deixar o usuário revisar e commitar.

## Modo sincronização

Usado quando o kit já foi instalado antes e (a) `project-bootstrap-kit` recebeu
atualizações (nova skill, novo item em `best-practices/`, seção nova no
`AGENTS.md` template) que ainda não chegaram ao projeto alvo, ou (b) o
usuário só quer conferir se o alvo está com tudo em dia.

1. **Comparar skills**: para cada pasta em `<bootstrap>/skills/*`, verificar
   se existe em `<alvo>/.claude/skills/*` com o mesmo nome.
   - Se faltar, copiar a pasta inteira.
   - Se existir mas o conteúdo divergir do kit, mostrar o diff resumido ao
     usuário e perguntar se quer atualizar para a versão do kit (pode haver
     customização proposital no projeto) — nunca sobrescrever skill
     customizada sem confirmar.

2. **Comparar `best-practices/`**: mesmo processo — arquivo novo no kit que
   não existe em `<alvo>/docs/best-practices/` é copiado direto (é conteúdo
   de referência, raramente customizado por projeto); arquivo que já existe
   nos dois e diverge, mostrar diff e perguntar antes de sobrescrever.

3. **Comparar `AGENTS.md`**: **nunca sobrescrever** o `AGENTS.md` do alvo (já
   tem os marcadores preenchidos com a realidade do projeto). Em vez disso,
   comparar a estrutura de seções com o `AGENTS.md` template do kit e listar
   para o usuário quais seções existem no kit e não existem no alvo (ex.:
   nova seção adicionada ao template desde a instalação) — deixar o usuário
   decidir se quer incorporá-las manualmente ou pedir para você redigi-las.

4. **Comparar templates** (`tasks/TEMPLATE.md`, `docs/architecture/TEMPLATE.md`,
   `docs/decisions/TEMPLATE.md`): se o template do kit mudou de forma
   estrutural (campo novo, seção nova), avisar o usuário — não sobrescrever
   templates do alvo silenciosamente, pois tasks/ADRs já criados seguem o
   formato antigo e trocar o template no meio do projeto pode gerar
   inconsistência.

5. **Reportar um resumo objetivo** ao final: o que foi copiado direto (sem
   risco), o que precisa de decisão do usuário (com o diff relevante), e o
   que já estava em dia.

## Não fazer

- Não rodar o setup dentro do próprio repositório `project-bootstrap-kit`.
- Não sobrescrever `AGENTS.md`, skills ou best-practices já customizados no
  alvo sem mostrar o diff e confirmar com o usuário.
- Não inventar valores para stack, comandos ou regras de segurança — sempre
  perguntar o que não puder ser inferido do próprio código do alvo.
- Não commitar/push no repositório alvo por conta própria.
- Não deixar marcadores `<...>` sem preencher em `AGENTS.md` sem avisar
  explicitamente quais ficaram pendentes e por quê.
