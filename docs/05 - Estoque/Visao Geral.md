# Estoque

Fonte oficial de dominio: BACKEND. Fonte oficial de interface: FRONTEND. Este documento registra somente itens encontrados no codigo.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Entidades de estoque

| Grupo | Entidades | Evidencia | Classificacao |
|---|---|---|---|
| Cadastros de estrutura fisica | `Almoxarifado`, `TipoAreaEstoque`, `AreaEstoque`, `TipoLocalizacao`, `LocalizacaoEstoque`, `LocalizacaoEstoqueTreeNode` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Almoxarifado.cs`; `AreaEstoque.cs`; `LocalizacaoEstoque.cs`; `TipoLocalizacao.cs`; `TipoAreaEstoque.cs` | Confirmado |
| Lotes e saldos | `LoteMaterial`, `SaldoEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/LoteMaterial.cs`; `SaldoEstoque.cs` | Confirmado |
| Movimentacoes e controle | `MovimentoEstoque`, `ReservaEstoque`, `BloqueioEstoque`, `InventarioEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`; `ReservaEstoque.cs`; `BloqueioEstoque.cs`; `InventarioEstoque.cs` | Confirmado |
| Recebimento, transferencia e ajuste | `RecebimentoEstoque`, `RecebimentoEstoqueItem`, `TransferenciaEstoque`, `TransferenciaEstoqueItem`, `AjusteEstoque`, `AjusteEstoqueItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA/RecebimentoEstoque.cs`; `TransferenciaEstoque.cs`; `AjusteEstoque.cs` | Confirmado |

## Cadastros de estoque na interface

| Tela/rota | Evidencia frontend | Evidencia backend | Classificacao |
|---|---|---|---|
| `listalmoxarifado` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AlmoxarifadoController.cs`; `Almoxarifado.cs` | Confirmado |
| `listtipoareaestoque` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/TipoAreaEstoqueController.cs`; `TipoAreaEstoque.cs` | Confirmado |
| `listareaestoque` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/areaestoque/services/areaestoque.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs`; `AreaEstoque.cs` | Confirmado |
| `listtipolocalizacao` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/TipoLocalizacaoController.cs`; `TipoLocalizacao.cs` | Confirmado |
| `listlocalizacaoestoque` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`; `LocalizacaoEstoque.cs` | Confirmado |
| `listlotematerial` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/LoteMaterialController.cs`; `LoteMaterial.cs` | Confirmado |

## Operacoes de estoque na interface

| Operacao | Rota frontend | Evidencia | Classificacao |
|---|---|---|---|
| Entrada de estoque | `entradaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Saida de estoque | `saidaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Transferencia de estoque | `transferenciaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Reserva de estoque | `reservaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Bloqueio de estoque | `bloqueioestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Inventario de estoque | `inventarioestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Ajuste de estoque | `ajusteestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |

## Endpoints especificos observados

| Controller | Endpoints observados | Evidencia | Classificacao |
|---|---|---|---|
| `LocalizacaoEstoqueController` | `GET`, `GET {id}`, `GET por-area/{areaEstoqueId}`, `GET por-almoxarifado/{almoxarifadoId}`, `GET arvore-por-area/{areaEstoqueId}`, `POST`, `PUT {id}`, `DELETE {id}` | `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs` | Confirmado |
| `TipoLocalizacaoController` | `GET`, `GET ativos`, `GET {id}`, `POST`, `PUT {id}`, `DELETE {id}` | `BACKEND/PRPA/PRPA/Controllers/TipoLocalizacaoController.cs` | Confirmado |
| `LoteMaterialController` | `GET por-produto/{produtoId}`, `GET por-codigo/{codlote}`, `GET consulta` alem de CRUD | `BACKEND/PRPA/PRPA/Controllers/LoteMaterialController.cs` | Confirmado |
| `AreaEstoqueController` | Endpoints de listagem ativa e por almoxarifado foram identificados. | `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs` | Confirmado |

## Regras e relacionamentos de estoque confirmados

