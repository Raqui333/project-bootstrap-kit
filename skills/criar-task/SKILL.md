---
name: criar-task
description: Cria uma nova tarefa em tasks/ seguindo o template e a convenção de numeração/fases do backlog, registrando o pedido original literal e separando decisões de implementação. Use quando o usuário pedir para "criar uma task", "adicionar tarefa ao backlog", "quebrar isso em tasks" ou similar.
---

# Criar Task

Cria um novo arquivo de tarefa dentro de `tasks/backlog/<fase>/` (ou
`tasks/pendentes/<fase>/` se for uma continuação de algo já parado), seguindo
`tasks/TEMPLATE.md` e as convenções do `AGENTS.md`.

O foco é registrar bem a origem e não esconder decisões dentro de passos.

## Passos

1. **Descobrir a fase/pasta certa.**
   - Se o usuário indicou a fase/épico, usar `tasks/backlog/<fase>/`.
   - Se o backlog do projeto não tiver conceito de fase, usar
     `tasks/backlog/` direto.
   - Se for débito técnico novo sem fase existente, criar uma pasta nova em
     `tasks/backlog/<slug-do-tema>/` com um `README.md` próprio (título, foco,
     **pedido original/origem**, fora de escopo, decisões pendentes, lista de
     tarefas).
   - Nunca criar direto em `tasks/done/`.

2. **Registrar a origem.** Copiar para a seção "Origem" o pedido **literal**
   (ou link para arquivo versionado no repo), com quem pediu e quando. Se o
   pedido veio de fora do repositório (chat, reunião, planilha), salvar o
   texto original no repo (ex.: no `README.md` da pasta da fase) — senão ele
   se perde e só sobra a interpretação.

3. **Determinar o próximo número sequencial** dentro da pasta escolhida:
   ```bash
   ls tasks/backlog/<fase>/ | grep -E '^(\(DONE\) )?[0-9]{4}-' | sed 's/^(DONE) //' | sort | tail -1
   ```
   Incrementar, sempre com 4 dígitos.

4. **Separar decisão de implementação.** Se o trabalho exige escolher algo de
   produto/arquitetura que ainda não foi decidido ("decidir se o lembrete é
   manual ou automático", "definir formato de entrega"), criar **duas** tasks:
   `NNNN-decidir-<tema>` (entrega: ADR ou resposta registrada; critério "a
   task seguinte pode ser executada sem alternativas em aberto") e a de
   implementação, dependente dela. Nunca deixar "Decidir X" como passo 1 de
   uma task de implementação.

5. **Copiar `tasks/TEMPLATE.md`** para `tasks/backlog/<fase>/NNNN-slug-da-tarefa.md`
   (slug kebab-case, no idioma de domínio do projeto, ação + resultado).

6. **Preencher o template.** O que já se sabe vai preenchido; o que não se
   sabe vai como pergunta em aberto ou premissa `A verificar` — **não**
   inventar para fechar o campo.
   - Fora de escopo: ao menos um item quando houver vizinhança óbvia
     (ex.: "só leitura — escrita fica na 0005").
   - Critérios de aceite verificáveis, incluindo ao menos um caso negativo/
     de borda.
   - Regras do `AGENTS.md` relevantes ao escopo (só as que se aplicam).
   - **Remover a seção de instruções do topo do template.**

7. **Atualizar o `README.md` da pasta da fase** incluindo a nova task na
   lista, mantendo a ordem numérica. Se a task pertence a uma fase nova,
   atualizar também `tasks/backlog/README.md`.

## Ao quebrar um documento de produto em tasks

Além de cobrir cada requisito do documento, checar o que documentos de
produto costumam **não** dizer e que acabou virando fase/tarefa inserida às
pressas nos projetos anteriores:

- Estrutura transversal (navegação, layout/área autenticada, dashboard
  inicial, onboarding, estados vazios).
- Fundação técnica que só dói depois: fuso/dia civil, paginação no banco,
  auditoria, observabilidade, isolamento de dado, limites de upload/body.
- Itens marcados como proposta/candidato no documento → task de decisão
  antes da implementação.
- Fluxos implícitos de conta: cadastro, recuperação de senha, convite,
  desativação.

Listar para o usuário o que foi incluído por essa checagem e o que foi
deixado de fora de propósito.

## Não fazer

- Não marcar `(DONE)` na criação — isso é da skill `concluir-task`.
- Não inventar estimativas precisas — são ordem de grandeza.
- Não pular campos alegando "não se aplica" sem justificar brevemente.
- Não preencher premissa/requisito com suposição apresentada como fato.
