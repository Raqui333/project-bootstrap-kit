---
name: doc-arquitetura-modulo
description: Gera/atualiza docs/architecture/<modulo>.md para um módulo recém-concluído, via subagent (evita poluir o contexto principal), e referencia o novo doc em overview.md. Use ao terminar a implementação de um módulo (ordem de verificação do AGENTS.md verde) ou quando o usuário pedir para "documentar esse módulo" / "atualizar a arquitetura".
---

# Documentação de arquitetura de módulo

Implementa a regra "Documentação individual de módulos" do `AGENTS.md`: ao
concluir um módulo, gera `docs/architecture/<nome-do-modulo>.md` — **sempre
via subagent**, nunca lendo os arquivos do módulo diretamente na sessão
principal.

## Pré-condição

Confirmar que o módulo está de fato concluído: a ordem de verificação
completa do `AGENTS.md` (lint, typecheck, format, test, build) passando
verde. Se não estiver, não gerar a doc ainda (ou avisar que é uma doc
parcial/rascunho, se o usuário pedir mesmo assim).

## Passos

1. **Identificar o módulo** (pasta do código-fonte correspondente) e se já
   existe `docs/architecture/<modulo>.md` (atualização) ou é novo (criação).

2. **Delegar a um subagent** com um prompt autocontido que instrua o
   subagent a:
   - Ler **todos** os arquivos do módulo (interface pública, regra de
     negócio, contratos de entrada/saída, testes etc.) e qualquer arquivo
     relacionado fora da pasta que seja essencial ao entendimento.
   - Ler 1–2 docs de módulos já existentes em `docs/architecture/` como
     referência de nível de detalhe e estrutura esperada — não copiar
     texto, só o formato.
   - Escrever `docs/architecture/<modulo>.md` cobrindo, nesta ordem:
     1. Visão geral do módulo e sua responsabilidade.
     2. Arquivos que o compõem e a função de cada um.
     3. Endpoints/interfaces expostas (rota/comando/evento, parâmetros,
        contratos de entrada/saída).
     4. Fluxos principais (diagrama de sequência em texto/ASCII quando
        relevante).
     5. Integrações com outros módulos (dependências de/para).
     6. Decisões de design relevantes (e, se alguma decisão for grande o
        suficiente para merecer ADR e ainda não tiver um, sinalizar isso na
        resposta em vez de criar o ADR sozinho — quem decide criar o ADR é
        a skill `criar-decisao-adr`, acionada separadamente).
   - Escrever no idioma de domínio do projeto, sem stack trace/dado sensível
     em exemplos.
   - Retornar um resumo curto (não o conteúdo inteiro do arquivo) do que foi
     escrito.

3. **Atualizar `docs/architecture/overview.md`** na seção de módulos
   existentes, adicionando/ajustando a entrada do módulo com link relativo
   (`Ver [<modulo>.md](./<modulo>.md) para detalhes.`), na mesma ordem em que
   os módulos aparecem no código-fonte.

4. Reportar ao usuário: caminho do doc gerado/atualizado, e se
   `overview.md` foi tocado.

## Não fazer

- Não ler o módulo inteiro na sessão principal antes de delegar — isso é
  exatamente o custo de contexto que a regra do `AGENTS.md` pede para evitar.
- Não gerar a doc antes da ordem de verificação completa passar, salvo
  pedido explícito de rascunho.
- Não criar um ADR dentro desta skill — decisões de design relevantes viram
  ADR pela skill `criar-decisao-adr`, separadamente.
