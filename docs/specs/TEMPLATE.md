# NNNN. Título da especificação

> Uma SPEC descreve **como** implementar o que um RFC aprovado decidiu.
> Ela não reabre decisões do RFC: se algo precisar mudar, registrar em
> "Desvios do RFC" com o motivo. Toda seção é obrigatória; quando não se
> aplicar, escrever "Não se aplica" e o motivo em uma linha.
>
> **Regras de escrita (valem para toda SPEC):**
> - **Um assunto, um lugar.** Cada fato mora em uma seção só; as demais
>   apontam para ela ("ver seção 3.3"). Não repetir tabelas entre a Parte A
>   e a Parte B. Se uma seção só repetiria outra, escreva a referência.
> - **Sem "a definir" técnico.** Dúvida técnica se resolve lendo o código
>   (contratos, enums, padrões existentes) antes de escrever. Só resta como
>   pendência o que é decisão de **produto**.
> - **Sistema externo:** o que ainda não foi verificado leva `a validar` +
>   "como validar"; o que foi verificado aponta a evidência (spike, fixture).
> - **Sem valor de cliente no texto corrido.** Código, nome, loja, preço e
>   contagem de um cliente só entram como exemplo de configuração ou fixture,
>   e dizendo que são exemplo.
> - **Fonte da numeração:** a SPEC leva o mesmo número do RFC que detalha.
> - **Tamanho:** objetivo é revisar em uma sentada. Tabela antes de prosa;
>   sem repetir o RFC (referenciar a seção dele).
> - **Definição de pronto:** só marcar `[x]` depois de conferir contra o
>   texto; item não verdadeiro fica `[ ]`.
> - Apague estas instruções ao copiar.

## Status
Rascunho | Aprovada | Implementada (data)

## Referências
- RFC: [NNNN](../rfcs/NNNN-slug.md)
- Fontes de requisito (stories/PRD): <IDs ou seções, no Linear/Jira/PRD versionado>
- ADRs relacionados: [NNNN](../decisions/NNNN-slug.md), ...
- Gotchas que condicionam o desenho: `GOTCHAS.md` #N, ... (ou "Nenhum")
- Validação de API externa (quando houver): documento de validação do spike

## Resumo
**Três a cinco linhas** (uma ideia por linha): o que será construído e onde
fica no código. Sem nome de cliente; valores de cliente só como exemplo de
configuração ou fixture.

## Desvios do RFC
O que mudou em relação ao RFC aprovado (hipótese do spike que falhou → plano
B, refinamento do desenho), com o motivo. "Nenhum" se não houver. Nenhuma
decisão numerada do RFC pode ser revertida aqui: isso exige novo RFC ou
emenda.

| Item do RFC | Como fica na SPEC | Motivo / evidência |
| -- | -- | -- |

---

# Parte A — Problema, requisitos e comportamento
Define **o quê** e **por quê**. Sem detalhe de implementação (isso é a Parte B).

## A.1 Contexto e definição do problema
Situação atual, dor, evidência (referenciar a "Motivação" do RFC em vez de
copiá-la) e a frase do problema em uma linha.

## A.2 Objetivos
Lista numerada (O1, O2...). Cada objetivo é verificável.

## A.3 Escopo
O que entra.

## A.4 Não objetivos
O que fica fora e, quando não óbvio, onde/quando entra.

## A.5 Requisitos funcionais

| ID | Requisito |
| -- | -- |

Cada RF é uma frase testável ("O sistema faz X quando Y").

## A.6 Requisitos não funcionais

| ID | Requisito | Meta / forma de verificação |
| -- | -- | -- |

Desempenho, isolamento, segurança, limites, compatibilidade que precisam de
meta ou de teste. Detalhes de como atingir ficam na seção 15 e na Parte B.

## A.7 Casos de uso

| ID | Ator | Caso | Resultado |
| -- | -- | -- | -- |

