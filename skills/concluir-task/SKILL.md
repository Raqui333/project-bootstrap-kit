---
name: concluir-task
description: Marca uma task de tasks/ como concluída (prefixo "(DONE) "), depois de marcar cada critério de aceite com evidência, registrar desvios/descobertas e validar a ordem de verificação do AGENTS.md (lint/typecheck/format/test/build). Use quando o usuário disser "essa task terminou", "marca como feita", "conclui a task X" ou equivalente.
---

# Concluir Task

Fecha o ciclo de uma tarefa em `tasks/`.

`(DONE)` significa "os critérios de aceite foram cumpridos e verificados", não
"o build passou". Nos projetos que usam este kit, a maioria das tasks
`(DONE)` ficou com critérios `- [ ]` desmarcados — ou seja, ninguém conferiu,
e as lacunas apareceram semanas depois como tasks de "ajuste"/"correção".

## Passos

1. **Conferir cada critério de aceite, um por um.** Ler o arquivo da task
   (`tasks/backlog/<fase>/NNNN-slug.md` ou `tasks/pendentes/...`) e, para cada
   critério:
   - Cumprido → marcar `- [x]` e anotar ao lado a evidência curta (nome do
     teste, comando, arquivo): `- [x] Tenant A não lê dado do B —
     test/isolamento.e2e-spec.ts "nega acesso por ID direto"`.
   - Não cumprido → **não marcar `(DONE)`**, a menos que o usuário aceite
     explicitamente adiar; nesse caso, criar a task de continuação (skill
     `criar-task`) e registrar o número dela em "Critérios não cumpridos".

2. **Conferir o mapa de impacto e as perguntas.** Todo consumidor listado em
   "Mapa de impacto" foi tratado (ou explicitamente adiado com task)? Toda
   pergunta em aberto tem resposta registrada, ou o default assumido foi
   comunicado ao usuário? Se a task não tinha essas seções preenchidas, fazer
   agora um grep rápido pelos consumidores do que mudou.

3. **Rodar, na ordem definida em `AGENTS.md`, e confirmar que tudo passa
   verde** (nunca marcar `(DONE)` sem isso). Tipicamente:
   ```bash
   <lint>
   <typecheck>
   <format>
   <test>
   <build>
   ```
   Se algum falhar, corrigir antes de continuar.

4. **Preencher "Registro de implementação"** na task:
   - Desvios do plano e por quê.
   - Descobertas: premissa que se mostrou falsa, comportamento surpreendente
     de lib/runtime (→ registrar em `GOTCHAS.md`), pendência nova (→ task
     nova, citar o número).
   - Critérios não cumpridos (ou "nenhum").

5. **Checar documentação que ficou mentindo.** Procurar em
   `docs/architecture/`, `docs/decisions/`, `docs/runbooks/` e `GOTCHAS.md`
   menções ao comportamento que mudou (grep pelo nome do campo/rota/módulo).
   Atualizar o que for o "como" (arquitetura, runbook) no mesmo commit; se um
   ADR ficou superado, avisar o usuário e sugerir `criar-decisao-adr` com
   status de substituição — não editar ADR aceito silenciosamente.

6. **Se essa mudança afetou o formato de resposta de algum endpoint/interface
   pública**, atualizar o contrato de resposta correspondente no mesmo commit
   (regra "Contratos de resposta sempre atualizados" do `AGENTS.md`).

7. **Renomear o arquivo** prefixando `(DONE) ` (com espaço):
   ```bash
   git mv "tasks/backlog/<fase>/NNNN-slug.md" "tasks/backlog/<fase>/(DONE) NNNN-slug.md"
   ```
   (usar `mv` normal se o arquivo ainda não estiver rastreado.)

8. **Verificar se era a última task pendente da pasta da fase:**
   ```bash
   ls tasks/backlog/<fase>/ | grep -v '^(DONE)' | grep -v README.md
   ```
   - **Vazio:** mover a pasta para `tasks/done/` e atualizar
     `tasks/backlog/README.md`.
   - **Só tarefa(s) travada(s) por motivo externo ao código** (decisão
     humana, dependência de terceiro): mover para `tasks/pendentes/`,
     documentando o motivo e quem destrava no próprio arquivo da task.
   - Caso contrário, deixar onde está.

9. **Atualizar o `README.md` da pasta da fase** com o novo status.

10. **Resumo ao usuário:** o que foi entregue, critérios com evidência,
    desvios/descobertas, tasks novas criadas e docs atualizados. Explicitar
    o que **não** foi feito — é a parte que mais costuma ficar implícita.

11. **Não fazer `git commit`/`push` sozinho**, a menos que o usuário já tenha
    autorizado de forma explícita e duradoura.

## Não fazer

- Não marcar `(DONE)` com critério de aceite desmarcado sem aceite explícito
  do usuário e task de continuação criada.
- Não marcar `(DONE)` com qualquer passo da ordem de verificação falhando.
- Não deixar descoberta de implementação só na conversa — ela vai para o
  Registro de implementação, `GOTCHAS.md` ou uma task nova.
- Não mover a pasta para `done/` se sobrar task pendente por motivo de
  código (só por motivo externo é que vai para `pendentes/`).
- Não fazer commit/push por conta própria sem autorização explícita.
