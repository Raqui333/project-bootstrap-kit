# Tratamento de erro — visão geral do padrão

Este documento descreve, de forma genérica (não amarrada a projeto específico),
um padrão de tratamento de erros para APIs HTTP.

## Princípios

1. **Um único ponto de saída para erros.** Toda exceção lançada em qualquer camada
   (controller, service, guard, pipe) passa por um filtro/middleware global antes de
   virar resposta HTTP. Nenhuma camada de negócio formata resposta de erro na mão.
2. **Contrato de erro estável e previsível.** O cliente da API sempre recebe o mesmo
   formato de corpo de erro, independente de qual exceção causou o problema.
3. **Nunca vazar detalhe interno.** Stack trace, mensagem crua de exceção de
   infraestrutura (driver de banco, biblioteca HTTP interna) ou qualquer dado sensível
   nunca chega ao cliente — só é logado internamente.
4. **Log correlacionável.** Todo erro logado no servidor carrega um identificador
   (trace id) que também é devolvido ao cliente, permitindo cruzar "o que o cliente viu"
   com "o que aconteceu no servidor" sem expor detalhe nenhum.
5. **Camadas de negócio lançam exceções de domínio, não formatam resposta.** Um service
   lança `NotFound`, `Conflict`, `ServiceUnavailable` etc. — quem decide o JSON
   final é sempre o filtro global, nunca o service.

## Componentes do padrão

### 1. Exceções de aplicação

Um conjunto pequeno e reutilizável de tipos de exceção, cada um mapeado a um status
HTTP:

| Situação de negócio                          | Exceção típica             | Status HTTP |
|----------------------------------------------|----------------------------|-------------|
| Corpo/parâmetros de entrada inválidos        | erro de validação          | 400/422     |
| Usuário não autenticado                      | não autorizado             | 401         |
| Usuário autenticado sem permissão            | proibido                   | 403         |
| Recurso não existe                           | não encontrado             | 404         |
| Conflito de estado/concorrência              | conflito                   | 409         |
| Payload maior que o limite permitido         | payload muito grande       | 413         |
| Excesso de requisições (rate limit)          | limite excedido            | 429         |
| Falha inesperada / dependência fora do ar    | erro interno / indisponível| 500/503     |

Não é necessário criar uma classe de exceção nova para cada caso de negócio — basta
reaproveitar essas poucas exceções genéricas com uma mensagem específica. Regras de
negócio repetidas (ex.: "busca por id e lança 'não encontrado' se nulo") podem virar um
helper compartilhado, em vez de reescrever o mesmo `if` em cada service.

### 2. Filtro/middleware global de exceções

Um único componente, registrado uma vez na inicialização da aplicação (não em cada
rota), responsável por:

- Capturar **qualquer** exceção não tratada (inclusive erros crus que não são exceções
  de aplicação — erro de biblioteca HTTP, erro de parser de body etc.).
- Determinar o status HTTP e o "código" lógico do erro.
- Montar o corpo de resposta no contrato único (ver próxima seção).
- Logar a exceção (com stack trace apenas no log interno, nunca na resposta).
- Gerar/reaproveitar um trace id para correlação.

Qualquer exceção que não se encaixe em nenhum tipo conhecido cai num caso padrão:
status 500, mensagem genérica fixa ("erro interno"), sem detalhe nenhum do erro
original.

### 3. Contrato único de corpo de erro

Formato estável, igual para todo erro da API:

```json
{
  "error": {
    "code": "NOT_FOUND",
    "message": "Mensagem amigável para o usuário final.",
    "fields": [
      { "field": "nomeDoCampo", "reason": "motivo da invalidação" }
    ],
    "trace_id": "identificador-correlacionavel-com-o-log"
  }
}
```

- `code`: valor de um enum fechado (`VALIDATION_ERROR`, `UNAUTHORIZED`, `FORBIDDEN`,
  `NOT_FOUND`, `CONFLICT`, `RATE_LIMITED`, `PAYLOAD_TOO_LARGE`, `INTERNAL`, ...) — não é
  a mensagem, é um código estável que o cliente pode usar em lógica (ex.: exibir tela
  específica para `NOT_FOUND`).
- `message`: texto amigável, nunca a mensagem crua de uma exceção de infraestrutura.
- `fields`: só aparece em erro de validação, listando campo a campo o que está errado.
- `trace_id`: mesmo identificador escrito no log do servidor para aquele erro — é o que
  permite localizar o erro específico em produção sem expor nada sensível ao cliente.

### 4. Log de erros correlacionado

Cada erro capturado pelo filtro global gera uma linha de log contendo: rota, método,
código do erro, mensagem e o `trace_id`. Dados sensíveis (query string, tokens, CPF/CNPJ
etc.) não devem ser logados, caso precise devem ser mascarados. O stack trace vai só para
o log interno — nunca para uma resposta ao cliente nem para um sistema de observabilidade
externo que não seja confiável/interno.

### 5. Validação de entrada tratada como caso especial

Erros de validação de payload/parâmetros (biblioteca de validação de DTO/schema)
normalmente chegam ao filtro global num formato diferente (ex.: array de mensagens).
O filtro deve normalizar esse formato para o mesmo contrato de erro único, preenchendo
`fields` com a lista de campo + motivo, em vez de repassar a mensagem crua da biblioteca
de validação.

### 6. Erros de infraestrutura (banco de dados, filas, serviços externos)

Erros vindos de infraestrutura (ex.: violação de unicidade no banco, fila fora do ar)
não devem vazar para o cliente como estão. Um erro de banco esperado (ex.: violação de
constraint única) deve ser detectado no ponto onde ocorre e traduzido para uma exceção
de aplicação (`Conflict`, por exemplo) — só depois disso ele desce para o filtro global,
que já sabe como formatá-lo. Qualquer erro de infraestrutura não mapeado explicitamente
cai no caso padrão (500 genérico), nunca expondo o erro original.

## Resumo do fluxo

```
Controller/Service/Guard/Pipe
        │
        │  lança exceção de aplicação (ou erro cru de infra)
        ▼
Filtro global de exceções
        │
        ├─ loga (rota, código, mensagem, trace_id, stack só internamente)
        ├─ mapeia exceção → status HTTP + code
        ├─ normaliza mensagem (nunca expõe detalhe interno)
        └─ monta { error: { code, message, fields?, trace_id } }
        ▼
Resposta HTTP ao cliente
```

Esse padrão é aplicável a qualquer stack (Node/NestJS, Express puro, Java/Spring,
Python/FastAPI etc.) — muda a sintaxe de "filtro global"/"middleware"/"exception
handler", mas os princípios (contrato único, nunca vazar detalhe interno, trace id
correlacionável, exceções de domínio genéricas e reutilizáveis) são os mesmos.
