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
| Movimentacao da nova vertical | `movimentacaoestoque-nova` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts`; `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/**` | Confirmado |

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

## Frontend da nova vertical

A rota operacional `/operacao/movimentacaoestoque-nova` implementa a interface da primeira vertical funcional de Estoque em abas para nova movimentacao e consulta, com etapas de Unidade Logistica, origem/destino, revisao, resultado e confirmacao. A tela permanece separada das telas legadas de entrada, saida, transferencia, reserva, bloqueio, inventario e ajuste.

| Recurso | Evidencia | Observacao |
|---|---|---|
| Modulo | `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/movimentacaoestoque-nova.module.ts` | Lazy-loaded por `OperacaoRoutingModule`. |
| Componente | `MovimentacaoestoqueNovaComponent` | Abas de nova movimentacao e consulta; etapas operacionais; revisao; detalhe estruturado; confirmacao quando o status permite. |
| Service | `MovimentacaoEstoqueNovaService` | Consome apenas `estoque/movimentacoes`. |
| Headers | `Idempotency-Key`, `X-Correlation-ID`, `X-Causation-ID`; `Authorization` via interceptor | POSTs usam idempotencia e correlacao; confirmacao envia causation ID quando ha movimentacao carregada. |
| Erros | 400, 401, 404, 409 e 500 tratados com mensagens de usuario | 409 nao possui retry automatico. |
| Limitacao | Sem endpoints de consulta para Unidade Logistica e LocalDeEstoque | Campos numericos foram mantidos; nao ha mock nem consumo de controllers legados para comandos. |

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
## Autorizacao granular da vertical atual - 2026-07-29

As actions novas de Estoque aplicam policies backend alem de `[Authorize]`. As permissoes efetivas sao: `estoque.movimentacao.consultar`, `estoque.movimentacao.criar`, `estoque.movimentacao.confirmar`, `estoque.unidade-logistica.consultar`, `estoque.unidade-logistica.historico`, `estoque.local.consultar` e `estoque.local.conteudo`.

O backend aceita a permissao como claim `permission`, claim `permissions` separada por virgula, ou role com o mesmo nome da permissao, preservando o padrao JWT/roles existente. Usuario nao autenticado deve receber 401; usuario autenticado sem permissao deve receber 403.

Rotas canonicas para menu dinamico: `/operacao/movimentacaoestoque-nova`, `/operacao/unidades-logisticas` e `/operacao/locais-estoque`. A montagem paralela `/home/operacao/...` permanece por ser preexistente; nao deve ser usada como link novo de menu.

Menu pendente de cadastro manual: Operacao > Estoque > Movimentacao de Estoque, Unidades Logisticas e Locais de Estoque. Nao houve migration nem database update.