---
name: criar-spec
description: Cria a SPEC (especificação técnica) de um RFC aceito em docs/specs/NNNN-slug.md, seguindo docs/specs/TEMPLATE.md, a partir do RFC, do spike/validação de API externa e das fixtures. Use quando o usuário pedir para "criar a SPEC do RFC NNNN", "escrever a especificação técnica", "detalhar o RFC" ou depois de um RFC aceito e (se houver) um spike concluído.
---

# Criar SPEC

Uma SPEC descreve **como** implementar o que um RFC aceito decidiu
(`docs/rfcs/`), em detalhe suficiente para virar tasks (`tasks/TEMPLATE.md`). Ela
**não reabre** decisões do RFC. Molde: `docs/specs/TEMPLATE.md` (Partes A, B e C);
as regras de escrita no topo valem sempre. Se existir uma SPEC anterior bem
resolvida no projeto, usá-la como referência de padrão.

## Quando usar

- O RFC está **Aceito**. Se estiver em rascunho/proposta, parar e avisar.
- Se o RFC depende de hipóteses sobre sistema externo e **não há spike** (ou
  documento de validação do spike), avisar: a SPEC sai com
  `a validar` onde faltar prova, e o primeiro passo da ordem de implementação é
  validar. Não inventar comportamento externo.

## Entradas a ler (nesta ordem, **inteiras**)

1. O RFC (`docs/rfcs/NNNN-*.md`): motivação, decisões numeradas, alternativas
   descartadas, riscos, pendências de produto, ADRs a registrar, rastreio dos
   critérios de aceite, hipóteses validadas. Arquivos grandes: ler em partes com
   offset, **nunca** aceitar saída truncada.
2. A validação de API externa e as **fixtures** (estrutura, não só o texto): abrir
   um exemplo de cada tipo e levantar a variação real (valores distintos, campos
   ausentes, formatos). Contar o que a SPEC vai afirmar em vez de supor.
3. `docs/specs/TEMPLATE.md` e `docs/specs/README.md`.
4. O código que a SPEC vai tocar, **para conferir contratos reais** em vez de
   chutar: portas/interfaces, schema de dados (modelos vizinhos, convenções),
   camada de acesso a dados e como se acrescentam métodos/transações, enums de
   auditoria, permissões em uso, constantes e limites comuns, módulos parecidos
   (ADRs citados pelo RFC).
5. `GOTCHAS.md`: varrer os títulos e ler os que tocam o que a SPEC usa (banco,
   runtime, fila, isolamento de dado, migrations, tipos). Cada gotcha que
   condiciona o desenho entra em "Referências" e na decisão que ele muda.
6. `AGENTS.md` / `project-rules.md`: regras de segurança, isolamento, auditoria,
   convenções de módulo.

Citar a origem das stories pela fonte original (Linear/Jira/PRD), nunca por cópia
local solta.

## Passos

1. **Número e nome:** a SPEC leva o **mesmo número do RFC**
   (`docs/specs/NNNN-<slug-do-rfc>.md`). Se já existir, parar e perguntar se é
   revisão.
2. **Copiar o TEMPLATE** e preencher na ordem: cabeçalho (Status `Rascunho`,
   Referências, Resumo de 3 a 5 linhas, Desvios do RFC) → Parte A → Parte B (1–14)
   → Parte C (15–20) → Definição de pronto.
3. **Projetar antes de escrever** (decidir, não deixar "a definir"):
   - modelo de dados com índices/unicidades que garantam a regra de negócio e o
     enquadramento no mecanismo de isolamento de dado, se houver;
   - exclusividade/concorrência por **mecanismo do banco** (índice único), ciente
     dos gotchas de atomicidade;
   - o que é **dado por cliente** (config/tabela) × **constante de sistema
     externo** (código);
   - contratos novos como extensão **opcional** de contrato existente;
   - categorias de erro, retentabilidade e o que sai para fora (sempre pelo
     tratador global de erros);
   - eventos de log, nunca com segredo ou dado devolvido.
4. **Desvios do RFC:** todo refinamento que o spike, as fixtures ou o código
   impuseram vai na tabela, com evidência. Se algo exige **reverter** decisão
   numerada do RFC, parar e avisar o usuário: isso é emenda/novo RFC, não SPEC.
5. **Itens de sistema externo:** verificado → apontar evidência; não verificado →
   `a validar` + como validar + passo da ordem de implementação que valida
   **antes** de quem depende.
6. **Rastreio:** uma tabela por story, **todo** critério de aceite do RFC/stories
   mapeado a seção + teste, ou marcado fora do escopo com motivo.
7. **Pendências de produto** (só produto), cada uma com comportamento padrão e
   impacto se mudar.
8. **Valores de cliente** só como exemplo de configuração ou fixture (dizer que é
   exemplo). Nada de nomes, códigos ou contagens do cliente no texto corrido.
9. **Revisar contra o template** antes de entregar:
   - todas as seções existem e na ordem;
   - cada fato mora em um lugar só (Parte A não repete B/C);
   - `grep` por "a definir", "TODO", "a decidir" e por valores de cliente;
   - referências cruzadas de seção batem (grep nos números citados);
   - Definição de pronto marcada **só** com o que foi conferido; o que não for
     verdade fica `[ ]` com a razão.
10. **Atualizar `docs/specs/README.md`** (índice: SPEC, RFC, título, status).
11. **Reportar ao usuário:** o que foi decidido por inferência do código, desvios
    do RFC, itens `a validar`, pendências novas de produto e o que ficou
    desmarcado na definição de pronto. Não afirmar que algo foi verificado se foi
    só lido.

## Mudança de status

- `Rascunho` → `Aprovada` só quando o usuário aprovar; atualizar o arquivo **e** o
  índice do README.
- `Implementada (data)` quando as tasks da seção 11 estiverem concluídas.

## Não fazer

- Não commitar (o usuário pede quando quiser).
- Não reabrir decisão do RFC dentro da SPEC.
- Não deixar "a definir" técnico: resolver lendo o código ou perguntar.
- Não escrever ADR, task nem documentação de arquitetura aqui; a SPEC só **lista**
  os ADRs a registrar (seção 14) e a ordem de implementação (seção 11). Para as
  tasks, usar `criar-task`; para ADR, `criar-decisao-adr`.
- Não copiar trechos grandes do RFC: referenciar a seção.
- Não criar SPEC retroativa para RFCs anteriores à prática (registrar o corte em
  `docs/specs/README.md`).
- Não usar fixture com dado sensível ou resposta bruta de terceiro como exemplo.
