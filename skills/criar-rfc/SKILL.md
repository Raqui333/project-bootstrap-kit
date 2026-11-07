---
name: criar-rfc
description: Cria um RFC em docs/rfcs/NNNN-slug.md seguindo docs/rfcs/TEMPLATE.md a partir de um pedido de produto/stories, de um problema técnico ou de uma ideia, com evidência do código atual, alternativas, riscos, hipóteses de spike e pendências de produto. Use quando o usuário pedir para "criar um RFC", "escrever a proposta", "propor o desenho de X" ou antes de implementar algo que muda arquitetura, modelo de dados ou integra sistema externo.
---

# Criar RFC

Um RFC decide **o quê** e **por quê** antes de qualquer implementação. A SPEC
(skill `criar-spec`) detalha o **como** depois que o RFC é aceito. Molde:
`docs/rfcs/TEMPLATE.md` (as regras de escrita no topo valem sempre). Se existir
um RFC anterior bem resolvido no projeto, usá-lo como referência de padrão.

## Quando vale um RFC (e quando não)

Vale quando a mudança: cria módulo ou camada nova, muda modelo de dados ou
fronteira entre módulos, integra sistema externo, mexe em segurança/isolamento de
dado, ou tem mais de um desenho plausível. **Não** vale para bug, refactor local
ou execução natural de algo já decidido: nesses casos, task (`criar-task`) ou ADR
(`criar-decisao-adr`). Na dúvida, perguntar.

## Entradas

1. O pedido, **literal** (stories com ID no Linear/Jira, trecho do PRD, mensagem
   do stakeholder) ou o problema descrito. Se vier de story, rodar antes a skill
   `interpretar-story`. Citar a fonte original, nunca uma cópia local solta.
2. O código atual **que a proposta toca** (não resumir de memória): módulos,
   schema de dados, contratos/portas, serviços vizinhos.
3. ADRs (`docs/decisions/`), arquitetura (`docs/architecture/`), `GOTCHAS.md`,
   documento de produto vigente, `AGENTS.md` e `project-rules.md` (se houver).
   RFCs anteriores parecidos servem de referência.
4. `docs/rfcs/TEMPLATE.md` e `docs/rfcs/README.md`.

## Passos

1. **Número:** o próximo da sequência.
   ```bash
   ls docs/rfcs | grep -E '^[0-9]{4}-' | sort | tail -1
   ```
   Slug kebab-case descrevendo a proposta (não o problema), no idioma do projeto.
2. **Copiar o TEMPLATE** e preencher na ordem: Status (`Rascunho`) → Origem →
   Motivação + Glossário + Estado atual do código → Objetivos/não objetivos →
   Proposta (inclui casos negativos, segurança, observabilidade) → Rastreio →
   Alternativas → Riscos → Hipóteses → Pendências de produto → ADRs → Plano →
   Consequências.
3. **Levantar a evidência primeiro.** Cada afirmação sobre o código leva fonte
   (`arquivo:linha`, ADR, gotcha). Sistema/biblioteca de terceiro: só é fato se
   foi testado; o resto vira hipótese. Se uma story citar ADR ou comportamento
   errado, registrar a correção na seção de estado atual.
4. **Desenhar genérico** (quando o produto for multi-cliente). Separar o que é
   núcleo reutilizável do que é do sistema externo e do que é **configuração por
   cliente** (dado, nunca `if` no código). Valores do cliente de origem só como
   exemplo/padrão/fixture.
5. **Decidir com trade-off visível.** Toda escolha relevante entra na tabela de
   alternativas descartadas (motivo concreto). Havendo duas opções plausíveis e
   impacto alto, apresentar a **recomendação**, não um survey.
6. **Segurança e isolamento** com mecanismo concreto (não só o enunciado).
   Conferir a seção de segurança do `AGENTS.md`.
7. **Hipóteses:** liste tudo que o desenho assume e não foi provado; para cada
   uma: como validar, comportamento padrão e plano B. Spike em sistema externo é
   **somente leitura** (nunca chamar rota de escrita) e não grava segredo; a
   evidência vai para um documento de validação linkado do RFC (ou `GOTCHAS.md`). Dúvida técnica
   **não** vira "pergunta em aberto": vira hipótese + spike.
8. **Pendências de produto:** só o que é decisão de produto, cada uma com dono,
   padrão assumido e se bloqueia.
9. **Rastreio:** se há stories, mapear cada critério de aceite ao tema do desenho
   (ou fora do escopo com motivo).
10. **Revisar contra o template:** todas as seções, genérico (grep por valores de
    cliente fora de exemplo), nenhum "a definir" técnico, referências verificadas
    (links relativos válidos).
11. **Atualizar `docs/rfcs/README.md`** (índice: RFC, título, status).
12. **Reportar** ao usuário: o que foi inferido do código, contradições achadas
    nas stories, hipóteses que pedem spike, pendências de produto e a recomendação
    principal. Não afirmar que algo foi verificado se foi só lido.

## Ciclo de vida

- `Rascunho` → `Proposta` (em revisão) → `Aceita (data)` só quando o usuário
  aceitar; atualizar arquivo **e** índice.
- Spikes rodam depois de aceito (ou antes, se condicionam o aceite); o resultado
  entra na seção de hipóteses. Em seguida: SPEC (`criar-spec`).
- `Implementada (data)` ao concluir; decisão posterior que muda o RFC entra como
  "Atualização" no topo, mantendo o texto original como registro.
- `Substituída por NNNN` quando outro RFC o troca.

## Não fazer

- Não escrever detalhe de SPEC (colunas, tipos, rotas, passos): referenciar a SPEC
  futura.
- Não amarrar o desenho a um cliente; não copiar valor de cliente como regra.
- Não afirmar comportamento de terceiro sem evidência; não deixar pergunta técnica
  em aberto.
- Não commitar (o usuário pede quando quiser).
- Não criar ADR, SPEC nem task aqui; só listar os ADRs a registrar e o plano em
  alto nível.
- Não reutilizar número de RFC.
