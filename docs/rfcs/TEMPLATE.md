# NNNN. Título do RFC (o que se propõe, em uma frase)

> Um RFC decide **o quê** e **por quê**: o problema, o desenho em alto nível,
> as alternativas descartadas e os riscos. A especificação detalhada (modelo de
> dados, contratos, fluxos, testes, ordem de implementação) vem na **SPEC** de
> mesmo número (`docs/specs/`), depois que o RFC é aceito e os spikes terminam.
> Toda seção é obrigatória; quando não se aplicar, escrever "Não se aplica" e o
> motivo em uma linha.
>
> **Regras de escrita (valem para todo RFC):**
> - **Genérico, não amarrado a um cliente.** Se o produto atende vários
>   clientes/tenants, o desenho serve a qualquer um que ative a mesma
>   capacidade. O cliente que originou a demanda é o "caso de origem": seus
>   valores (nomes, códigos, horários, limites) aparecem só como exemplo, valor
>   padrão ou fixture, dizendo que são exemplo. O que varia por cliente é
>   **configuração ou dado**, nunca regra no código.
> - **Pedido original literal.** Registrar a citação (ou link versionado) do que
>   foi pedido, por quem e quando. A paráfrase é onde a interpretação errada se
>   esconde.
> - **Evidência antes de afirmação.** Comportamento do código atual e de
>   sistema/biblioteca de terceiro só é fato depois de verificado (arquivo:linha,
>   spike, documentação oficial). O que não foi verificado entra como hipótese,
>   com plano de validação e plano B.
> - **Decisão numerada.** Toda escolha com trade-off aparece na tabela de
>   alternativas descartadas, com o motivo.
> - **Dúvida técnica vira spike, não pendência.** Pergunta técnica se resolve por
>   spike (ou lendo o código) antes da SPEC. Só **pendência de produto** pode
>   sobrar, cada uma com dono, comportamento padrão e se bloqueia.
> - **Story/PRD não é fonte da verdade.** Se contradisser o código, os ADRs ou o
>   documento de produto, registrar a contradição (skill `interpretar-story`).
>   Citar a fonte original (Linear/Jira/PRD versionado), não cópia solta.
> - **Tamanho:** revisável em uma sentada. Tabela antes de prosa; referenciar ADR,
>   arquitetura e PRD em vez de copiá-los.
> - Apague estas instruções ao copiar.

## Status
Rascunho | Proposta | Aceita (AAAA-MM-DD) | Implementada (AAAA-MM-DD) | Abandonada | Substituída por [NNNN](./NNNN-slug.md)

Uma linha sobre o que vem depois (ex.: "A SPEC será escrita depois do spike").
Mudança posterior de status ou de decisão entra como "Atualização" logo abaixo,
mantendo o texto original como registro.

## Origem
- **Pedido original:** `<citação literal / link versionado, quem, quando>`
- **Fontes de requisito consultadas:** `<PRD vX §N, Linear/Jira, conversa com Y — todas. Divergências entre elas vão em "Contradições" abaixo.>`

## Motivação
O problema real por trás do pedido, em linguagem do negócio: quem sofre, como
sofre hoje, por que agora, como saberemos que foi resolvido. Se há mais de uma
causa, numerá-las.

**Caso de origem.** Quem pediu e a ressalva de que o cliente é só o primeiro
caso (quando o produto for multi-cliente).

