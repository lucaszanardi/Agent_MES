# DL-0043 - Planta como Escopo Superior de Producao, Armazens e Estoque

## Status

Aprovado.

## Origem

- AS-0009 - Modelo de Planta, Armazem e Estrutura Fisica Industrial.
- Relacionada a DL-0038, DL-0041 e DL-0042.
- Deve orientar decisoes futuras do modulo de Producao.

## Decisao

Fica aprovado o conceito de Planta como unidade fisica superior de operacao industrial ou logistica no MES/MOM.

A Planta pertence a uma Empresa e agrupa Armazens/Almoxarifados, areas produtivas, linhas, recursos e areas mistas. A Planta pode ser classificada em tres tipos:

| Tipo | Definicao |
|---|---|
| Industrial | Planta que possui processos produtivos. |
| Logistica | Planta dedicada principalmente a armazenagem, distribuicao ou operacao logistica, sem obrigacao de possuir linhas de producao. |
| Mista | Planta que combina producao e logistica. |

## Relacionamentos aprovados

| Relacao | Decisao |
|---|---|
| Empresa -> Planta | Uma Empresa pode possuir uma ou mais Plantas. |
| Planta -> Almoxarifado | Uma Planta pode possuir zero ou mais Almoxarifados/Armazens. |
| Novo Almoxarifado -> Planta | Todo novo Almoxarifado devera pertencer a uma Planta. |
| Almoxarifado legado -> Planta | O vinculo podera ser temporariamente opcional durante transicao e backfill. |
| Pos-transicao | O vinculo `Almoxarifado -> Planta` devera ser obrigatorio. |
| Planta -> Linha de Producao | Linhas pertencem funcionalmente a Planta. |
| Linha -> local fisico | Linhas podem estar fisicamente dentro de Armazem, Galpao ou Area Mista. |

## Identificadores

| Identificador | Decisao |
|---|---|
| `WarehouseId` | Representa a mesma identidade de `AlmoxarifadoId`. |
| Cadastro de Warehouse | Nao deve existir cadastro separado de Warehouse. |
| `PlantId` | Identidade da Planta derivada pela associacao do Almoxarifado. |
| `PlantId` em `LocalizacaoEstoque` | Deve ser derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`; nao deve ser repetido ou informado manualmente em cada localizacao. |
| Unidade Logistica e Movimentacao | Devem derivar escopo de Planta e Armazem por suas referencias operacionais. |

## Areas mistas e estoques de linha

Buffers, supermercados de linha e estoques intermediarios pertencem ao escopo da Planta e podem ser representados por enderecos de estoque.

Um centro de distribuicao sem producao pode ser representado como Planta do tipo Logistica. Um estabelecimento com producao e logistica deve ser representado como Planta do tipo Mista.

## Limites da decisao

Esta DL nao decide manter, integrar ou eliminar `CLOCALDEESTOQUE`.

A discussao `LocalizacaoEstoque` versus `LocalDeEstoque` deve ser tratada em Architecture Session especifica e independente.

Esta DL nao autoriza implementacao imediata, migration, database update, alteracao de backend, alteracao de frontend ou carga de dados.

## Impactos futuros

- Criar contrato de persistencia para Planta e tipo de Planta.
- Definir entidade/tabela de Planta em tarefa futura aprovada.
- Definir vinculo fisico/logico `Almoxarifado -> Planta`.
- Planejar backfill de Almoxarifados legados.
- Ajustar PlantId da nova vertical para derivacao pelo Almoxarifado quando houver modelo implementado.
- Rever escopos de Producao, Estoque, Qualidade, OEE e Rastreabilidade.
- Definir como linhas, maquinas, recursos e areas mistas serao persistidos no modulo de Producao.

## Consequencias

- Planta passa a ser a fronteira fisica superior para operacao industrial/logistica.
- `Almoxarifado` nao deve ser confundido com Planta.
- `WarehouseId` nao cria novo cadastro; ele equivale ao identificador do Almoxarifado.
- `PlantId` deve ser derivado, evitando duplicacao manual em `LocalizacaoEstoque`.
- O legado pode permanecer transicionalmente sem Planta ate backfill aprovado.

## Implementacao

Pendente. Nenhuma implementacao foi realizada por esta Decision Log.