| Item | Evidencia | Classificacao |
|---|---|---|
| Areas de estoque pertencem a almoxarifado e tipo de area. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AreaEstoqueConfig.cs` | Confirmado |
| Localizacoes pertencem a almoxarifado, area, tipo e podem ter localizacao pai. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LocalizacaoEstoqueConfig.cs` | Confirmado |
| Lotes pertencem a produto. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LoteMaterialConfig.cs` | Confirmado |
| Saldos vinculam produto, lote, almoxarifado, localizacao e unidade. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/SaldoEstoqueConfig.cs` | Confirmado |
| Movimentos registram origem/destino de almoxarifado e localizacao, produto, lote, unidade e movimento de origem. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/MovimentoEstoqueConfig.cs` | Confirmado |
| Transferencias possuem cabecalho com almoxarifado origem/destino e itens com localizacoes origem/destino. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/TransferenciaEstoqueConfig.cs`; `TransferenciaEstoqueItemConfig.cs` | Confirmado |
| Ajustes possuem cabecalho por almoxarifado e itens por produto/lote/localizacao/unidade. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AjusteEstoqueConfig.cs`; `AjusteEstoqueItemConfig.cs` | Confirmado |

## Funcionalidades parciais e duvidas

| Item | Evidencia | Classificacao |
|---|---|---|
| Atualizacao automatica de saldo a partir de operacoes de estoque nao foi confirmada nesta leitura. | Entidades e telas existem, mas a regra transacional completa nao foi identificada nos arquivos analisados. | Nao identificado |
| Catalogos para status/tipo/motivo de movimentos, reservas, lotes e documentos nao foram identificados como entidades. | Campos aparecem em entidades como `MovimentoEstoque.cs`, `ReservaEstoque.cs`, `LoteMaterial.cs` e `RecebimentoEstoque.cs`. | Nao identificado |
| Integracao com ERP/WMS externo para estoque nao foi encontrada no codigo analisado. | Busca em controllers, services e environments nao confirmou endpoint externo de ERP/WMS. | Nao identificado |

## Especificacoes tecnicas

| Documento | Finalidade | Status |
|---|---|---|
| `Contrato de Persistencia da Primeira Vertical de Estoque.md` | Especifica o contrato relacional, transacional, de idempotencia, outbox e migration inicial para `CriarMovimentacaoDeEstoque` e `ConfirmarMovimentacaoDeEstoque`. | Infraestrutura EF, repositories, idempotencia persistida, reserva transacional, outbox, Unit of Work, migration `20260727170707_CreateFirstEstoqueVertical` e API publica minima gerados; build aprovado e harness com 75 cenarios aprovados; publicacao de outbox, frontend, entrada/saida/transferencia completas, saldos legados e retencao operacional permanecem fora do escopo atual. |
| `Inventario da Implementacao Legada de Locais de Estoque.md` | Inventaria componentes legados de Localizacao/Local de Estoque, classifica destinos, consolida transicao, fonte da verdade, ocupacao, reserva e identificadores. | Arquitetura consolidada com ressalvas operacionais de go-live. |

## API publica da nova vertical

A primeira superficie HTTP da nova vertical foi exposta separadamente dos controllers CRUD legados.

| Rota | Caso de uso | Observacao |
|---|---|---|
| `POST /api/estoque/movimentacoes` | `CriarMovimentacaoDeEstoqueHandler` | Requer `Idempotency-Key`, usuario autenticado com claim `id` numerica e versao esperada da UL. |
| `POST /api/estoque/movimentacoes/{id}/confirmacao` | `ConfirmarMovimentacaoDeEstoqueHandler` | Requer `Idempotency-Key` e versoes esperadas da movimentacao e da UL. |
| `GET /api/estoque/movimentacoes/{id}` | Consulta minima por repository | Retorna DTO de movimentacao, sem expor entidade EF diretamente. |

Nao foram criadas rotas genericas de entrada, saida, transferencia, saldo ou edicao direta de UnidadeLogistica nesta etapa.
