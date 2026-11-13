# Descoberta e requisitos — evitando retrabalho

Compilado dos padrões de retrabalho observados em projetos que usam este kit
(backends NestJS multi-tenant e um app Next.js), e da prática que teria pego
cada um antes. A maioria do retrabalho **não** veio de bug de código: veio de
começar a implementar antes de entender, de pendência descoberta tarde, ou de
task descrita de forma curta demais.

Ferramentas do kit que aplicam isto: template de task (`tasks/TEMPLATE.md`),
RFC (`docs/rfcs/`), `GOTCHAS.md`, skill
`concluir-task`.

## 1. Interpretação do problema

| Padrão observado | Prática |
|---|---|
| PRD marcava `[PROPOSTA — validar com Produto]`; a implementação tratou como requisito; Produto decidiu outra coisa e a máquina de estados foi revertida (ADR substituído, e2e refeito). | Todo item marcado como proposta/candidato/"validar" vira **pergunta bloqueante** ou task de decisão. Nunca requisito firme. |
| Lista de valores de um enum mudou 6 vezes em commits sucessivos — a pendência estava escrita no PRD e nunca foi fechada. | Fechar a lista com produto **antes** da primeira migration; registrar a resposta (quem, quando). |
| Modelo conceitual mal lido ("autorização por processo" quando era "por agente dentro do processo") → tasks canceladas. | Seção "Glossário / modelo conceitual" na RFC, validada com produto antes do schema. |
| Escopo desenhado a partir do PRD; as histórias do Linear pediam outra arquitetura. | Ler **todas** as fontes de requisito na abertura da trilha e reconciliar as divergências antes de criar tasks. |
| Pedido do stakeholder existia só num arquivo fora do repo; sobrou apenas a paráfrase do agente. | Guardar o pedido **literal** no repositório (seção "Origem" da task / README da fase). |
| Lacuna no PRD (ex.: "hora sem hora final") preenchida pelo agente com uma regra restritiva inventada. | Lacuna = pergunta. Se não bloqueia, declarar o default assumido de forma visível. |

## 2. Pendências e dependências descobertas tarde

| Padrão observado | Prática |
|---|---|
| Task de implementação com "1. Decidir se ..." como primeiro passo — decisão tomada no meio da execução. | Decisão vira task própria (`NNNN-decidir-...`) antes da implementação. |
| RFC com "Perguntas em aberto" sem dono; implementado mesmo assim e removido 4h depois. | Pergunta tem dono, marca de bloqueio e resposta datada. RFC com pergunta bloqueante aberta não vira task. |
| Simulador de API externa construído a partir de premissas; testes verdes, API real rejeitava o formato. | **Spike contra o sistema real antes** do ADR e do simulador; simulador alimentado com respostas reais capturadas. |
| ADR de estratégia de credencial escrito antes do spike; spike mostrou token de 12h e o ADR foi refeito. | Spike → ADR, nessa ordem, quando a decisão depende de comportamento externo. |
| APIs davam 403/404, só funcionava via VPN, liberação de TI pendente — descoberto ao executar os lotes. | Checklist de dependências externas (acesso, credencial, rede, permissão, liberação) **na abertura** da trilha, com quem destrava. |
| Restrição de plataforma (volume sem permissão para não-root) descoberta no deploy; containers voltaram para root. | Incluir "ambiente de deploy" nas dependências externas; validar no ambiente real cedo. |
| Feature planejada e cancelada por LGPD. | Checar dado sensível/compliance ao definir a task, não depois do plano. |
| Defeito de fundação (data em UTC adiantando 3h) só apareceu quando chegou a feature que dependia dele. | Checklist de fundação no início do projeto: fuso/dia civil, tipos temporais, paginação no banco, e2e com banco real. |

## 3. Tasks descritas de forma simples demais

| Padrão observado | Prática |
|---|---|
| Novo valor de enum adicionado ao contrato; dashboard descartava os valores novos em silêncio. | **Mapa de impacto**: grep de todos os consumidores do que vai mudar. |
| Corrigida a restrição de CPF na leitura; o PUT continuava apagando o CPF gravado. | Mapear o **ciclo completo** do dado (criar/ler/atualizar/excluir/importar/exportar). |
| Fase inteira buscava tudo e paginava em memória; 11 tasks de correção. | Requisitos não funcionais (volume) explícitos no template. |
| Idempotência sem concorrência nem "mesma chave, body diferente" → efeito duplicado em produção. | Checklist de casos de borda com critério de aceite para os aplicáveis. |
| RFC de RBAC sem casos negativos → vazamento entre setores e PATCH sem checagem, achados depois. | Matriz operação × ator na RFC; critério de aceite negativo em cada task. |
| `delete()` apagava uma linha só; teste usava 1 item. | Testes com N>1; depois de achar um gotcha, **varrer** o mesmo padrão no código. |

## 4. Fechamento fraco

| Padrão observado | Prática |
|---|---|
| A maioria das tasks `(DONE)` com critérios de aceite desmarcados. | `(DONE)` exige cada critério marcado com evidência, ou task de continuação explícita. |
| Auditoria posterior achou dezenas de itens: ADR errado, runbook desatualizado, worker sem escopo. | Ao concluir: grep de docs/ADRs/runbooks que mencionam o comportamento alterado. |
| Afirmação sobre lib ("é o padrão usado por bibliotecas conhecidas") aceita sem teste; derrubada pelo primeiro e2e real. | "Como foi verificado" em ADR; gotcha só com evidência; teste contra o pipeline real, não componente isolado com contexto fake. |
| Gotcha documentado em prosa se repetiu (hash de contrato desatualizado, dois commits "fix"). | Regra que já falhou duas vezes vira checagem automática (script, hook, CI), não mais um parágrafo. |

## 5. Comunicação agente ↔ usuário

- Ao apresentar entendimento ou plano, o agente lista explicitamente:
  premissas, o que fica **fora**, e o que ele vai assumir se ninguém disser o
  contrário. O que fica implícito é o que vira lacuna.
- Perguntas vêm todas juntas, antes de implementar, cada uma com opções e a
  consequência de cada opção — não "como você prefere?" em aberto.
- Ao terminar, o resumo diz o que **não** foi feito tão claramente quanto o
  que foi.
