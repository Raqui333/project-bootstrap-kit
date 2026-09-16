# AGENTS.md

Instruções para agentes de codificação (Claude Code, Codex, OpenCode etc.)
trabalhando neste repositório. Leia isto antes de qualquer alteração.

> **Template.** Preencha todo trecho entre `<...>` com a realidade do projeto
> e apague esta observação. Mantenha as seções cujo princípio se aplica
> independente de stack; adapte só o conteúdo concreto (comandos, convenções
> de nome, stack).

---

## Sobre o projeto

`<Uma ou duas frases descrevendo o produto/sistema e o domínio de negócio.
Referenciar o documento de produto/PRD se existir, ex.: "Ver README.md para
visão de produto e arquitetura.">`

**Stack:** `<listar as escolhas reais — linguagem, framework, banco, fila,
observabilidade etc. Só o que já é fato no projeto, não aspiração.>`

## Linguagem e idioma

- `<Definir idioma de comentários, nomes de variáveis públicas e documentação.
  Ex.: "PT-BR" ou "inglês". Ser explícito também sobre logs internos e
  mensagens de erro visíveis ao usuário final, que podem ter regra diferente.>`

## Documentação de arquitetura

- A pasta `docs/architecture/` explica **como o sistema conversa entre si**
  (fluxo de dados, integração entre módulos, camadas e dependências). Começa
  por `docs/architecture/overview.md`.
- Alterações estruturais (novos módulos, mudança de fluxo ou de camadas) devem
  ser refletidas na documentação de arquitetura no mesmo commit/PR da mudança.

### Documentação individual de módulos

Ao **concluir a implementação de um módulo** (todos os arquivos criados e as
verificações de qualidade — ver "Ordem de verificação" abaixo — passando),
**criar um arquivo de documentação** em `docs/architecture/<nome-do-modulo>.md`
descrevendo:

- Visão geral do módulo e sua responsabilidade.
- Arquivos que o compõem e a função de cada um.
- Endpoints/interfaces expostos (rotas, comandos, eventos, parâmetros —
  o que for aplicável à natureza do módulo).
- Fluxos principais (diagramas de sequência em texto quando relevante).
- Integrações com outros módulos (dependências).
- Decisões de design relevantes.

O overview (`docs/architecture/overview.md`) deve ser atualizado para
referenciar o novo documento com link relativo (`[nome.md](./nome.md)`).

**Usar um subagent** para escrever a documentação do módulo, quando a
ferramenta de codificação suportar. Isso evita poluir o contexto da sessão
principal com leitura extensa de arquivos e escrita de markdown. O subagent
deve ler todos os arquivos do módulo e gerar o `.md` completo.

### Registro de decisões (ADR)

A pasta `docs/decisions/` explica **por que** o sistema é como é (motivação,
trade-offs, alternativas descartadas) — diferente de `docs/architecture/`, que
explica **como** ele funciona. Índice em `docs/decisions/README.md`.

Toda **decisão técnica ou arquitetural importante** — escolha de
biblioteca/framework, padrão adotado em vez de outro, trade-off de performance
vs. simplicidade, mudança que resolve um gotcha não óbvio, etc. — deve ser
registrada em um arquivo separado `docs/decisions/NNNN-slug-curto.md`
(numeração sequencial de 4 dígitos, estilo ADR), usando
`docs/decisions/TEMPLATE.md` como base.

Nem toda mudança de código vira ADR — apenas decisões com trade-off real ou
que alguém no futuro poderia questionar/reverter sem esse contexto.
`docs/decisions/README.md` deve ser atualizado no mesmo commit/PR,
referenciando o novo arquivo com link relativo.

Toda afirmação sobre comportamento de runtime, framework ou biblioteca de
terceiro citada em um ADR precisa apontar a evidência concreta que a comprova
(teste, log, repro reproduzível) — "faz sentido porque X" não é evidência, é
hipótese. Se não houver como verificar, dizer isso explicitamente em vez de
apresentar como fato (ver `docs/decisions/TEMPLATE.md`).

### Contratos de resposta (DTOs/schema de API) sempre atualizados

Sempre que a criação ou edição de um módulo alterar o formato da resposta de
um endpoint/interface pública (campo novo/removido/renomeado, mudança de
tipo, novo endpoint etc.), **atualizar o contrato de resposta correspondente**
(classe/schema/DTO usado para gerar a documentação de API, quando o projeto
tiver um) no mesmo commit/PR da mudança — nunca deixar para depois.

