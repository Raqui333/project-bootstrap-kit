---
name: criar-decisao-adr
description: Registra uma decisão técnica/arquitetural em docs/decisions/NNNN-slug.md (estilo ADR) e atualiza o índice docs/decisions/README.md. Use quando uma escolha de biblioteca/padrão/trade-off relevante for tomada, ou quando o usuário pedir para "registrar essa decisão", "criar um ADR" ou "documentar por que fizemos assim".
---

# Criar Decisão (ADR)

Implementa a seção "Registro de decisões (ADR)" do `AGENTS.md`.
`docs/decisions/` explica o **porquê** (motivação, trade-offs, alternativas
descartadas) — diferente de `docs/architecture/`, que explica o **como**.

## Quando vale um ADR (e quando não vale)

Vale a pena criar um ADR quando a decisão:
- Escolhe uma biblioteca/framework/padrão em vez de outro concorrente
  plausível.
- Envolve trade-off real (ex.: performance vs. simplicidade, custo vs.
  resiliência).
- Resolve um gotcha não óbvio que alguém no futuro poderia tropeçar de novo.
- Poderia razoavelmente ser questionada ou revertida por alguém sem esse
  contexto.

**Não** vale ADR para toda mudança de código — se for só a execução natural
de algo já decidido, ou um detalhe de implementação sem trade-off real, não
criar. Na dúvida, perguntar ao usuário antes de gerar o arquivo.

## Passos

1. **Determinar o próximo número sequencial**:
   ```bash
   ls docs/decisions/ | grep -E '^[0-9]{4}-' | sort | tail -1
   ```
   Incrementar, sempre 4 dígitos.

2. **Copiar `docs/decisions/TEMPLATE.md`** para
   `docs/decisions/NNNN-slug-curto-da-decisao.md` (slug kebab-case,
   descrevendo a decisão em si, não o problema — ex.
   `0029-modulo-comum-de-integracao.md`, não
   `0029-problema-de-duplicacao-de-client.md`).

3. **Preencher as 4 seções do template:**
   - `# NNNN. Título da decisão` — título curto e afirmativo (o que foi
     decidido).
   - `## Status` — `Aceita` na grande maioria dos casos (só usar outro
     status se o usuário pedir explicitamente, ex. `Proposta`, `Substituída
     por NNNN`).
   - `## Contexto` — problema, requisito ou restrição que motivou.
   - `## Decisão` — o que foi decidido e como foi implementado, citando
     arquivos/módulos relevantes (paths reais do repo, não genéricos).
   - `## Como foi verificado` — a evidência concreta (teste, log, repro) por
     trás de qualquer afirmação sobre comportamento de runtime/framework/lib
     de terceiro. Se não houver como verificar, dizer isso explicitamente.
   - `## Consequências` — trade-offs, alternativas descartadas e por quê,
     gotchas conhecidos, implicações para código futuro.

4. **Atualizar `docs/decisions/README.md`**, adicionando uma linha na tabela
   (mesmo formato das existentes: `| [NNNN](./NNNN-slug.md) | Decisão |
   Status |`), ao final da tabela (ordem cronológica de numeração).

5. **Se a decisão também mudar comportamento documentado em
   `docs/architecture/`**, avisar que o doc de arquitetura do módulo
   correspondente pode precisar de atualização (acionar
   `doc-arquitetura-modulo` separadamente) — não editar o doc de arquitetura
   dentro desta skill.

## Não fazer

- Não criar ADR para toda mudança — só decisão com trade-off real ou
  reversível sem contexto (ver seção acima).
- Não pular a atualização do índice `docs/decisions/README.md` — é parte da
  mesma entrega, não um "depois".
- Não usar números fora de sequência ou reutilizar um número já usado.
- Não apresentar hipótese/suposição como fato verificado na seção "Como foi
  verificado" — dizer explicitamente quando é hipótese não testada.
