# Testes — princípios agnósticos de stack

## Pirâmide pragmática

- **Unitário**: regra de negócio isolada, rápido, junto do código testado.
  Mock de dependência externa (rede, tempo, aleatoriedade) é aceitável e
  esperado aqui.
- **Integração/e2e**: exercita o sistema real de ponta a ponta — request de
  verdade contra a interface pública, dados de fato persistidos/lidos na
  camada de dados real.

## Nunca mockar a camada sob teste

O erro mais caro observado neste tipo de projeto: reescrever a fluent
API/ORM à mão como um fake "PrismaService"-like em teste de
integração/e2e. Isso mascara exatamente as classes de bug que o teste de
integração existe para pegar:

- Bug de query real (join errado, filtro esquecido, N+1).
- Falha de policy/permissão de banco (RLS, ACL) — um fake nunca aplica RLS
  de verdade.
- Bug de serialização entre o driver do banco e a camada de aplicação (tipos
  de data/hora, precisão numérica, campos opcionais).

**Regra prática:** teste de integração/e2e roda contra uma instância real do
armazenamento (container efêmero — Testcontainers ou equivalente —, banco de
teste dedicado, etc.), com migrations reais aplicadas. Só serviço externo de
terceiro (gateway de pagamento, provedor de e-mail, LLM de terceiro) é
mockado — porque esse, sim, está fora do que está sob teste.

## Teste de isolamento multi-tenant é obrigatório, não opcional

Se o sistema tem qualquer noção de dado que não pode vazar entre
clientes/tenants, toda feature que toque nesse dado precisa de um teste que
prove isolamento tentando ativamente acessar dado de outro tenant (não só
"a query filtra corretamente no caminho feliz").

## CI espelha localmente

Nenhum passo de qualidade que roda em CI deveria ser diferente do que roda
localmente — mesma versão de ferramenta, mesmos comandos (ver
`ci-cd.md`). Se o CI "pega algo que localmente não pega", isso é sinal de
divergência de ambiente a ser corrigida, não aceita como normal.

## Evidência antes de afirmação

Uma alegação sobre "como o framework/lib de terceiro se comporta" só é fato
depois de testada contra o comportamento real — nunca "faz sentido que
funcione assim". Ver a seção "Como foi verificado" do template de ADR
(`docs/decisions/TEMPLATE.md`): esse mesmo padrão de rigor vale para qualquer
comentário/decisão de código que descreva comportamento de terceiro.
