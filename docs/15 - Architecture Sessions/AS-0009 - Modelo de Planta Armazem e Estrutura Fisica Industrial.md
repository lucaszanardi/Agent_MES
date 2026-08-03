# AS-0009 - Modelo de Planta, Armazem e Estrutura Fisica Industrial

## Metadados

| Campo | Valor |
|---|---|
| Status | Concluida |
| Data | 2026-08-03 |
| Origem | Auditoria arquitetural e documental sobre a introducao do conceito de Planta no MES/MOM |
| Decision Log relacionado | DL-0043 |
| Escopo | Planta, Armazem/Almoxarifado, Area de Estoque, estrutura fisica industrial, PlantId e WarehouseId |
| Fora de escopo | Implementacao em backend/frontend, migrations, database update, carga de dados e decisao sobre manter, integrar ou eliminar `CLOCALDEESTOQUE` |

## 1. Contexto

A primeira vertical funcional de Estoque ja usa `PlantId` e `WarehouseId` como escopo operacional em `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque`, eventos, outbox, idempotencia e reserva transacional.

O cadastro legado de estrutura fisica possui `Almoxarifado`, `AreaEstoque`, `TipoLocalizacao` e `LocalizacaoEstoque`. A auditoria nao identificou entidade/tabela de Planta implementada, nem vinculo atual de `CentroTrabalho`, linha, celula, maquina ou roteiro a uma Planta.

A validacao humana encerrou a decisao conceitual: o termo oficial e `Planta`.

## 2. Evidencias de codigo

| Conceito | Evidencia encontrada | Tabela/artefato | Situacao |
|---|---|---|---|
| Planta | Nao identificado `class Planta`, `Plant`, `DbSet<Planta>` ou tabela dedicada | Nao identificado | Decisao aprovada, implementacao pendente |
| `PlantId` | Propriedades em `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque`, eventos, outbox, idempotencia e reserva | `CLOCALDEESTOQUE`, `CUNIDADELOGISTICA`, `CMOVIMENTACAODEESTOQUE`, `COUTBOXMESSAGE`, `CIDEMPOTENCYREQUEST`, `CUNIDADELOGISTICAMOVEMENTRESERVATION` | Escopo tecnico ja persistido na nova vertical |
| `WarehouseId` | Propriedades em entidades e servicos da nova vertical | Mesmas tabelas da nova vertical | Identidade operacional equivalente a `AlmoxarifadoId` |
| Almoxarifado | `BACKEND/PRPA/App.Domain/Entities/PRPA/Almoxarifado.cs` | `CALMOXARIFADO` | Armazem/almoxarifado; deve pertencer a uma Planta no modelo aprovado |
| Area de Estoque | `BACKEND/PRPA/App.Domain/Entities/PRPA/AreaEstoque.cs` | `CAREAESTOQUE` | Area vinculada a `Almoxarifado` |
| Localizacao de Estoque legada | `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs` | `CLOCALIZACAOESTOQUE` | Hierarquia fisica legada por armazem e area |
| Local de Estoque novo | `BACKEND/PRPA/App.Domain/Entities/Estoque/LocaisDeEstoque/LocalDeEstoque.cs` | `CLOCALDEESTOQUE` | Fora da decisao desta AS quanto a manter, integrar ou eliminar |
| Centro de Trabalho | `BACKEND/PRPA/App.Domain/Entities/PRPA/CentroTrabalho.cs` | `CENTROSDETRABALHO` | Deve pertencer funcionalmente a Planta em modelagem futura de Producao |

## 3. Decisoes aprovadas

