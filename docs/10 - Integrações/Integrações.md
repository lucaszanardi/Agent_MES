# Integracoes

Este documento registra somente integracoes encontradas em codigo do BACKEND e FRONTEND.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Frontend -> Backend

| Integracao | Evidencia | Classificacao |
|---|---|---|
| O frontend de desenvolvimento aponta para `http://localhost:5046/api/`. | `FRONTEND/src/environments/environment.ts` - `urlserver` | Confirmado |
| O frontend de producao aponta para `https://back.techforyou.com.br/api/`. | `FRONTEND/src/environments/environment.prod.ts` - `urlserver` | Confirmado |
| O proxy de desenvolvimento aponta para `https://localhost:7137`. | `FRONTEND/proxy.conf.json` | Confirmado |
| Services Angular montam chamadas HTTP usando `environment.urlserver`. | `FRONTEND/src/app/application/cadastro/**/services/*.ts`; `FRONTEND/src/app/core/authentication/services/authentication.service.ts` | Confirmado |
| O interceptor adiciona token JWT no header `Authorization: Bearer`. | `FRONTEND/src/app/core/token/token.interceptor.ts` | Confirmado |
| Em HTTP 401 o interceptor remove token e redireciona para `/sign-in`. | `FRONTEND/src/app/core/token/token.interceptor.ts` | Confirmado |

## Autenticacao e autorizacao

| Integracao | Evidencia | Classificacao |
|---|---|---|
| Backend configura autenticação JWT Bearer. | `BACKEND/PRPA/PRPA/Program.cs` | Confirmado |
| Frontend chama endpoints de auth como login, recuperacao/reset de senha, usuario, roles e cadastro. | `FRONTEND/src/app/core/authentication/services/authentication.service.ts` | Confirmado |
| Backend possui controllers com `[Authorize]` e varios endpoints com `[AllowAnonymous]`. | `BACKEND/PRPA/PRPA/Controllers/BaseApiController.cs`; `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |
| Menu e permissoes de role sao consumidos pelo frontend via endpoints `rolemenu`. | `FRONTEND/src/app/core/menu/services/menu.service.ts`; `BACKEND/PRPA/PRPA/Controllers/RoleMenuController.cs`; `MenuController.cs` | Confirmado |

## Banco de dados e infraestrutura

| Integracao | Evidencia | Classificacao |
|---|---|---|
| Backend usa MySQL via Entity Framework Core/Pomelo para `ProjetoContext` e `ApplicationDbContext`. | `BACKEND/PRPA/PRPA/Program.cs`; `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | Confirmado |
| Swagger e habilitado no ambiente de desenvolvimento. | `BACKEND/PRPA/PRPA/Program.cs` | Confirmado |
| CORS permite origens locais e dominios `techforyou.com.br`/`admin.techforyou.com.br`. | `BACKEND/PRPA/PRPA/Program.cs` | Confirmado |
| Backend habilita arquivos estaticos. | `BACKEND/PRPA/PRPA/Program.cs` - `UseStaticFiles()` | Confirmado |
| Frontend possui `urlimagem` e `urlarquivos` no environment. | `FRONTEND/src/environments/environment.ts`; `environment.prod.ts` | Confirmado |

## Endpoints de dominio integrados pelo frontend

| Modulo | Evidencia frontend | Evidencia backend | Classificacao |
|---|---|---|---|
| Produto | `FRONTEND/src/app/application/cadastro/produto/services/produto.service.ts` | `BACKEND/PRPA/PRPA/Controllers/ProdutoController.cs` | Confirmado |
| Roteiro de producao | `FRONTEND/src/app/application/cadastro/roteiroproducao/services/roteiro-producao.service.ts` | `BACKEND/PRPA/PRPA/Controllers/RoteiroProducaoController.cs` | Confirmado |
| Almoxarifado | `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AlmoxarifadoController.cs` | Confirmado |
| Area de estoque | `FRONTEND/src/app/application/cadastro/areaestoque/services/areaestoque.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs` | Confirmado com divergencia de padrao de rota |
| Menu/roles | `FRONTEND/src/app/core/menu/services/menu.service.ts` | `BACKEND/PRPA/PRPA/Controllers/MenuController.cs`; `RoleMenuController.cs` | Confirmado |

## Nao identificado

| Integracao | Evidencia | Classificacao |
|---|---|---|
| ERP externo | Nao foi identificado consumo ou publicacao para ERP no codigo analisado. | Nao identificado |
| MES/SCADA/PLC/OPC-UA/MQTT | Nao foi identificado codigo de integracao industrial desse tipo no backend/frontend analisados. | Nao identificado |
| Gateway de pagamento, servicos fiscais ou transportadoras | Nao foi identificado codigo de integracao desse tipo no escopo analisado. | Nao identificado |
| Contrato formal de API, como OpenAPI versionado fora do Swagger em runtime. | Apenas configuracao Swagger em desenvolvimento foi identificada. | Nao identificado |
