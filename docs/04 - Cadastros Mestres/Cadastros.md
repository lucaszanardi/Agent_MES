# Cadastros Mestres

Fonte oficial de dominio: BACKEND. Fonte oficial de interface: FRONTEND. Este documento registra somente cadastros, rotas e telas encontrados no codigo.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Padrao geral observado

| Item | Evidencia | Classificacao |
|---|---|---|
| Cadastros do backend seguem controllers ASP.NET com rota `api/[controller]` em varios casos e operacoes CRUD (`GET`, `GET {id}`, `POST`, `PUT`, `DELETE`). | `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |
| O frontend centraliza rotas de cadastros em `CadastroRoutingModule`. | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | Confirmado |
| Services do frontend usam `environment.urlserver` para compor URLs da API. | `FRONTEND/src/app/application/cadastro/**/services/*.ts`; `FRONTEND/src/environments/environment.ts` | Confirmado |

## Cadastros confirmados na interface

| Cadastro/modulo | Rota frontend | Evidencia frontend | Evidencia backend | Classificacao |
|---|---|---|---|---|
| Estado | `listestado` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/EstadoController.cs`; `BACKEND/PRPA/App.Domain/Entities/PRPA/Estado.cs` | Confirmado |
| Municipio | `listmunicipio` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/MunicipioController.cs`; `Municipio.cs` | Confirmado |
| Pais | `listpais` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/PaisesController.cs`; `Pais.cs` | Confirmado |
| Unidade de medida | `listunidademedida` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/UnidadeMedidaController.cs`; `UnidadeMedida.cs` | Confirmado |
| Produto | `listproduto` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/ProdutoController.cs`; `Produto.cs` | Confirmado |
| Familia/grupo/subgrupo/tipo/status de produto | `listprodutofamilia`, `listprodutogrupo`, `listprodutosubgrupo`, `listprodutotipo`, `listprodutostatus` | `cadastro-routing.module.ts` | Controllers e entidades `ProdutoFamilia`, `ProdutoGrupo`, `ProdutoSubgrupo`, `ProdutoTipo`, `ProdutoStatus` em `BACKEND/PRPA` | Confirmado |
| Versao, especificacao e codigo de identificacao de produto | `listprodutoversao`, `listprodutoespecificacao`, `listprodutocodigoidentificacao` | `cadastro-routing.module.ts` | Entidades e controllers correspondentes em `BACKEND/PRPA` | Confirmado |
| Tipo de especificacao, area responsavel e tipo de codigo de identificacao | `listtipoespecificacaoproduto`, `listarearesponsavelespecificacao`, `listtipocodigoidentificacaoproduto` | `cadastro-routing.module.ts` | Entidades e controllers correspondentes em `BACKEND/PRPA` | Confirmado |
| Estrutura de produto e itens | `listestruturaproduto`, `listitensestruturaproduto` | `cadastro-routing.module.ts` | `EstruturaProduto.cs`, `ItensEstruturaProduto.cs` e controllers correspondentes | Confirmado |
| Centro de trabalho e grupo de capacidade | `listcentrodetrabalho`, `listgrupocapacidade` | `cadastro-routing.module.ts` | `CentroTrabalho.cs`, `GrupoCapacidade.cs`, `GrupoCapacidadeCentroTrabalho.cs` | Confirmado |
| Roteiro de producao | `listroteiroproducao` | `cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/roteiroproducao/services/roteiro-producao.service.ts` | `BACKEND/PRPA/PRPA/Controllers/RoteiroProducaoController.cs`; entidades `Roteiro*` | Confirmado |
| Cliente | `listcliente` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/ClienteController.cs`; `Cliente.cs` | Confirmado |
| Pedido | `listpedido` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/PedidoController.cs`; `Pedido.cs`; `PedidoItem.cs` | Confirmado |
| Ordem de producao | `listordemproducao` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/OrdemProducaoController.cs`; `OrdemProducao.cs` | Confirmado |
| Almoxarifado | `listalmoxarifado` | `cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AlmoxarifadoController.cs`; `Almoxarifado.cs` | Confirmado |
| Tipo de area e area de estoque | `listtipoareaestoque`, `listareaestoque` | `cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/areaestoque/services/areaestoque.service.ts` | `BACKEND/PRPA/PRPA/Controllers/TipoAreaEstoqueController.cs`; `AreaEstoqueController.cs` | Confirmado |
| Tipo de localizacao e localizacao de estoque | `listtipolocalizacao`, `listlocalizacaoestoque` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/TipoLocalizacaoController.cs`; `LocalizacaoEstoqueController.cs` | Confirmado |
| Lote de material | `listlotematerial` | `cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/LoteMaterialController.cs`; `LoteMaterial.cs` | Confirmado |

## Cadastros ou telas com backend nao confirmado nesta leitura

| Item frontend | Evidencia | Situacao | Classificacao |
|---|---|---|---|
| `listsenioridade`, `listfuncoes`, `listcategoriaaquisicoes`, `listreajuste`, `listcategoriafornecedores`, `listfornecedores`, `listparametrogrupo`, `listparametrovalor`, `listarquitetos`, `listvendedores`, `listferiasarquitetos` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | Rotas existem no frontend; entidades/controllers correspondentes nao foram identificados no conjunto principal analisado do BACKEND. | Nao identificado |

## Divergencias observadas

| Divergencia | Evidencia | Classificacao |
|---|---|---|
| Rotas kebab-case e PascalCase coexistem entre backend e frontend. Exemplo: frontend de area de estoque usa `area-estoque`; controllers genericos frequentemente usam `api/[controller]`. | `FRONTEND/src/app/application/cadastro/areaestoque/services/areaestoque.service.ts`; `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs` | Confirmado |
| `RoteiroProducaoController` expõe rotas `api/roteiros-producao` e `api/RoteiroProducao`, indicando compatibilidade com dois formatos. | `BACKEND/PRPA/PRPA/Controllers/RoteiroProducaoController.cs` | Confirmado |
