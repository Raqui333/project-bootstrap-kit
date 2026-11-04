---
name: refinar-task
description: Analisa uma task (ou um pedido ainda solto) ANTES de implementar — reformula o problema, confronta com o código real, separa premissas verificadas de suposições, monta o mapa de impacto, levanta casos de borda e devolve ao usuário todas as perguntas de uma vez, marcando a task como Pronta ou Bloqueada. Use antes de começar qualquer task não trivial, quando o usuário disser "vamos fazer a task X", "refina essa task", "analisa esse pedido", "o que falta para começar", ou quando um pedido chegar de stakeholder/PRD/Linear.
---

# Refinar Task (definição de pronto)

Existe porque o retrabalho mais caro observado nos projetos que usam este kit
não veio de bug de código, e sim de **começar a implementar cedo demais**:

- Implementar como fato algo que o PRD marcava `[PROPOSTA — validar]` e depois
  reverter (máquina de estados, lista de classificações mudando 6 vezes).
- Task com "1. Decidir X" como primeiro passo de implementação — a decisão é
  tomada pelo agente, no meio da execução, sem ninguém perceber.
- Corrigir a leitura de um dado e esquecer a escrita; adicionar valor a um
  enum e esquecer o dashboard que o consumia.
- RFC com "Perguntas em aberto" sem dono, implementado mesmo assim, e
  removido no mesmo dia.
- Simulador/ADR construído a partir de premissas sobre uma API externa, que a
  API real contradisse depois.

Esta skill **não escreve código**. O produto dela é a task atualizada e uma
lista de perguntas para o usuário.

## Passos

1. **Localizar a fonte do pedido.** Ler a task e o que ela referencia (PRD,
   história, mensagem). Se o pedido original não estiver no repositório
   (só existe na conversa, num chat, num `tasks.md` externo), pedir ao usuário
   e registrá-lo **literalmente** na seção "Origem" — paráfrase não serve.
   Se houver mais de uma fonte de requisito (PRD + Linear + pedido verbal),
   ler todas e listar divergências entre elas.

2. **Reformular o problema.** Escrever em "Entendimento" qual dor de quem a
   task resolve e como se saberá que resolveu. Se não conseguir escrever isso
   sem inventar, é sinal de interpretação incerta → pergunta.

3. **Caçar marcadores de incerteza na fonte.** Procurar `[PROPOSTA]`,
   "validar", "candidato", "a definir", "TBD", "ex.:", "pode começar com",
   "ou" entre alternativas de produto. Cada um que a task toque vira uma
   **premissa `Suposição do PRD`** ou uma **pergunta bloqueante** — nunca
   requisito firme.

4. **Confrontar com o código real.** Ler/grep o código afetado (não confiar
   em doc de arquitetura, que pode estar defasada) e preencher o **Mapa de
   impacto**:
   - Todos os consumidores do que vai mudar (grep pelo campo/enum/rota/tabela).
   - Ciclo completo do dado: criar, ler, listar, atualizar, excluir,
     importar, exportar — o pedido costuma mencionar só um deles.
   - Docs/ADRs/runbooks/GOTCHAS que descrevem o comportamento atual.
   Divergência entre o que a task assume e o que o código faz vira nota
   explícita na task.

5. **Classificar premissas.** Para cada afirmação sobre comportamento de lib,
   runtime, API externa ou dado real: `Verificada` (com evidência) ou
   `A verificar`. Premissa crítica sobre sistema externo não verificada →
   propor uma **task de spike antes** (chamar a API real, capturar respostas,
   rodar repro) em vez de seguir com simulador/mock baseado na suposição.
   Consultar `GOTCHAS.md` para a área tocada.

6. **Levantar dependências externas.** Acesso, credencial, VPN/rede, permissão
   em sistema de terceiro, liberação de TI, decisão de pessoa específica,
   restrição de plataforma de deploy, LGPD. Cada uma entra em "Dependências
   externas" com quem destrava.

7. **Percorrer o checklist de casos de borda e não funcionais** do template
   (volume, concorrência, permissão negativa na leitura **e** escrita, fuso,
   encoding, N>1, falha externa, dado sensível). Para cada um aplicável,
   garantir um critério de aceite que o teste.

8. **Separar decisões de implementação.** Se a task contém "decidir",
   "definir se", "escolher entre" sobre comportamento de produto ou
   arquitetura, extrair para uma task de decisão própria (`NNNN-decidir-...`,
   com critério "a task seguinte pode ser executada sem alternativas em
   aberto") e marcar a task de implementação como dependente dela.

9. **Devolver ao usuário, de uma vez só:**
   - O entendimento reformulado (para ele confirmar ou corrigir).
   - Perguntas bloqueantes numeradas, cada uma com as opções e a
     consequência de cada opção (não perguntar "como você quer?" em aberto).
   - Perguntas não bloqueantes com o default que será assumido.
   - Premissas a verificar e spikes propostos.
   - Itens do mapa de impacto que ampliam o escopo ("isso também afeta o
     export e o dashboard — incluo aqui ou abro task separada?").

10. **Atualizar a task** com tudo acima e o campo **Prontidão**:
    - `Pronta` — nenhuma pergunta bloqueante aberta, premissas críticas
      verificadas, mapa de impacto preenchido.
    - `Bloqueada (ver Perguntas)` — caso contrário. Se o bloqueio for externo
      ao código e duradouro, sugerir mover para `tasks/pendentes/`.
    Ao receber respostas, registrá-las na tabela (resposta, data, quem) e
    reavaliar — resposta pode gerar pergunta nova.

## Profundidade proporcional

Task trivial e local (renomear label, ajustar texto) não precisa do ritual
inteiro: basta Origem, Entendimento e uma checagem rápida de consumidores.
Na dúvida sobre se é trivial, fazer o passo 4 (grep de consumidores) — é ele
que revela que não era.

## Não fazer

- Não implementar nada durante o refinamento.
- Não preencher lacuna do requisito com uma regra "razoável" sem registrá-la
  como pergunta ou default explícito — foi assim que regras restritivas
  inventadas pelo agente viraram retrabalho.
- Não tratar `[PROPOSTA]`/"candidato" do PRD como requisito confirmado.
- Não fazer perguntas uma por vez ao longo da implementação — levantar tudo
  aqui, antes.
- Não marcar `Pronta` com pergunta bloqueante sem resposta registrada.
