# Visão geral da arquitetura

> Como os módulos do sistema conversam entre si — fluxo de dados, integração
> e camadas. Para o "porquê" de decisões específicas, ver `docs/decisions/`.

## Contexto

`<Uma ou duas frases sobre o sistema: o que ele é, principais dependências
externas (banco, auth, filas), e o que este documento cobre no estado atual
(ex.: "cobre o estado atual do backend; módulos futuros serão adicionados
aqui conforme forem implementados").>`

## Módulos existentes

```
<raiz-do-código>/
├── <módulo-a>/     # <responsabilidade em uma linha>
├── <módulo-b>/     # <responsabilidade em uma linha>
└── <módulo-c>/     # <responsabilidade em uma linha>
```

- **`<módulo-a>`** (`<caminho>/`) — `<o que faz, principal serviço/classe
  exposta, com quem se integra>`.
- **`<módulo-b>`** (`<caminho>/`) — `<idem>`.

## Fluxo de uma requisição/operação principal (estado atual)

```
<Cliente/gatilho>
  │
  ▼
<Camada 1 — ex.: autenticação>          — <o que resolve>
  ▼
<Camada 2 — ex.: autorização>           — <o que resolve>
  ▼
<Camada 3 — ex.: controller/handler>
  ▼
<Camada 4 — ex.: service/regra de negócio>
  ▼
<Camada 5 — ex.: acesso a dados>  → <armazenamento>
```

`<Descrever qualquer ordem invariante entre camadas (ex.: "guard A sempre
roda antes do guard B") e qualquer rota/caso que pula alguma camada (ex.: rota
pública sem autenticação).>`

## Limitações conhecidas / próximos passos

- `<O que ainda não foi implementado mas é assumido por alguma decisão já
  tomada — para não ser redescoberto do zero depois.>`

## Índice de documentos de módulo

- [`<modulo>.md`](./<modulo>.md) — `<resumo de uma linha>`.
