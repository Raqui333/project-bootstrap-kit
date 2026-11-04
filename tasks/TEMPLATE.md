# TEMPLATE de Tarefa

Copie este arquivo para criar uma nova tarefa. **Preencha todos os campos.**
Remova esta seção de instruções.

> **Antes de implementar**, a task precisa estar **Pronta** (ver skill
> `refinar-task`): nenhuma pergunta bloqueante em aberto, premissas críticas
> verificadas e mapa de impacto preenchido. Task com "Decidir X" como passo de
> implementação não está pronta — a decisão vira uma task própria antes.
>
> **Ao concluir a tarefa**, marque cada critério de aceite com a evidência e
> renomeie o arquivo prefixando `(DONE) ` (ex.: `(DONE) 0001-nome-da-tarefa.md`),
> seguindo a skill `concluir-task`.

---

# Título da Tarefa (ação + resultado, ex.: "Criar schema de dados do domínio X")

## Metadados

| Campo              | Valor           |
| ------------------ | --------------- |
| **Fase/Épico**     | `<fase0X-nome ou "--" se backlog não tiver fases>` |
| **Prontidão**      | `Rascunho` `Bloqueada (ver Perguntas)` `Pronta` |
| **Dificuldade**    | `Fácil` `Média` `Difícil` |
| **Prioridade**     | `Alta` `Média` `Baixa`    |
| **Estimativa humano** | `X–Y h`      |
| **Estimativa LLM** | `X–Y execuções` (quantas iterações/tool-calls típicas) |
| **Dependências (tasks)** | `0001-...`, `--` |
| **Dependências externas** | `<acesso/credencial/rede/decisão de pessoa X/liberação de TI — ou "--">` |

## Origem

- **Pedido original:** `<citação literal (ou link para arquivo versionado no
  repo) do que foi pedido, por quem e quando — PRD §N, mensagem do stakeholder,
  história do Linear etc. Nunca só a paráfrase: a paráfrase é onde a
  interpretação errada se esconde.>`
- **Entendimento (com minhas palavras):** `<reformulação do problema que esta
  task resolve — qual dor de quem, e como saberemos que foi resolvida. Se o
  pedido foi "fazer X", explicar o problema por trás de X.>`

## Descrição

Para que serve a tarefa, em 2–4 frases. Do que quem for executar precisa, o
que deve entregar.

## Fora de escopo

- `<O que alguém poderia razoavelmente achar que está incluído, mas não está —
  e para onde foi (outra task, decisão, "não será feito").>`

## Premissas

| Premissa | Status | Evidência / como verificar |
|---|---|---|
| `<ex.: "a API externa aceita filtro por empresa">` | `Verificada` / `A verificar` / `Suposição do PRD [PROPOSTA]` | `<teste, resposta real capturada, confirmação de quem/quando — ou o spike que vai verificar>` |

Premissa crítica **não verificada** = risco de retrabalho. Ou se verifica
antes (spike), ou vira pergunta bloqueante.

## Perguntas em aberto

| # | Pergunta | Quem responde | Bloqueante? | Resposta (data, quem) |
|---|---|---|---|---|
| 1 | `<pergunta objetiva, com as opções e a consequência de cada uma>` | `<produto / TI / usuário>` | `Sim` / `Não (default assumido: ...)` | |

Pergunta não bloqueante precisa declarar o **default assumido**, para que a
escolha fique visível e reversível — nunca decidir em silêncio.

## Mapa de impacto

Preenchido a partir do código real (grep/leitura), não da memória:

- **Consumidores do que vai mudar:** `<quem lê/usa o campo, enum, endpoint,
  tabela, evento — dashboard, export, import, worker, frontend, relatórios>`
- **Ciclo completo do dado:** `<criar / ler / listar / atualizar / excluir /
  importar / exportar — quais caminhos esta task cobre e quais precisam mudar
  junto>`
- **Docs que descrevem o comportamento atual:** `<docs/architecture/*.md,
  ADRs, runbooks, GOTCHAS que ficarão desatualizados>`

## Casos de borda e requisitos não funcionais

Marcar os que se aplicam e dizer como serão tratados/testados:

- [ ] Volume (paginação/agregação no banco, não em memória; limites)
- [ ] Concorrência (duas requisições simultâneas, idempotência, corrida)
- [ ] Permissão negativa (outro tenant/papel/escopo, acesso por ID direto, na
      leitura **e** na escrita)
- [ ] Datas, fuso e dia civil
- [ ] Encoding/caracteres especiais, valores vazios/nulos, N>1 itens
- [ ] Falha de dependência externa (timeout, 4xx/5xx, indisponível)
- [ ] Dados sensíveis (LGPD, logs)
- [ ] `<outro específico do domínio>`

## Passos sugeridos

1. ...
2. ...

## Critérios de aceite (verificáveis)

- [ ] Critério objetivo, testável, que prova a conclusão.
- [ ] Ao menos um critério cobre um caso negativo/de borda listado acima.
- [ ] Referenciar requisito do documento de produto quando aplicável.

## Regras e convenções aplicáveis (AGENTS.md)

- `<Listar só as convenções do AGENTS.md relevantes ao escopo desta task
  específica — não copiar a lista inteira sem pensar.>`
- Rodar a ordem de verificação completa do `AGENTS.md` ao concluir.

## Referências no documento de produto

- `<Seção/RF relevante, se houver documento de produto/PRD.>`

## Notas / riscos

- Alertas de escala, pontos de atenção. (Decisões em aberto vão em
  "Perguntas em aberto", não aqui.)

## Registro de implementação

Preenchido ao concluir (skill `concluir-task`):

- **Desvios do plano:** `<o que foi feito diferente do descrito acima e por quê>`
- **Descobertas:** `<premissa que se mostrou falsa, gotcha novo (→ GOTCHAS.md),
  pendência nova (→ task nova Nº ...)>`
- **Critérios não cumpridos:** `<nenhum — ou qual e para qual task foi movido>`