## A.8 Restrições técnicas
Tecnologia, regras do projeto (`AGENTS.md`, ADRs) e gotchas que
limitam o desenho, com o motivo de cada uma.

## A.9 Integrações e dependências
Tabela curta (sistema/módulo, para que serve, fronteira). Detalhe de contrato
fica nas seções 3.1 e 3.3.

| Dependência | Uso | Fronteira |
| -- | -- | -- |

---

# Parte B — Especificação técnica
Define **como**.

## 1. Modelo de dados e migrations

### 1.1 Modelos novos e alterados
Para cada entidade/tabela/modelo (arquivo de schema do projeto: `<caminho>`):

| Modelo | Campo | Tipo | Nulo | Default | Observação |
| -- | -- | -- | -- | -- | -- |

- Índices e restrições de unicidade (e a regra de negócio que cada uma garante).
- Isolamento de dado (tenant/cliente/organização), **se o projeto tiver**: a
  entidade entra no mecanismo de isolamento (RLS, filtro, lista de modelos
  escopados)? Sim, ou motivo explícito para não entrar. Entra também no helper
  de limpeza de banco dos testes?

### 1.2 Migrations
- Ordem das migrations e o que cada uma faz.
- Dados existentes: backfill necessário? Migration reversível?
- Retenção/limpeza de dados (quanto tempo, por qual rotina).
- Dimensionamento esperado (ordem de grandeza de linhas e bytes).

## 2. Esquema de configuração
Configuração por instância/cliente (ex.: um campo JSON de configuração),
validada por schema.

| Campo | Tipo | Obrigatório | Default | Validação | Exemplo |
| -- | -- | -- | -- | -- | -- |

- Onde o schema mora (seed, arquivo do módulo).
- Como a alteração de configuração entra em vigor (imediato, próximo ciclo).
- Valores do caso de origem aparecem **só** na coluna "Exemplo" ou em fixture.

### 2.1 Validações de entrada
Toda entrada externa (usuário, sistema, agente) validada na fronteira:

| Entrada | Regra | Falha |
| -- | -- | -- |

## 3. Contratos

### 3.1 Contratos internos
Interfaces/tipos entre módulos, com assinatura TypeScript e quem
implementa/consome. Conferir no código a assinatura real das portas
existentes antes de escrever. Respeitar as fronteiras de módulo já decididas
em ADR/`AGENTS.md` (quem pode importar quem). Incluir a estrutura de pastas quando houver módulo
novo.

### 3.2 Operações expostas a consumidores não humanos
Aplicável se o módulo expõe operações a agente de IA, job ou sistema
externo (senão: "Não se aplica"). Para cada operação, com a autorização que o
projeto exigir:

- Nome da operação e descrição vista por quem a consome.
- Schema de entrada e de saída (exemplo de envelope).
- Limites: itens máximos, tamanho máximo, tempo máximo.
- Semântica de erro (o que vira exceção, o que vira resposta vazia).

### 3.3 Contrato com o sistema externo
Rotas chamadas, método, parâmetros, formato de resposta usado. Marcar cada
comportamento **ainda não verificado** como `a validar`, com **como validar**
(chamada real, fixture gravada, documentação oficial). Comportamento já
verificado aponta a evidência.

| Rota / recurso | Uso | Parâmetros | Situação |
| -- | -- | -- | -- |

## 4. Interface pública (HTTP, CLI, eventos)

| Método | Rota | Permissão | Corpo / parâmetros | Resposta | Erros |
| -- | -- | -- | -- | -- | -- |

- Contratos de entrada e de resposta (DTO/schema usado na documentação de
  API), atualizados no mesmo PR (ver `AGENTS.md`, "Contratos de resposta").
- Auditoria (se o projeto tiver): tipo de operação **existente** a reutilizar
  ou **novo** a acrescentar, para toda mudança de estado sensível.

## 5. Fluxos

### 5.1 Fluxo principal
Sequência passo a passo (diagrama em texto quando ajudar): gatilho, etapas,
pontos de gravação, critério de sucesso/publicação.

