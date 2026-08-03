# Modelo Conceitual

## Planta, Armazem e Estrutura Fisica Industrial

Referencias:

- `../15 - Architecture Sessions/AS-0009 - Modelo de Planta Armazem e Estrutura Fisica Industrial.md`.
- `../12 - Decision Log/DL-0043 - Planta como Escopo Superior de Producao Armazens e Estoque.md`.

Planta e o conceito oficial para a unidade fisica superior de operacao industrial ou logistica no MES/MOM. Uma Planta pertence a uma Empresa e agrupa Armazens/Almoxarifados, areas produtivas, linhas, recursos e areas mistas.

Tipos aprovados de Planta:

| Tipo | Definicao |
|---|---|
| Industrial | Planta que possui processos produtivos. |
| Logistica | Planta dedicada principalmente a armazenagem, distribuicao ou operacao logistica, sem obrigacao de possuir linhas de producao. |
| Mista | Planta que combina producao e logistica. |

Modelo conceitual aprovado:

```text
Empresa
`-- Planta
    |-- Almoxarifado / Armazem
    |   `-- Area de Estoque
    |       `-- Localizacao de Estoque
    |-- Area Produtiva
    |   `-- Linha de Producao
    |       `-- Maquina / Recurso
    `-- Area Mista
        |-- Linha de Producao
        |-- Buffer
        |-- Supermercado de Linha
        `-- Localizacoes de Estoque
```

Cardinalidades:

| Relacao | Cardinalidade |
|---|---|
| Empresa -> Planta | 1:N |
| Planta -> Almoxarifado | 1:N |
| Almoxarifado -> Area de Estoque | 1:N |
| Area de Estoque -> LocalizacaoEstoque | 1:N |
| Planta -> Linha de Producao | 1:N |
| Linha -> Recursos | 1:N, conforme decisao futura do modulo de Producao |

`WarehouseId` representa a mesma identidade de `AlmoxarifadoId` e nao cria cadastro separado de Warehouse. `PlantId` deve ser derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`, sem repeticao manual em cada localizacao.

A entidade Planta, a tabela de Planta, o vinculo `Almoxarifado -> Planta`, a migration e o backfill ainda nao estao implementados.

## Identidade Unica de Localizacao de Estoque

Referencias:

- `../15 - Architecture Sessions/AS-0010 - Unificacao da Identidade Fisica e Operacional dos Locais de Estoque.md`.
- `../12 - Decision Log/DL-0044 - LocalizacaoEstoque como Identidade Unica Fisica e Operacional.md`.
- `../05 - Estoque/Contrato de Transicao para Identidade Unica de Localizacao de Estoque.md`.

Modelo conceitual operacional aprovado:

```text
Planta
`-- Almoxarifado
    `-- Area de Estoque
        `-- LocalizacaoEstoque
            |-- UnidadeLogistica
            `-- MovimentacaoDeEstoque
```

`LocalizacaoEstoque` passa a ser a identidade unica fisica e operacional dos enderecos de estoque. O usuario cadastra o endereco uma unica vez, sem publicacao para `LocalDeEstoque` e sem sincronizacao com `CLOCALDEESTOQUE`.

Somente localizacao com classificacao efetiva `ARMAZENA` e elegivel para receber Unidade Logistica e participar de origem/destino de movimentacao. `ESTRUTURAL` e `BLOQUEADO` nao sao elegiveis.

`PlantId` e derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`. `WarehouseId` equivale a `AlmoxarifadoId`.
