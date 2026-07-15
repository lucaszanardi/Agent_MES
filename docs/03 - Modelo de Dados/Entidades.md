# Entidades

Fonte oficial usada nesta leitura: BACKEND. Este documento registra somente entidades encontradas no codigo.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Padrao base

| Item | Evidencia | Classificacao |
|---|---|---|
| Entidades de dominio herdam uma base com `Id`, `DataCriacao`, `DataEdicao`, `UsuarioCriacao` e `UsuarioEdicao`. | `BACKEND/PRPA/App.Domain/Entities/BaseEntity.cs` - `BaseEntity` | Confirmado |
| O contexto EF expõe DbSets com nomes historicamente prefixados por `C` em varios cadastros. | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` - propriedades `DbSet` | Confirmado |

## Entidades encontradas

| Modulo | Entidades | Evidencia | Classificacao |
|---|---|---|---|
| Configuracao e acesso | `Menu`, `RoleMenu` | `BACKEND/PRPA/App.Domain/Entities/Config/Menu.cs`; `BACKEND/PRPA/App.Domain/Entities/Config/RoleMenu.cs` | Confirmado |
| Geografia | `Pais`, `Estado`, `Municipio` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Pais.cs`; `Estado.cs`; `Municipio.cs` | Confirmado |
| Produto | `Produto`, `ProdutoTipo`, `ProdutoFamilia`, `ProdutoGrupo`, `ProdutoSubgrupo`, `ProdutoStatus`, `ProdutoVersao`, `ProdutoEspecificacao`, `ProdutoCodigoIdentificacao`, `TipoCodigoIdentificacaoProduto`, `TipoEspecificacaoProduto`, `AreaResponsavelEspecificacao`, `UnidadeMedida` | `BACKEND/PRPA/App.Domain/Entities/PRPA/*.cs`; `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | Confirmado |
| Engenharia de produto | `EstruturaProduto`, `ItensEstruturaProduto` | `BACKEND/PRPA/App.Domain/Entities/PRPA/EstruturaProduto.cs`; `ItensEstruturaProduto.cs` | Confirmado |
| Producao | `OrdemProducao`, `CentroTrabalho`, `GrupoCapacidade`, `GrupoCapacidadeCentroTrabalho`, `RoteiroProducao`, `RoteiroOperacao`, `RoteiroFluxo` | `BACKEND/PRPA/App.Domain/Entities/PRPA/*.cs`; configs em `BACKEND/PRPA/App.Infra.Data/Map/PRPA/` | Confirmado |
| Comercial | `Cliente`, `Pedido`, `PedidoItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Cliente.cs`; `Pedido.cs`; `PedidoItem.cs` | Confirmado |
| Estoque | `Almoxarifado`, `TipoAreaEstoque`, `AreaEstoque`, `TipoLocalizacao`, `LocalizacaoEstoque`, `LocalizacaoEstoqueTreeNode`, `LoteMaterial`, `SaldoEstoque`, `MovimentoEstoque`, `ReservaEstoque`, `BloqueioEstoque`, `InventarioEstoque`, `RecebimentoEstoque`, `RecebimentoEstoqueItem`, `TransferenciaEstoque`, `TransferenciaEstoqueItem`, `AjusteEstoque`, `AjusteEstoqueItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA/*.cs`; `BACKEND/PRPA/App.Infra.Data/Map/PRPA/*Config.cs` | Confirmado |
| Perfil | `Perfil` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Perfil.cs`; `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | Confirmado |

## Detalhes de entidades principais

| Entidade | Campos/navegacoes observados | Evidencia | Classificacao |
|---|---|---|---|
| `Produto` | Codigo, descricao, descricao comercial, SKU, cor, flags de lote/serial/validade/critico/ativo, peso/volume, FKs para tipo, familia, grupo, subgrupo, unidade e status; colecoes de versoes, especificacoes, codigos, itens de pedido e roteiros. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs` - `Produto` | Confirmado |
| `Almoxarifado` | Codigo, nome, descricao, controle de localizacao e ativo. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Almoxarifado.cs` - `Almoxarifado` | Confirmado |
| `AreaEstoque` | Vinculo com almoxarifado e tipo de area; codigo, nome, descricao; flags para entrada, saida, producao, qualidade, quarentena, expedicao e ativo. | `BACKEND/PRPA/App.Domain/Entities/PRPA/AreaEstoque.cs` - `AreaEstoque` | Confirmado |
| `LocalizacaoEstoque` | Vinculos com almoxarifado, area, localizacao pai e tipo; codigo, nome, capacidade e flags de entrada, saida, producao e bloqueio. | `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs` - `LocalizacaoEstoque` | Confirmado |
| `LoteMaterial` | Produto, fornecedor opcional, codigo do lote, lote do fornecedor, datas de fabricacao/validade/recebimento, status de lote e qualidade, certificado, inspecao, ativo e observacao. | `BACKEND/PRPA/App.Domain/Entities/PRPA/LoteMaterial.cs` - `LoteMaterial` | Confirmado |
| `SaldoEstoque` | Produto, versao opcional, lote opcional, almoxarifado, localizacao, quantidades fisica/reservada/bloqueada/disponivel, unidade e ultima movimentacao. | `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs` - `SaldoEstoque` | Confirmado |
| `MovimentoEstoque` | Numero, tipo/motivo, produto, lote, origem/destino, quantidade, unidade, documento, ordem/operacao, data, usuario, estorno e movimento de origem. | `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs` - `MovimentoEstoque` | Confirmado |
| `ReservaEstoque` | Numero, status/motivo, produto, lote, almoxarifado, localizacao, quantidades necessaria/reservada/consumida/cancelada, unidade, ordem/operacao e datas/usuarios. | `BACKEND/PRPA/App.Domain/Entities/PRPA/ReservaEstoque.cs` - `ReservaEstoque` | Confirmado |
| `RecebimentoEstoque` | Numero, status, tipo/numero de documento, pedido, nota fiscal, fornecedor, data, usuario e observacao. | `BACKEND/PRPA/App.Domain/Entities/PRPA/RecebimentoEstoque.cs` - `RecebimentoEstoque` | Confirmado |

## Nao identificado

| Item | Evidencia | Classificacao |
|---|---|---|
| Entidades especificas para alguns campos de status e tipo em estoque, como `statusloteid`, `statusreservaid`, `tipomovimentoid` e `motivomovimentoid`. | Campos aparecem em `BACKEND/PRPA/App.Domain/Entities/PRPA/LoteMaterial.cs`, `ReservaEstoque.cs`, `MovimentoEstoque.cs`, mas nao foi identificada entidade correspondente no conjunto de entidades listado. | Nao identificado |
| Conteudo real do banco de dados e carga inicial de cadastros. | Apenas modelos, configs e migrations foram analisados. | Nao identificado |
