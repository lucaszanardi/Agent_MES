# DL-0044 - LocalizacaoEstoque como Identidade Unica Fisica e Operacional

## Status

Aprovado.

## Origem

- AS-0010 - Unificacao da Identidade Fisica e Operacional dos Locais de Estoque.
- Relaciona-se com AS-0004, AS-0005, AS-0007, AS-0008 e AS-0009.
- Relaciona-se com DL-0038, DL-0041, DL-0042 e DL-0043.

## Decisao

`LocalizacaoEstoque` sera a identidade unica fisica e operacional dos enderecos de estoque.

`CLOCALIZACAOESTOQUE` sera a fonte de verdade para estrutura hierarquica, identificacao do endereco, elegibilidade para armazenagem, posicionamento de Unidade Logistica, origem e destino de movimentacoes, bloqueio, permissoes operacionais, capacidade e rastreabilidade do endereco.

`LocalDeEstoque` e `CLOCALDEESTOQUE` deixam de ser utilizados como identidade operacional independente. `CLOCALDEESTOQUE` sera descontinuada, com remocao fisica posterior e controlada.

## Regras aprovadas

| Tema | Decisao |
|---|---|
| Fonte de verdade | `CLOCALIZACAOESTOQUE`. |
| Elegibilidade | Apenas classificacao efetiva `ARMAZENA` recebe Unidade Logistica. |
| Estrutural | `ESTRUTURAL` nao recebe estoque e nao pode ser origem/destino. |
| Bloqueado | `BLOQUEADO` nao recebe estoque e nao pode ser origem/destino. |
| Unidade Logistica | Devera referenciar `LocalizacaoEstoque`. |
| MovimentacaoDeEstoque | Devera usar `LocalizacaoEstoque` em origem e destino. |
| Planta | `PlantId` sera derivado pelo Almoxarifado. |
| Warehouse | `WarehouseId` equivale a `AlmoxarifadoId`. |
| Publicacao | Nao havera publicacao de `LocalizacaoEstoque` para `LocalDeEstoque`. |
| Sincronizacao | Nao havera sincronizacao com `CLOCALDEESTOQUE`. |
| Garantias | Idempotencia, concorrencia, reserva transacional e outbox serao preservadas. |
| Dados operacionais | Nao ha dados operacionais para migrar. |

## Complementos e substituicoes

| Decisao anterior | Efeito desta DL |
|---|---|
| DL-0038 | Parcialmente substituida quanto ao uso futuro de `CLOCALDEESTOQUE` como tabela de identidade operacional. Mantem-se a cautela de transicao controlada. |
| DL-0041 | Complementada: a transicao deixa de seguir para duas identidades e passa a convergir para `CLOCALIZACAOESTOQUE` como identidade unica. |
| DL-0042 | Complementada: finalidade configurada e classificacao efetiva tornam-se criterios operacionais diretos. |
| DL-0043 | Mantida: Planta e derivada pelo Almoxarifado e `WarehouseId` equivale a `AlmoxarifadoId`. |

## Alteracoes hierarquicas

Nao sera permitido transformar uma localizacao operacionalmente armazenadora em estrutural enquanto houver dependencias operacionais, incluindo Unidade Logistica posicionada, saldo direto, movimentacao ativa, reserva, inventario em andamento ou bloqueio operacional incompativel.

## Limites

Esta DL nao executa implementacao, nao cria migration, nao altera snapshot, nao executa database update, nao altera banco, nao altera backend e nao altera frontend.

A remocao fisica de `CLOCALDEESTOQUE` so podera ocorrer em etapa futura apos refatoracao do dominio, handlers, FKs, testes, revisao SQL e validacao humana.

## Consequencias

- O usuario cadastra o endereco uma unica vez.
- Nao existe processo de publicacao duplicado.
- O backend passa a ser autoridade de elegibilidade operacional em implementacao futura.
- A arquitetura evita divergencia entre cadastro fisico e operacao.
- A nova vertical devera ser refatorada para referenciar `CLOCALIZACAOESTOQUE`.

## Implementacao

Pendente. Nenhuma implementacao foi realizada por esta Decision Log.