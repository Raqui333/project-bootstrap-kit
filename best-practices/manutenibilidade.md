# Manutenibilidade — princípios agnósticos de stack

## Documentação viva, não retrospectiva

- `docs/architecture/` (o "como") é atualizado no mesmo commit/PR que muda a
  estrutura — não em um esforço de documentação separado meses depois.
- `docs/decisions/` (o "porquê", estilo ADR) captura a decisão no momento em
  que ela é tomada, com o contexto que existia ali — reconstruir o "porquê"
  depois é sempre pior e mais especulativo que registrar na hora.
- Documentação de módulo é gerada ao **concluir** o módulo (código +
  testes verdes), nunca antes (vira rascunho que fica desatualizado) nem
  "quando sobrar tempo" (nunca sobra).

## Escopo da mudança = escopo do problema

- Uma correção de bug não precisa de refatoração ao redor.
- Uma operação única não precisa de abstração/helper reutilizável.
- Três linhas parecidas são melhores que uma abstração prematura desenhada
  para um caso hipotético futuro.
- Não adicionar tratamento de erro, fallback ou validação para cenário que
  não pode acontecer — confiar em garantias internas do código/framework;
  validar só na fronteira do sistema (input de usuário, API externa).

## Backlog rastreável em vez de lista informal

- Toda unidade de trabalho vira um arquivo de task com critério de aceite
  verificável (`tasks/TEMPLATE.md`) — "vou lembrar" não escala além de uma
  sessão.
- Task nasce em `backlog/`, morre (fica `(DONE)`) só depois da ordem de
  verificação de qualidade (lint/typecheck/test/build) passar — nunca
  marcada como concluída com sinal vermelho.
- Dependência entre tasks é explícita (campo "Dependências" do template),
  não descoberta na hora de implementar.

## Nomenclatura consistente reduz carga cognitiva

- Convenção de boolean (`is`/`has`/`should`/`can`) é seguida mesmo em
  domínio com nome de negócio no idioma local — o prefixo semântico em
  inglês não é uma inconsistência, é alinhamento com convenção já
  onipresente em bibliotecas de terceiro.
- Convenção de nome de arquivo (kebab-case para módulo, PascalCase para
  classe, ou o que o ecossistema da linguagem já convencionar) é definida
  uma vez no `AGENTS.md` e seguida sem exceção silenciosa.

## Revisão de qualidade antes de "pronto"

Nenhuma tarefa é considerada concluída sem passar pela ordem de verificação
definida no `AGENTS.md` (tipicamente: lint → typecheck → format → test →
build → e2e). A ordem importa: começar pelo mais rápido/barato de rodar
permite falhar cedo sem gastar tempo em passos mais lentos.

## Skills/automação como memória de processo

Processos que se repetem (criar task, concluir task, registrar decisão,
documentar módulo) viram uma skill/procedimento documentado — não dependem
de alguém lembrar os passos de cor toda vez. Isso vale tanto para pessoas
quanto para agentes de codificação operando no repositório.
