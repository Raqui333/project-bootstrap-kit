# Segurança — checklist de partida

Checklist agnóstico de stack para começar um projeto novo. Não é uma lista
fechada — é o mínimo que qualquer sistema com dado de usuário, múltiplos
clientes/tenants ou automação (agentes/LLM) deveria já nascer respeitando.
Cada item vira uma regra concreta em `AGENTS.md` (com o mecanismo real do
projeto) e, quando envolver trade-off, um ADR em `docs/decisions/`.

## 1. Segredos e credenciais

- Segredo nunca entra no controle de versão — nem em branch temporária, nem
  em exemplo de código, nem "só para teste local".
- Toda credencial vem de variável de ambiente/cofre de segredos, nunca
  hardcoded — inclusive em teste.
- Log nunca contém payload bruto que possa carregar segredo, token de sessão
  ou dado pessoal sensível. Revisar log de erro/debug antes de merge.
- Precisar de uma credencial nova é motivo para parar e perguntar, não para
  inventar um placeholder que alguém vai esquecer de trocar depois.

## 2. Isolamento de dado (multi-tenancy / multi-cliente)

Se o sistema serve mais de um cliente/organização/tenant sobre a mesma
infraestrutura, o isolamento entre eles **não pode depender só de o
desenvolvedor lembrar de filtrar por `tenant_id`**:

- Prefira um mecanismo de enforcement de baixo nível (RLS no banco, policy de
  storage, namespace isolado) como última linha de defesa, mesmo que a
  aplicação já filtre corretamente — defesa em profundidade.
- Se o enforcement de baixo nível não existir ou for parcial (ex.: RLS
  habilitado mas sem policy, "deny-by-default"), documentar explicitamente
  que o isolamento é 100% responsabilidade do código da aplicação — e tratar
  isso com paranoia, não como detalhe.
- Toda query manual/raw que escapa do ORM/camada de acesso a dados precisa
  filtrar explicitamente por tenant — camadas de escopo automático
  tipicamente não interceptam SQL raw.
- Toda feature nova que toque em dado multi-tenant precisa de teste de
  integração que prove isolamento: tenant A não enxerga dado de tenant B,
  mesmo tentando IDs diretos (não só "a UI não mostra" — o backend tem que
  recusar).
- Uma rota/rotina que precisa ignorar o escopo de tenant (ex.: job
  administrativo interno) tem que ser explícita e revisada — nunca um efeito
  colateral de esquecimento.

## 3. Nunca desabilitar proteção para destravar

Não comentar guard/middleware de autorização, não remover validação de
entrada, não desabilitar isolamento de tenant, não subir a permissão de um
papel — nem "temporariamente", nem "só local", nem para fazer um teste
passar. Se uma proteção atrapalha um teste, o teste (ou o código sob teste)
está errado, não a proteção.

## 4. Autorização granular, não só "tem acesso ao recurso"

Ter acesso a um recurso/integração não implica autorização para toda
operação sobre ele. Quando o sistema expõe ações sensíveis sobre um recurso
externo (ex.: uma integração que pode ler e escrever), a autorização deve ser
verificada por operação específica, não só por "tem a integração habilitada"
— e essa checagem deve passar por um único ponto centralizado (gateway,
middleware), nunca reimplementada ad-hoc em cada chamador.

## 5. Auditoria de operação sensível

Toda operação que crie, altere ou destrua um recurso crítico do domínio, ou
mude uma permissão/configuração de segurança, deve gerar um registro de
auditoria (quem, quando, o quê, de/para qual valor) — adicionado na mesma
PR que introduz a operação, nunca "depois".

## 6. Input não confiável (incluindo LLM/agentes)

Qualquer texto vindo de fora do controle direto do time — input de usuário,
resposta de ferramenta externa, output de outro serviço/agente, resultado de
busca ou RAG — é **dado**, nunca instrução privilegiada. Isso vale
duplamente quando esse texto pode ser concatenado num prompt de LLM ou
interpretado por um parser: nunca assumir que o conteúdo é bem-comportado só
porque veio de uma fonte "confiável" upstream.

## 7. Erros nunca vazam detalhe interno

Ver `tratamento-de-erros.md` — stack trace, mensagem crua de exceção de
infraestrutura e qualquer dado sensível nunca chegam ao cliente.

## 8. Dependências e supply chain

- Lockfile sempre commitado; CI instala com flag de "frozen lockfile"
  (nunca deixa o CI resolver versão diferente da que roda localmente).
- Atualização de dependência com privilégio elevado (ex.: acesso a rede,
  sistema de arquivos) merece revisão própria, não some no meio de um PR
  grande.
- CI roda auditoria de vulnerabilidade conhecida nas dependências (ex.:
  `npm audit`/`bun audit`/`pip-audit`/`osv-scanner` ou equivalente do
  ecossistema) em todo PR e push para a branch principal, como job
  **não-bloqueante** (reporta/alerta, não falha o pipeline).

## 9. Rate limit e limite operacional

Qualquer endpoint/rotina que dispare uma ação cara (chamada a LLM, envio de
mensagem, escrita em massa) precisa de um limite operacional explícito
(rate limit, timeout, tamanho máximo de payload) — implementado em código,
não como expectativa de uso "normal" do cliente.