### 5.2 Gatilhos e agendamento
O que dispara o fluxo (agenda, evento, chamada manual), idempotência e
concorrência (duas execuções ao mesmo tempo para a mesma instância; qual
mecanismo do banco/infra garante a exclusividade, ciente dos gotchas de
atomicidade).

### 5.3 Fluxos de falha
O que acontece em cada falha relevante: parcial, total, tempo esgotado,
credencial recusada. O que é preservado e o que é descartado.

| Falha | O que acontece | Preservado | Descartado |
| -- | -- | -- | -- |

### 5.4 Fluxos alternativos
Variações legítimas do fluxo principal (primeira execução, disparo manual com
execução em andamento, desativação no meio, reativação etc.).

### 5.5 Edge cases
Entradas e estados limite, com o comportamento definido para cada um.

| Caso | Comportamento |
| -- | -- |

### 5.6 Regras de transformação
Lógica **em código** (semântica do sistema externo ou da plataforma, igual
para qualquer cliente): filtros, normalizações, mapas, deduplicação,
saneamento. Uma subseção por regra, numeradas (5.6.1...). O que varia por
cliente **não** entra aqui: vai para a seção 6.

## 6. Regras de negócio como dado
Toda regra que varia por cliente (filtros, listas de exclusão, horários,
limites) é **configuração ou dado**, nunca `if`/enum/constante no código.

| Regra | Onde fica (config, seed, tabela) | Default | Como é aplicada |
| -- | -- | -- | -- |

Constantes de código só para o que é do **sistema externo** (igual para
qualquer cliente dele) ou da plataforma, com o motivo (listar e apontar para
a seção 5.6).

## 7. Erros
- Categorias internas de erro (código, causa, se é retentável).
- O que chega ao consumidor: sempre via exceção de aplicação e tratador global
  de erros, sem detalhe de terceiro (ver `docs/best-practices/tratamento-de-erros.md`).
- Política de nova tentativa (quais erros, quantas vezes, espera).

| Categoria | Causa | Retentável |
| -- | -- | -- |

| Situação | Categoria interna | Retentável | Resposta ao consumidor / HTTP |
| -- | -- | -- | -- |

## 8. Observabilidade
- Logs estruturados: eventos, campos, nível. Nunca segredo, token, payload
  bruto ou dado sensível.
- Alertas (evento, condição, para quem).
- Métricas/traces (ferramenta de observabilidade do projeto) e o que é registrado na execução.

| Evento | Nível | Quando | Campos |
| -- | -- | -- | -- |

## 9. Segurança e isolamento de dado
Checklist com o mecanismo concreto de cada item:

- [ ] Isolamento de dado (tenant/cliente/organização), com o mecanismo do
      projeto (RLS, filtro obrigatório, guard); SQL raw também filtra; rotinas
      fora de requisição definem o escopo explicitamente.
- [ ] Autorização por operação (papel/escopo/permissão) no ponto certo.
- [ ] Segredos só no cofre/configuração; nunca em log, trace, erro, resposta
      ou registro de auditoria.
- [ ] Dado externo é dado, nunca instrução (sanitização; para agentes de IA,
      formato de entrega ao modelo).
- [ ] Guardas de escrita/somente leitura no sistema externo, em código.
- [ ] Limites operacionais (timeout, tamanho, taxa) em código.
- [ ] Auditoria das operações sensíveis.

Itens que não se aplicam ao projeto: "Não se aplica" + motivo. Os itens do checklist ficam `[ ]` na SPEC (são verificados na implementação);
cada um descreve o mecanismo, não só repete o enunciado.

### 9.1 Autenticação e autorização

| Superfície | Autenticação | Autorização |
| -- | -- | -- |

## 10. Estratégia de testes

