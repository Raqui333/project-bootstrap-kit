---
name: criar-task
description: Cria uma nova tarefa em tasks/ seguindo o template e a convenção de numeração/fases do backlog. Use quando o usuário pedir para "criar uma task", "adicionar tarefa ao backlog", "quebrar isso em tasks" ou similar.
---

# Criar Task

Cria um novo arquivo de tarefa dentro de `tasks/backlog/<fase>/` (ou
`tasks/pendentes/<fase>/` se for uma continuação de algo já parado), seguindo
`tasks/TEMPLATE.md` e as convenções do `AGENTS.md`.

## Passos

1. **Descobrir a fase/pasta certa.**
   - Se o usuário indicou a fase/épico, usar `tasks/backlog/<fase>/`.
   - Se o backlog do projeto não tiver conceito de fase, usar
     `tasks/backlog/` direto.
   - Se for débito técnico novo sem fase existente, criar uma pasta nova em
     `tasks/backlog/<slug-do-tema>/` com um `README.md` próprio (seguir o
     padrão dos `README.md` de pastas irmãs: título, foco, lista de tarefas).
   - Nunca criar direto em `tasks/done/` — toda task nasce em `backlog/` (ou
     `pendentes/` se já for sabidamente bloqueada por decisão humana).

2. **Determinar o próximo número sequencial** dentro da pasta escolhida:
   ```bash
   ls tasks/backlog/<fase>/ | grep -E '^[0-9]{4}-' | sort
   ```
   Pegar o maior número existente (ignorando prefixo `(DONE) `) e incrementar,
   sempre com 4 dígitos (`0001`, `0002`, ...).

3. **Copiar `tasks/TEMPLATE.md`** para `tasks/backlog/<fase>/NNNN-slug-da-tarefa.md`
   (slug em kebab-case, no idioma de domínio do projeto, descrevendo ação +
   resultado).

4. **Preencher todos os campos do template:**
   - Metadados (fase, dificuldade, prioridade, estimativas humano/LLM,
     dependências — referenciar outras tasks pelo número quando houver
     dependência real).
   - Descrição (2–4 frases).
   - Passos sugeridos.
   - Critérios de aceite verificáveis (referenciar seção do documento de
     produto/PRD quando aplicável).
   - Regras do `AGENTS.md` relevantes ao escopo da task (não copiar a lista
     inteira sem pensar — só o que se aplica).
   - Referências no documento de produto, se houver.
   - Notas/riscos, se houver.
   - **Remover a seção de instruções do topo do template.**

5. **Atualizar o `README.md` da pasta da fase** incluindo a nova task na
   lista/tabela de tarefas, mantendo a ordem numérica.

6. **Se a task pertence a uma fase totalmente nova**, também atualizar
   `tasks/backlog/README.md` (tabela de fases pendentes) com a nova pasta.

## Não fazer

- Não marcar `(DONE)` na criação — isso é responsabilidade da skill
  `concluir-task`, só depois que a implementação estiver pronta e verificada.
- Não inventar estimativas precisas — são ordem de grandeza.
- Não pular campos do template alegando "não se aplica" sem justificar
  brevemente.