### Glossário / modelo conceitual
Termos do domínio e o que significam **aqui** (ex.: "autorização é por agente
dentro de um processo, não por processo"). Validar com produto: interpretação
errada do modelo conceitual é a causa de retrabalho mais cara.

### Estado atual do código (evidência, AAAA-MM-DD)
O que já existe e resolve parte do problema, e o que não existe. Cada fato com
sua fonte.

| Fato | Verificado? | Onde (arquivo:linha, teste, ADR) |
| -- | -- | -- |

Contradições achadas (story × código, ADR citado errado etc.) ficam registradas
aqui com a correção.

## Objetivos e não objetivos

**Objetivos**
-

**Não objetivos**
- (o que fica fora e, quando não óbvio, onde/quando entra)

## Proposta

### Visão geral
Diagrama em texto e a ideia central em poucas linhas: quais peças existem, como
conversam, onde ficam as fronteiras (núcleo genérico separado do específico de
cada integração/cliente).

### Desenho por tema
Uma subseção por decisão de desenho (nomear por letra ou número para o rastreio:
"(a)", "(b)"...). Em cada uma:
- o que muda e em que parte do sistema (módulos, não arquivos linha a linha);
- o que é **configuração/dado por cliente** × o que é constante de sistema
  externo ou da plataforma;
- o essencial do modelo de dados e de contrato **só quando for a decisão** (o
  detalhe de colunas, tipos e rotas vai para a SPEC).

### Casos negativos e de borda
Matriz por operação (criar / ler / listar / atualizar / excluir) × ator (dono,
outro tenant/setor/papel, sem permissão, sistema externo): o que deve acontecer
em cada um. Foi aqui que lacunas de autorização escaparam de RFCs anteriores
("o produto não tem pendências" — e depois várias tasks de correção).

### Segurança e isolamento de dado
Mecanismo concreto (não só o enunciado): isolamento de tenant/cliente,
autorização, segredos, auditoria, dado externo tratado como dado (nunca
instrução), guardas em código, limites operacionais. Ver
`docs/best-practices/seguranca.md` e a seção de segurança do `AGENTS.md`.

### Observabilidade e operação
Logs, alertas, métricas e o que o operador enxerga. Sem segredo nem dado
sensível.

## Rastreio dos critérios de aceite
Quando o RFC nasce de stories: uma tabela por story, cada critério mapeado ao
tema do desenho que o atende. Critério fora do escopo, com motivo. "Não se
aplica" quando não houver stories.

| Critério | Atendido por |
| -- | -- |

## Alternativas descartadas

| # | Decisão | Alternativa descartada | Motivo |
| -- | -- | -- | -- |

## Riscos e quando reconsiderar

| Risco | Mitigação / gatilho para reconsiderar |
| -- | -- |

## Hipóteses a validar no spike
Tudo que o desenho assume sobre sistema externo, biblioteca ou comportamento
ainda não verificado. Cada hipótese tem plano de validação, **comportamento
padrão** e **plano B** se falhar. Spike em sistema externo é **somente leitura**.
Depois do spike, preencher o resultado (data, evidência em
documento de validação do spike linkado, ou `GOTCHAS.md`) e o efeito no
desenho.

| # | Hipótese | Como validar | Se falhar (plano B) | Resultado / efeito |
| -- | -- | -- | -- | -- |

## Pendências de produto
Só decisões de **produto** (o time técnico não resolve sozinho). Cada uma com
dono, o comportamento padrão que vale enquanto não houver resposta e se
**bloqueia** (tudo / uma task / não). Uma pendência bloqueante sem resposta
impede virar tasks; a task que depende dela nasce `Bloqueada` e a cita.
"Nenhuma" se não houver.

| # | Pendência (com opções e consequência de cada) | Quem responde | Bloqueia | Padrão assumido | Resposta (data, quem) |
| -- | -- | -- | -- | -- | -- |

## ADRs que a implementação deve registrar
Decisões com trade-off real que viram ADR (título curto e motivo). Achados sobre
comportamento de terceiro vão para `GOTCHAS.md`, não para ADR.

## Plano em alto nível
Fases curtas, em ordem, com a dependência entre elas (spike → SPEC → passos de
implementação → documentação). O detalhamento e a quebra em tasks ficam na SPEC.

## Consequências
O que muda para quem mantém o sistema depois (positivas e negativas): novas
peças, custo operacional, dívida assumida, o que fica mais fácil e mais difícil.