| Camada | O que cobre | Arquivo(s) | Dados |
| -- | -- | -- | -- |
| Unitário | | `<padrão do projeto>` | mocks de rede/tempo |
| Integração/e2e | | `<padrão do projeto>` | banco real, não mock (`docs/best-practices/testes.md`) |
| Isolamento de dado | X não vê dado de Y, mesmo por ID direto (se aplicável) | | |
| Fixtures do sistema externo | | `<pasta de fixtures>` | respostas reais projetadas, sem dado sensível |

- Caso de aceite das stories → teste que o cobre (ver seção 12).
- Testes de fronteira existentes que não podem ser afrouxados.
- Quais e2e rodar localmente e se a suíte completa é necessária
  (`AGENTS.md`, "Ordem de verificação").

## 11. Ordem de implementação
Passos pequenos, cada um verificável e com lint/typecheck/test verdes.
Cada passo vira uma task (`tasks/TEMPLATE.md`). Validações de sistema
externo marcadas `a validar` vêm **antes** dos passos que dependem delas.

| # | Passo | Depende de | Pronto quando |
| -- | -- | -- | -- |

## 12. Rastreio dos critérios de aceite
Todo critério de aceite das stories de origem aparece aqui, uma tabela por
story.

| Story | Critério | Seção da SPEC | Teste | Observação |
| -- | -- | -- | -- | -- |

Critério fora do escopo: dizer qual, e por quê (story futura, decisão do
RFC, pendência de produto). Itens das stories que o RFC substituiu também
ficam listados com o motivo.

## 13. Pendências
Só pendências de **produto** (são as "questões em aberto"). Nenhum "a
definir" técnico pode sobrar nesta SPEC. Cada pendência tem o comportamento
padrão que será implementado se não houver resposta.

| # | Pendência | Comportamento padrão | Impacto se mudar depois |
| -- | -- | -- | -- |

## 14. ADRs a registrar
Decisões com trade-off real que viram ADR na implementação (título curto e
motivo). Numeração sequencial só no momento do registro. Achados sobre
comportamento de terceiro vão para `GOTCHAS.md`, não para ADR.

---

# Parte C — Atributos de qualidade e análise

## 15. Performance, escalabilidade e confiabilidade
Só o que **não** está nos requisitos não funcionais (A.6) nem no desenho dos
fluxos: como o desenho atinge as metas, como cresce com o volume, o que
degrada e como se recupera.

- **Performance:**
- **Escalabilidade:**
- **Confiabilidade:**

## 16. Compatibilidade
Banco (migrations aditivas?), API (campos opcionais, rotas novas), contratos
internos, módulos existentes afetados e rollback.

## 17. Invariantes
Lista numerada de coisas que **sempre** valem (verificáveis por teste ou
consulta). Cada uma vira, no mínimo, uma asserção de teste.

## 18. Trade-offs

| Decisão | Ganho | Custo |
| -- | -- | -- |

## 19. Riscos

| Risco | Prob. | Impacto | Mitigação |
| -- | -- | -- | -- |

## 20. Premissas
O que se assume verdadeiro sem ter sido provado; cada premissa que falhar
deve ter efeito conhecido (ver riscos).

## Definição de pronto da SPEC
Marcar `[x]` só depois de conferir contra o texto.

- [ ] Todo critério de aceite das stories mapeado para uma seção, ou marcado
      fora do escopo com motivo.
- [ ] Nenhum "a definir" técnico; só pendências de produto, cada uma com
      comportamento padrão. Contratos, enums, tipos de auditoria e formatos
      foram conferidos no código.
- [ ] Nenhum valor específico de cliente fora de exemplo de configuração ou
      fixture.
- [ ] Todo comportamento de sistema externo não verificado marcado como
      `a validar`, com como validar.
- [ ] Cada fato aparece em um lugar só (sem tabela repetida entre as Partes
      A, B e C).
- [ ] Respeita as regras de segurança do `AGENTS.md` (e `project-rules.md`,
      se houver): isolamento de dado, autorização, auditoria, dado externo
      nunca vira instrução.
