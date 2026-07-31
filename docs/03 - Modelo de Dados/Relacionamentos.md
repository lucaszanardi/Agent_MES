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
