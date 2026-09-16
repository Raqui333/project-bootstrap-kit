# Project Bootstrap Kit

Kit genérico e **agnóstico de projeto/stack** para iniciar um repositório novo já
com a metodologia de trabalho usada neste tipo de projeto: documentação viva,
backlog rastreável, registro de decisões, e um conjunto de skills para agentes
de codificação (Claude Code, Codex, OpenCode etc.) operarem de forma consistente.

Nada aqui depende de NestJS, Bun, Prisma, Supabase ou qualquer outra escolha
técnica específica — cada arquivo tem marcadores `<ENTRE-COLCHETES>` para você
preencher com as escolhas do projeto novo. O objetivo é a **metodologia**, não a
stack.

## O que compõe o kit

```
project-bootstrap-kit/
├── AGENTS.md                    # instruções para agentes de codificação (copiar para a raiz)
├── docs/
│   ├── architecture/
│   │   ├── overview.md          # template do "como" o sistema conversa entre si
│   │   └── TEMPLATE.md          # template de doc de módulo individual
│   └── decisions/
│       ├── README.md            # índice de ADRs (template)
│       └── TEMPLATE.md          # template de ADR
├── tasks/
│   ├── TEMPLATE.md              # template de tarefa de backlog
│   └── backlog/README.md        # template do índice de fases do backlog
├── skills/                      # skills agnósticas (copiar para .claude/skills/)
│   ├── setup-projeto/           # instala o kit num projeto e sincroniza atualizações depois
│   ├── criar-task/
│   ├── concluir-task/
│   ├── criar-decisao-adr/
│   └── doc-arquitetura-modulo/
└── best-practices/               # compilado de boas práticas, referenciado pelo AGENTS.md
    ├── seguranca.md
    ├── tratamento-de-erros.md
    ├── testes.md
    ├── manutenibilidade.md
    └── ci-cd.md
```

## Como usar em um projeto novo

O jeito recomendado é acionar a skill `setup-projeto` (com o Claude Code, ou
outro agente compatível com skills, já rodando neste diretório
`project-bootstrap-kit`): ela copia a estrutura, entrevista você pelas
informações reais do projeto (stack, comandos, regras de segurança
específicas etc.) e preenche os marcadores. Manualmente, os passos
equivalentes são:

1. **Copiar a estrutura** para a raiz do novo repositório:
   ```bash
   cp project-bootstrap-kit/AGENTS.md ./AGENTS.md
   cp -r project-bootstrap-kit/docs ./docs
   cp -r project-bootstrap-kit/tasks ./tasks
   cp -r project-bootstrap-kit/skills ./.claude/skills
   cp -r project-bootstrap-kit/best-practices ./docs/best-practices
   ```
2. **Preencher os marcadores** de `AGENTS.md` e `docs/architecture/overview.md`:
   nome do produto, stack real, comandos reais (lint/typecheck/test/build),
   convenção de idioma, e as regras de segurança específicas do domínio
   (multi-tenancy, PII, compliance setorial etc. — usar
   `best-practices/seguranca.md` como checklist de partida, não como lista
   fechada).
3. **Criar `CLAUDE.md`** (ou equivalente do agente usado) apontando para
   `AGENTS.md` via import (`@AGENTS.md`), se o agente suportar — mantém uma
   única fonte de verdade em vez de duplicar instruções.
4. **Ajustar `tasks/backlog/README.md`** com as fases reais do roadmap do
   projeto (o template só ilustra a convenção de numeração e status).
5. **Apagar** o que não fizer sentido para o projeto (ex.: se não houver
   conceito de "fase" no backlog, simplificar para uma lista única de tasks).

### Mantendo o projeto em dia com atualizações do kit

Quando `project-bootstrap-kit` ganhar uma skill nova, um item novo em
`best-practices/` ou uma seção nova em algum template, acione `setup-projeto`
de novo (agora apontando para o projeto já instalado) — ela detecta que o kit
já existe no alvo, entra em modo sincronização e só aplica o que estiver
faltando, sempre mostrando diffs e pedindo confirmação antes de sobrescrever
qualquer coisa já customizada (nunca mexe em `AGENTS.md` sem perguntar).

## Princípio por trás do kit

Cada peça resolve um problema recorrente observado ao longo do desenvolvimento
deste tipo de projeto com agentes de codificação:

| Problema recorrente | Peça do kit |
|---|---|
| Setup inicial é manual, repetitivo e fica com marcador `<...>` esquecido | skill `setup-projeto` (modo instalação inicial) |
| Kit evolui mas os projetos que já o instalaram ficam desatualizados | skill `setup-projeto` (modo sincronização) |
| Contexto se perde entre sessões/conversas do agente | `docs/architecture/` (o "como") + `docs/decisions/` (o "porquê") |
| Trabalho vira uma lista informal que ninguém rastreia | `tasks/` com template, numeração e status por arquivo |
| Decisão de arquitetura é esquecida/questionada meses depois sem contexto | ADR (`docs/decisions/`) |
| Documentação de módulo nunca é escrita porque "põe-se depois" | skill `doc-arquitetura-modulo`, acionada ao concluir o módulo |
| Afirmação sobre comportamento de runtime/lib vira "fato" sem nunca ter sido testada | seção "Como foi verificado" do template de ADR |
| Erro de infraestrutura vaza detalhe sensível/stack trace para o cliente | `best-practices/tratamento-de-erros.md` |
| Testes usam mocks que mascaram bugs reais de integração | `best-practices/testes.md` |
| Pipeline de CI cresce sem cobrir os mesmos checks que rodam localmente | `best-practices/ci-cd.md` |

## O que este kit **não** é

- Não é um scaffold de código (não gera `package.json`, controllers, etc.).
- Não prescreve stack, linguagem ou framework.
- Não substitui o PRD/documento de produto do projeto novo — só organiza como
  a equipe (humana + agentes) documenta e executa o trabalho em cima dele.
