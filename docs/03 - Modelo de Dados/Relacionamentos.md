# Relacionamentos

Fonte oficial usada nesta leitura: BACKEND. Este documento descreve somente relacionamentos encontrados em entidades, configuracoes do Entity Framework ou contexto.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Regras gerais do Entity Framework

| Relacionamento/regra | Evidencia | Classificacao |
|---|---|---|
| O contexto aplica configuracoes Fluent API por entidade via `ApplyConfiguration`. | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` - `OnModelCreating` | Confirmado |
| O comportamento geral de delecao de FKs e definido como `DeleteBehavior.Restrict`. | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` - loop em `modelBuilder.Model.GetEntityTypes()` | Confirmado |
| `RoleMenu` usa chave composta por `MenuId` e `RoleId`. | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` - `HasKey(pp => new { pp.MenuId, pp.RoleId })` | Confirmado |
| `RoteiroProducao` possui indice unico por produto, codigo e versao. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/RoteiroProducaoConfig.cs`; `ProjetoContext.cs` | Confirmado |

## Produto e cadastros tecnicos

| Origem | Destino | Evidencia | Classificacao |
|---|---|---|---|
| `Produto` | `ProdutoTipo`, `ProdutoFamilia`, `ProdutoGrupo`, `ProdutoSubgrupo`, `UnidadeMedida`, `ProdutoStatus` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs` | Confirmado |
| `Produto` | `ProdutoVersao`, `ProdutoEspecificacao`, `ProdutoCodigoIdentificacao`, `PedidoItem`, `RoteiroProducao` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs` - colecoes de navegacao | Confirmado |
| `ProdutoGrupo` | `ProdutoFamilia`; colecoes de subgrupos e produtos | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoGrupo.cs` | Confirmado |
| `ProdutoSubgrupo` | `ProdutoGrupo`; colecao de produtos | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoSubgrupo.cs` | Confirmado |
| `ProdutoVersao` | `Produto`; colecoes de especificacoes e codigos | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoVersao.cs` | Confirmado |
| `ProdutoEspecificacao` | `Produto`, `ProdutoVersao` opcional e `UnidadeMedida` opcional | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoEspecificacao.cs` | Confirmado |
| `ProdutoCodigoIdentificacao` | `Produto`, `ProdutoVersao` opcional e `TipoCodigoIdentificacaoProduto` | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoCodigoIdentificacao.cs` | Confirmado |
| `TipoEspecificacaoProduto` | `AreaResponsavelEspecificacao` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/TipoEspecificacaoProdutoConfig.cs` | Confirmado |
| `UnidadeMedida` | `Produto` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/UnidadeMedidaConfig.cs` | Confirmado |

## Engenharia e producao

| Origem | Destino | Evidencia | Classificacao |
|---|---|---|---|
| `EstruturaProduto` | `Produto` e `UnidadeMedida` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/EstruturaProdutoConfig.cs` | Confirmado |
| `ItensEstruturaProduto` | estrutura/engenharia, produto componente e unidade de medida | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/ItensEstruturaProdutoConfig.cs` | Confirmado |
| `GrupoCapacidadeCentroTrabalho` | `GrupoCapacidade` e `CentroTrabalho`, com indice unico para o par | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/GrupoCapacidadeCentroTrabalhoConfig.cs` | Confirmado |
| `RoteiroProducao` | `Produto`, `RoteiroOperacao` e `RoteiroFluxo` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/RoteiroProducaoConfig.cs` | Confirmado |
| `RoteiroOperacao` | `RoteiroProducao` e `GrupoCapacidade` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/RoteiroOperacaoConfig.cs` | Confirmado |
| `RoteiroFluxo` | `RoteiroProducao`, operacao origem e operacao destino | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/RoteiroFluxoConfig.cs` | Confirmado |
| `RoteiroProducao` -> `Operacoes` e `Fluxos` | Delecao configurada como cascade neste relacionamento especifico. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/RoteiroProducaoConfig.cs`; `RoteiroFluxoConfig.cs` | Confirmado |

## Estoque

| Origem | Destino | Evidencia | Classificacao |
|---|---|---|---|
| `AreaEstoque` | `Almoxarifado` e `TipoAreaEstoque` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AreaEstoqueConfig.cs` | Confirmado |
| `LocalizacaoEstoque` | `Almoxarifado`, `AreaEstoque`, `LocalizacaoEstoque` pai e `TipoLocalizacao` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LocalizacaoEstoqueConfig.cs` | Confirmado |
| `LoteMaterial` | `Produto` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LoteMaterialConfig.cs` | Confirmado |
| `SaldoEstoque` | `Produto`, `LoteMaterial`, `Almoxarifado`, `LocalizacaoEstoque` e `UnidadeMedida` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/SaldoEstoqueConfig.cs` | Confirmado |
| `MovimentoEstoque` | Produto, lote, unidade, almoxarifado/localizacao de origem e destino e movimento de origem | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/MovimentoEstoqueConfig.cs` | Confirmado |
| `ReservaEstoque` | Produto, lote, almoxarifado, localizacao e unidade | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/ReservaEstoqueConfig.cs` | Confirmado |
| `BloqueioEstoque` | Produto, lote, almoxarifado, localizacao e unidade | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/BloqueioEstoqueConfig.cs` | Confirmado |
| `RecebimentoEstoqueItem` | Recebimento, produto, lote, almoxarifado/localizacao e unidade | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/RecebimentoEstoqueItemConfig.cs` | Confirmado |
| `TransferenciaEstoque` | Almoxarifado origem e destino | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/TransferenciaEstoqueConfig.cs` | Confirmado |
| `TransferenciaEstoqueItem` | Transferencia, produto, lote, localizacoes origem/destino e unidade | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/TransferenciaEstoqueItemConfig.cs` | Confirmado |
| `AjusteEstoque` | Almoxarifado | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AjusteEstoqueConfig.cs` | Confirmado |
| `AjusteEstoqueItem` | Ajuste, produto, lote, localizacao e unidade | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AjusteEstoqueItemConfig.cs` | Confirmado |

## Geografia e comercial

| Origem | Destino | Evidencia | Classificacao |
|---|---|---|---|
| `Estado` | `Pais` | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/EstadoConfig.cs` | Confirmado |
| `Municipio` | `Estado` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Municipio.cs`; config em `BACKEND/PRPA/App.Infra.Data/Map/PRPA/MunicipioConfig.cs` | Confirmado |
| `Pedido` e `PedidoItem` | Cliente/produto sao indicados por entidades e DbSets, mas os detalhes completos nao foram revalidados nesta etapa. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Pedido.cs`; `PedidoItem.cs`; `ProjetoContext.cs` | Provavel |

## Nao identificado

| Item | Evidencia | Classificacao |
|---|---|---|
| Cardinalidade completa de todos os campos terminados em `id` que nao possuem config ou navegacao explicita observada. | Entidades de estoque possuem varios campos de status/tipo/motivo sem entidade correspondente identificada. | Nao identificado |
| Regras de integridade aplicadas fora do EF, como triggers ou constraints criadas manualmente no banco. | Somente codigo-fonte e migrations foram analisados. | Nao identificado |
