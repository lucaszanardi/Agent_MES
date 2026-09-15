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

## Entrada Direta (EST-OP-02C)

| Item | Evidencia | Classificacao |
|---|---|---|
| Entrada Direta — sincroniza MovimentoEstoque + SaldoEstoque atomicamente | `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs` | Confirmado |
| Conversão de unidade (mesma / produto / global) aplicada na entrada | `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs` | Confirmado |
| Rollback transacional via `IUnitOfWork.ExecuteAsync` | `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs` | Confirmado |
| Entrada com Unidade Logística (UL) opcional | `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs` (linha 78-135) | Confirmado |
| Contrato de resposta enxuto: `EntradaDiretaResponseDto` | `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/EntradaDiretaResponseDto.cs` | Confirmado |
| Contrato inclui: `movimentoId`, `unidadeLogisticaId`, `unidadeLogisticaCodigo`, `statusQualidade` | `EntradaDiretaResponseDto.cs` | Confirmado |
| Frontend recebe tipos primitivos; não recebe entidades de domínio completas | `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts` | Confirmado |
| Mensagem pós-entrada exibe: Movimento ID, UL ID, Código UL, Status Quarentena | `entradaestoque.component.ts` (showSuccessMessage) | Confirmado |
| Botões pós-entrada: "Ver Movimento" (usa movimentoId), "Ver UL" (usa unidadeLogisticaId) | `entradaestoque.component.ts` | Confirmado |
| Correção histórica de `[object Object]` causada por exposição de Value Objects no contrato | `EST-OP-02C.3-D1.20.6` e `EST-OP-02C.3-D1.20.7` | Confirmado |
| Dívida técnica: `MovimentoEstoqueService.cadastrarMovimentoEstoque` tipado como `any` temporariamente | `movimentoestoque.service.ts` | Confirmado |

## Identidade da Unidade Logística

| Item | Evidencia | Classificacao |
|---|---|---|
| `CUNIDADELOGISTICA.Id` gerado pelo banco (AUTO_INCREMENT) | `BACKEND/PRPA/App.Infra.Data/Mapping/Estoque/UnidadeLogisticaConfig.cs` | Confirmado |
| EF Core: `ValueGeneratedOnAdd()` | `UnidadeLogisticaConfig.cs` | Confirmado |
| `UnidadeLogisticaId.Create(0)` permanece INVÁLIDO (domain exception) | `BACKEND/PRPA/App.Domain/Entities/Estoque/Shared/ValueObjects.cs` | Confirmado |
| Estado transiente da entidade nova tratado separadamente do ID persistido | `EntradaDiretaSincronizacaoServices.cs` (linha 119-134) | Confirmado |
| Migration corretiva: `FixUnidadeLogisticaIdentity` | `BACKEND/PRPA/App.Infra.Data/Migrations/20260911112709_FixUnidadeLogisticaIdentity.cs` | Confirmado |
| Remediação: UL antiga Id 0 → Id positivo; referência em `CMOVIMENTOESTOQUE` preservada | Migration designer + SQL | Confirmado |

## ExigeInspecao e Qualidade

| Item | Evidencia | Classificacao |
|---|---|---|
| `Produto.ExigeInspecao` (bool) configurável no cadastro | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs` | Confirmado |
| DTOs persistem: `ProdutoCreateDto`, `ProdutoUpdateDto` | `BACKEND/PRPA/App.Service/DTOs/Produto/` | Confirmado |
| Regra: ExigeInspecao=false → `StatusQualidade = Liberado` | `EntradaDiretaSincronizacaoServices.cs` (linha 72) | Confirmado |
| Regra: ExigeInspecao=true → `StatusQualidade = EmQuarentena` | `EntradaDiretaSincronizacaoServices.cs` (linha 72) | Confirmado |
| Saldo em Quarentena: físico AUMENTA; disponível NÃO AUMENTA | `EntradaDiretaSincronizacaoServices.cs` (linha 144-149) | Confirmado |
| Consumo não pode utilizar saldo em Quarentena | Regras de domínio / `SaldoEstoque` | Confirmado |

## Finalidade Operacional dos Locais de Estoque

| Item | Evidencia | Classificacao |
|---|---|---|
| Hierarquia: LocalizacaoEstoque → AreaEstoque → TipoAreaEstoque → FinalidadeOperacional | `BACKEND/PRPA/App.Domain/Entities/PRPA/` | Confirmado |
| `FinalidadeOperacional` é ENUM | `BACKEND/PRPA/App.Domain/Entities/Estoque/Shared/ValueObjects.cs` | Confirmado |
| Valores: Armazenagem, Quarentena, Refugo, Picking, Recebimento, Expedicao, Producao, Outro | ValueObjects.cs | Confirmado |
| Quarentena determinada APENAS por FinalidadeOperacional (não por nome/flag) | `LocalizacaoEstoqueServices.cs` / Domain | Confirmado |
| NÃO existe flag `EhQuarentena` em LocalizacaoEstoque | Confirmado por ausência | Confirmado |
| Migration: `AddFinalidadeOperacionalToTipoAreaEstoque` | `BACKEND/PRPA/App.Infra.Data/Migrations/20260914193821_AddFinalidadeOperacionalToTipoAreaEstoque.cs` | Confirmado |
| Remediação inicial: MP-01/PRA-01 → Armazenagem; REF-001 → Refugo; QUA-01 → Quarentena; PI-001 → Picking | Migration SQL | Confirmado |

## Validação de Quarentena na Entrada

| Cenário | Resultado | Evidencia |
|---|---|---|
| Produto ExigeInspecao=true + localização não Quarentena | BLOQUEAR | `EntradaDiretaSincronizacaoServices.cs` |
| Produto ExigeInspecao=true + localização Quarentena | PERMITIR | `EntradaDiretaSincronizacaoServices.cs` |
| Produto ExigeInspecao=false + localização Quarentena | BLOQUEAR | `EntradaDiretaSincronizacaoServices.cs` |
| Produto ExigeInspecao=false + localização normal | PERMITIR | `EntradaDiretaSincronizacaoServices.cs` |
| Erros de negócio lançam `DomainException` (mensagem amigável) | DomainException | Confirmado |

## Tratamento de Erros

| Item | Evidencia | Classificacao |
|---|---|---|
| Erros de negócio: mensagem operacional amigável | `ExceptionMiddleware` / Services | Confirmado |
| Frontend NÃO recebe: stack trace, inner exception, path físico, SQL, assembly | `ExceptionMiddleware` / Controllers | Confirmado |
| ExceptionMiddleware separa: erro de negócio vs erro técnico | `BACKEND/PRPA/PRPA/Middleware/ExceptionMiddleware.cs` | Confirmado |

## Próxima Fase

| Item | Status |
|---|---|
| Próxima prioridade funcional: **MAPA DE ESTOQUE** | Definido em `MES-PROJECTBOOK-UPDATE-01` |
| Fase: `EST-OP-02C.4-AUDIT` — Levantamento de arquitetura e dados existentes | Registrado |
| Gestão de capacidade/ocupação permanece no backlog (NÃO é próxima fase) | Confirmado |
