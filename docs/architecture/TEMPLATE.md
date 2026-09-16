# TEMPLATE de doc de módulo

Copie este arquivo para `docs/architecture/<nome-do-modulo>.md` ao concluir a
implementação de um módulo. Preencha todas as seções; remova este bloco de
instruções.

---

# Módulo de `<nome-do-modulo>` (`<caminho-no-código>/`)

> Uma frase curta resumindo a responsabilidade do módulo (o que ele resolve,
> em que fase/épico/PRD se encaixa, se aplicável).

## Visão geral

Parágrafo(s) explicando o que o módulo faz, que domínio(s) cobre e o que
**não** faz (limites de responsabilidade, se houver sobreposição aparente com
outro módulo).

## Arquivos

```
<caminho>/
├── <arquivo-de-composição>       # o que agrega/expõe
├── <arquivo-de-interface>        # rotas/comandos/handlers expostos
├── <arquivo-de-regra-de-negócio> # regra de negócio
└── <subpasta-de-contratos>/
    ├── <contrato-de-entrada>
    └── <contrato-de-saída>
```

| Arquivo | Responsabilidade |
|---|---|
| `<arquivo>` | `<o que faz>` |
| `<arquivo>` | `<o que faz>` |

## Endpoints / interfaces expostas

`<Adaptar a tabela ao tipo de interface do módulo — HTTP, CLI, evento
consumido/publicado, RPC etc.>`

| Método/Tipo | Rota/Comando/Evento | Parâmetros | Observação |
|---|---|---|---|
| `GET` | `/recurso` | `?filtro=` | autorização, escopo, paginação etc. |
| `POST` | `/recurso` | body: `<contrato-de-entrada>` | |

Lembrar de manter o contrato de resposta (DTO/schema usado para gerar
documentação de API) atualizado sempre que o formato de resposta mudar (ver
seção "Contratos de resposta sempre atualizados" do `AGENTS.md`).

## Fluxos principais

Descrever os fluxos não óbvios (validações em cadeia, máquina de estados,
processamento assíncrono). Usar diagrama de sequência em texto quando ajudar:

```
Cliente → Interface → Regra de negócio → Acesso a dados → Armazenamento
                     └→ valida <invariante> antes de gravar
```

## Integrações com outros módulos

- **`<módulo-dependência>`** — o que é consumido de lá e por quê.
- **Módulos que dependem deste** — se relevante.

## Decisões de design

- Trade-offs relevantes, alternativas descartadas, gotchas não óbvios.
- Se alguma decisão aqui for grande o suficiente para merecer ADR e ainda não
  tiver um, sinalizar e criar o ADR separadamente (não criar o ADR dentro
  deste documento).
