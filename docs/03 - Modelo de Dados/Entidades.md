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

## Consulta operacional read-only da nova vertical de Estoque

Implementacao comprovada em `BACKEND/PRPA/PRPA/Controllers/EstoqueUnidadesLogisticasController.cs`, `BACKEND/PRPA/PRPA/Controllers/EstoqueLocaisController.cs` e `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`.

Endpoints criados:

- `GET /api/estoque/unidades-logisticas/{id}`
- `GET /api/estoque/unidades-logisticas?termo={termo}&page={page}&pageSize={pageSize}`
- `GET /api/estoque/unidades-logisticas/{id}/movimentacoes`
- `GET /api/estoque/locais/{id}`
- `GET /api/estoque/locais?termo={termo}&page={page}&pageSize={pageSize}`
- `GET /api/estoque/locais/{id}/unidades-logisticas`

Campos retornados conforme modelo atual:

- Unidade Logistica: id, codigo, produtoId, quantidade, unidadeMedidaId, status, versao, local atual id/codigo, plantId, warehouseId, dataCriacao e indicador de movimentacao ativa.
- Movimentacao recente da UL: id, origem id/codigo, destino id/codigo, status, dataSolicitacao, dataConfirmacao, versao e correlationId.
- Local de Estoque: id, codigo, status, versao, plantId, warehouseId e quantidade de ULs associadas.

Limitacoes documentadas: o agregado `LocalDeEstoque` nao possui descricao, tipo, hierarquia fisica, local pai, capacidade ou ocupacao; o agregado `UnidadeLogistica` nao possui descricao, tipo ou data de atualizacao. A consulta nao usa `LocalizacaoEstoque`, `MovimentoEstoque` ou `SaldoEstoque` legados como fonte de verdade e nao executa escrita.

Permissoes sugeridas para cadastro operacional/menu, sem insercao em banco nesta etapa: `estoque.unidade-logistica.consultar`, `estoque.unidade-logistica.historico`, `estoque.local.consultar`, `estoque.local.conteudo`.
## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.
