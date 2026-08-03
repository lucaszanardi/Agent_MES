# Modelo Logico

## Planta, Armazem e Estrutura Fisica Industrial

Referencias:

- `../15 - Architecture Sessions/AS-0009 - Modelo de Planta Armazem e Estrutura Fisica Industrial.md`.
- `../12 - Decision Log/DL-0043 - Planta como Escopo Superior de Producao Armazens e Estoque.md`.

Estado atual de implementacao:

| Elemento | Tabela/artefato atual | Observacao |
|---|---|---|
| Planta | Nao implementada | Nao ha tabela, entidade, DbSet ou controller localizado. |
| Tipo de Planta | Nao implementado | Tipos aprovados: Industrial, Logistica e Mista. |
| `PlantId` | `CLOCALDEESTOQUE`, `CUNIDADELOGISTICA`, `CMOVIMENTACAODEESTOQUE`, outbox, idempotencia e reserva | Campo tecnico de escopo da nova vertical; devera ser alinhado ao cadastro de Planta em implementacao futura. |
| Armazem/Almoxarifado | `CALMOXARIFADO` | Cadastro legado que representa Warehouse. |
| `WarehouseId` | Campos da nova vertical de Estoque | Equivale a `AlmoxarifadoId`; nao deve existir cadastro separado de Warehouse. |
| Area de Estoque | `CAREAESTOQUE` | Possui FK para `CALMOXARIFADO`. |
| Localizacao de Estoque | `CLOCALIZACAOESTOQUE` | Hierarquia fisica legada por armazem e area. |
| Centro de Trabalho | `CENTROSDETRABALHO` | Deve pertencer funcionalmente a Planta em modelagem futura de Producao. |

Diretrizes logicas aprovadas:

- todo novo Almoxarifado devera pertencer a uma Planta;
- registros legados poderao manter o vinculo `Almoxarifado -> Planta` temporariamente opcional durante transicao e backfill;
- apos a transicao, o vinculo devera ser obrigatorio;
- `PlantId` de `LocalizacaoEstoque` deve ser derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`;
- `PlantId` nao deve ser repetido ou informado manualmente em cada `LocalizacaoEstoque`;
- Unidade Logistica e Movimentacao devem derivar o escopo de Planta e Armazem por suas referencias operacionais.

A decisao sobre manter, integrar ou eliminar `CLOCALDEESTOQUE` nao foi tomada neste modelo e devera ser tratada em AS especifica.

## Identidade Unica de Localizacao de Estoque

Referencias: AS-0010, DL-0044 e contrato de transicao.

Estado alvo logico:

| Elemento | Decisao |
|---|---|
| `CLOCALIZACAOESTOQUE` | Fonte de verdade para enderecos fisicos e operacionais. |
| `CLOCALDEESTOQUE` | Descontinuada como identidade operacional independente; remocao fisica futura e controlada. |
| `CUNIDADELOGISTICA` | Devera referenciar `LocalizacaoEstoque` por nome logico recomendado `LocalizacaoEstoqueId`. |
| `CMOVIMENTACAODEESTOQUE` | Devera usar `LocalizacaoOrigemId` e `LocalizacaoDestinoId`, ambos referenciando `LocalizacaoEstoque`. |
| Concorrencia | `LocalizacaoEstoque` precisara de mecanismo compativel; forma fisica pendente. |
| Status | `BLOQUEADO` derivado do bloqueio; `ESTRUTURAL` e `ARMAZENA` sao classificacoes funcionais. |

Nenhuma coluna, FK, migration ou remocao de tabela foi criada nesta documentacao.