`<Se o projeto usar geração automática de doc de API a partir de tipos runtime
(ex.: OpenAPI a partir de decorators), explicar aqui por que a interface
TypeScript/type pura não basta (apagada em tempo de compilação) e qual classe
usar.>`

## Comandos essenciais

```bash
<gerenciador de pacotes> install     # instalar dependências
<comando> build                     # build de produção
<comando> dev                       # dev server com watch
<comando> lint                      # linter (nome da ferramenta real)
<comando> typecheck                 # checagem de tipos, se aplicável
<comando> format                    # formatador
<comando> test                      # testes unitários
<comando> test:e2e                  # testes end-to-end
<comando> test:cov                  # cobertura
```

## Ordem de verificação antes de commitar

`<Listar a ordem real de comandos que precisam passar verde antes de
considerar qualquer tarefa concluída. Manter a ordem "mais rápido/mais barato
de rodar" → "mais lento/mais caro", para falhar cedo.>`

1. `<lint>`
2. `<typecheck>`
3. `<format>`
4. `<test unitário>`
5. `<build>`
6. `<test e2e, se rodar localmente/no fluxo de conclusão de task>`

---

## Regras de segurança — não-negociáveis

`<Adaptar esta seção ao domínio real do projeto. Ver
docs/best-practices/seguranca.md para o checklist completo e o racional por
trás de cada item. Abaixo, os princípios que se repetem em praticamente
qualquer projeto — preencher a coluna "como se aplica aqui".>`

### Segredos e credenciais

- Nunca commitar `.env`, chaves de API, connection strings ou tokens — mesmo
  em branch temporária ou exemplo de código.
- Nunca logar payloads que possam conter segredos, tokens de sessão ou dados
  pessoais sensíveis. Revisar logs de erro/debug antes de fazer merge.
- Usar sempre o módulo/mecanismo de configuração do framework para variáveis
  de ambiente; nunca hardcode credencial, nem em teste.
- Se precisar de uma credencial nova, parar e perguntar — não inventar
  placeholder que alguém possa esquecer de trocar.

### Nunca desabilitar proteções para "fazer o teste passar"

Não comentar guards/middlewares de autorização, não remover validação de
entrada, não desabilitar isolamento de dado multi-tenant/multi-cliente, não
aumentar permissão de um papel só para destravar um bug local. Se uma
proteção está atrapalhando um teste, o teste (ou o código) está errado — não
a proteção.

### Isolamento de dado (multi-tenancy, multi-cliente, multi-organização)

`<Se o projeto tiver qualquer noção de "dado pertence a X e não pode vazar
para Y" — tenant, cliente, organização, workspace — descrever aqui o
mecanismo real (RLS? filtro em toda query? guard/middleware?) e deixar
explícito que **toda** feature nova que toque nesse dado precisa de teste de
integração provando isolamento (X não enxerga dado de Y, mesmo tentando IDs
diretos). Ver docs/best-practices/seguranca.md.>`

### Auditoria

`<Se o projeto tiver operações sensíveis (criação/alteração/exclusão de
entidade crítica, mudança de permissão, intervenção humana em processo
automatizado etc.), listar quais e exigir que todo endpoint/service que altere
esse estado gere um registro de auditoria na mesma PR.>`

---

## Convenções de código

`<Preencher com os padrões reais: estrutura de módulos, decorators/anotações
usados, configuração de strict mode, convenção de import, framework de teste,
localização de testes unitários vs. e2e, convenção de nomes de arquivo.>`

### Nomenclatura de booleans

- Função/método/variável que retorna booleano usa um prefixo semântico
  consistente (`is`, `has`, `should`, `can` — escolher o padrão do
  ecossistema/linguagem do projeto) mesmo quando o restante do nome está no
  idioma de domínio do projeto. Não é uma exceção à convenção de idioma, só do
  prefixo — mantém consistência com bibliotecas de terceiro já usadas
  (`isNaN`, `isArray`, etc.).

### Padrão de qualidade dos testes end-to-end

`<Descrever o padrão real: infraestrutura de teste (containers efêmeros?
banco real ou fake?), o que é mockado (só serviços externos de terceiro) e o
que nunca deve ser mockado (a própria camada de dados/banco sob teste — mocks
aqui mascaram bugs reais de query, RLS/permissão e serialização). Ver
docs/best-practices/testes.md.>`

## Regras específicas do projeto

`<Se o projeto tiver um documento próprio de regras específicas de domínio
(Ex.: project-rules.md), referenciar aqui via import, mantendo uma única
fonte de verdade em vez de duplicar.>`
