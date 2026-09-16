---
name: concluir-task
description: Marca uma task de tasks/ como concluída (prefixo "(DONE) "), depois de validar a ordem de verificação do AGENTS.md (lint/typecheck/format/test/build). Use quando o usuário disser "essa task terminou", "marca como feita", "conclui a task X" ou equivalente.
---

# Concluir Task

Fecha o ciclo de uma tarefa em `tasks/`.

## Passos

1. **Confirmar que os critérios de aceite da task foram cumpridos.** Ler o
   arquivo da task (`tasks/backlog/<fase>/NNNN-slug.md` ou
   `tasks/pendentes/...`) e checar a lista de "Critérios de aceite" contra o
   que foi de fato implementado.

2. **Rodar, na ordem definida em `AGENTS.md`, e confirmar que tudo passa
   verde** (nunca marcar `(DONE)` sem isso). Tipicamente:
   ```bash
   <lint>
   <typecheck>
   <format>
   <test>
   <build>
   ```
   Se algum falhar, corrigir antes de continuar — não marcar a task como
   concluída com passos vermelhos.

3. **Renomear o arquivo** prefixando `(DONE) ` (com espaço) antes do número
   sequencial, mantendo o resto do nome:
   ```bash
   git mv "tasks/backlog/<fase>/NNNN-slug.md" "tasks/backlog/<fase>/(DONE) NNNN-slug.md"
   ```
   (usar `git mv` para preservar histórico; se o arquivo não estiver
   rastreado ainda, usar `mv` normal.)

4. **Se essa mudança afetou o formato de resposta de algum endpoint/interface
   pública**, atualizar o contrato de resposta correspondente no mesmo commit
   (regra "Contratos de resposta sempre atualizados" do `AGENTS.md`) — não
   deixar para depois.

5. **Verificar se era a última task pendente da pasta da fase.** Rodar:
   ```bash
   ls tasks/backlog/<fase>/ | grep -v '^(DONE)' | grep -v README.md
   ```
   - Se **vazio** (todas concluídas): mover a pasta inteira para
     `tasks/done/` e atualizar `tasks/backlog/README.md` (remover da tabela
     de fases pendentes, adicionar em fases concluídas).
   - Se restar **só tarefa(s) travada(s) por motivo externo ao código**
     (decisão humana, dependência de terceiro): mover a pasta para
     `tasks/pendentes/` em vez de `tasks/done/`, documentando o motivo no
     próprio arquivo da task travada.
   - Caso contrário, deixar a pasta onde está.

6. **Atualizar o `README.md` da pasta da fase** para refletir o novo status
   da task (se a pasta tiver uma tabela de progresso).

7. **Não fazer `git commit`/`push` sozinho**, a menos que o usuário já tenha
   autorizado isso de forma explícita e duradoura (ex.: instrução salva em
   `AGENTS.md`/memória do agente) — por padrão, quem decide quando e como
   commitar é o usuário.

## Não fazer

- Não marcar `(DONE)` com qualquer passo da ordem de verificação falhando.
- Não mover a pasta para `done/` se sobrar task pendente por motivo de
  código (só por motivo externo é que vai para `pendentes/`).
- Não fazer commit/push por conta própria sem autorização explícita.