| ID | Decisao |
|---|---|
| D1 | O termo oficial sera Planta. |
| D2 | Planta representa a unidade fisica superior de operacao industrial ou logistica. |
| D3 | Planta podera possuir os tipos Industrial, Logistica e Mista. |
| D4 | Uma Empresa pode possuir uma ou mais Plantas. |
| D5 | Uma Planta pode possuir zero ou mais Almoxarifados/Armazens. |
| D6 | Todo novo Almoxarifado devera pertencer a uma Planta. |
| D7 | Para registros legados, o vinculo `Almoxarifado -> Planta` podera ser temporariamente opcional durante a transicao e o backfill. |
| D8 | Apos a transicao, o vinculo `Almoxarifado -> Planta` devera ser obrigatorio. |
| D9 | `WarehouseId` representa a mesma identidade de `AlmoxarifadoId`. |
| D10 | Nao devera existir cadastro separado de Warehouse. |
| D11 | `PlantId` de uma `LocalizacaoEstoque` devera ser derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`. |
| D12 | `PlantId` nao devera ser repetido ou informado manualmente em cada `LocalizacaoEstoque`. |
| D13 | Unidade Logistica e Movimentacao deverao derivar o escopo de Planta e Armazem por suas referencias operacionais. |
| D14 | Um centro de distribuicao sem producao podera ser representado como Planta do tipo Logistica. |
| D15 | Um estabelecimento com producao e logistica sera Planta do tipo Mista. |
| D16 | Linhas de producao pertencem funcionalmente a Planta. |
| D17 | Linhas podem estar fisicamente localizadas dentro de um Armazem, Galpao ou Area Mista. |
| D18 | Buffers, supermercados de linha e estoques intermediarios pertencem ao escopo da Planta e podem ser representados por enderecos de estoque. |
| D19 | A decisao sobre manter, integrar ou eliminar `CLOCALDEESTOQUE` nao sera tomada na AS-0009. |
| D20 | A discussao `LocalizacaoEstoque` versus `LocalDeEstoque` devera ser tratada em Architecture Session especifica e independente. |

## 4. Modelo conceitual aprovado

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

Cardinalidades aprovadas:

| Relacao | Cardinalidade |
|---|---|
| Empresa -> Planta | 1:N |
| Planta -> Almoxarifado | 1:N |
| Almoxarifado -> Area de Estoque | 1:N |
| Area de Estoque -> LocalizacaoEstoque | 1:N |
| Planta -> Linha de Producao | 1:N |
| Linha -> Recursos | 1:N, conforme decisao futura do modulo de Producao |

## 5. PlantId e WarehouseId

| Campo | Definicao aprovada |
|---|---|
| `PlantId` | Identidade da Planta, derivada pela associacao do Almoxarifado. |
| `WarehouseId` | Identidade operacional equivalente a `AlmoxarifadoId`; nao representa cadastro independente. |

Para `LocalizacaoEstoque`, `PlantId` deve ser derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`. Ele nao deve ser repetido ou informado manualmente em cada localizacao.

## 6. Cenarios suportados pela decisao

| Cenario | Resultado |
|---|---|
| Empresa com uma planta e varios armazens | Suportado. |
| Empresa com varias plantas | Suportado por Empresa 1:N Planta. |
| Planta com varios armazens | Suportado por Planta 1:N Almoxarifado. |
| Centro de distribuicao sem producao | Representado como Planta Logistica. |
| Estabelecimento com producao e logistica | Representado como Planta Mista. |
| Linhas dentro de armazem, galpao ou area mista | Permitido conceitualmente; modelagem tecnica futura. |
| Buffers e supermercados de linha | Pertencem ao escopo da Planta e podem usar enderecos de estoque. |
| Decisao sobre `CLOCALDEESTOQUE` | Nao decidida nesta AS. |

## 7. Pendencias tecnicas de implementacao

- Criar contrato de persistencia para Planta e tipo de Planta.
- Definir migration para entidade/tabela de Planta somente em tarefa futura aprovada.
- Definir vinculo `Almoxarifado -> Planta` e politica de obrigatoriedade transicional.
- Definir backfill de Almoxarifados legados para Planta.
- Revisar DTOs, services, controllers, permissoes e frontend somente em tarefa futura aprovada.
- Revisar como Producao, Qualidade, OEE e Rastreabilidade consumirao Planta.
- Criar AS especifica para discutir `LocalizacaoEstoque` versus `LocalDeEstoque` e o futuro de `CLOCALDEESTOQUE`.

## 8. Estado documental e tecnico

| Item | Estado |
|---|---|
| Decisao arquitetural | Aprovada e registrada na DL-0043. |
| Entidade Planta | Ainda nao implementada. |
| Tabela Planta | Ainda nao criada. |
| Vinculo `Almoxarifado -> Planta` | Ainda nao implementado. |
| Migration | Nao criada nesta AS. |
| Banco de dados | Nao alterado nesta AS. |
| Backend | Nao alterado nesta AS. |
| Frontend | Nao alterado nesta AS. |
| `CLOCALDEESTOQUE` | Sem decisao de manter, integrar ou eliminar nesta AS. |

## 9. Conclusao

A AS-0009 fica concluida. Planta passa a ser o conceito oficial de unidade fisica superior de operacao industrial ou logistica no MES/MOM, com tipos Industrial, Logistica e Mista.

`Almoxarifado` permanece como Armazem/Warehouse e `WarehouseId` deve representar `AlmoxarifadoId`. `PlantId` deve ser derivado por meio do Almoxarifado, sem repeticao manual em cada `LocalizacaoEstoque`.

Nenhuma implementacao foi realizada nesta sessao documental.