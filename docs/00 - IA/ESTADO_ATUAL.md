# Estado Atual dos Repositórios BACKEND e FRONTEND

Radiografia técnica realizada em 2026-07-14 a partir dos arquivos locais. Nenhum arquivo de `BACKEND` ou `FRONTEND` foi alterado.

Legenda: **Confirmado** = identificado diretamente em código/configuração; **Provável** = inferido por padrão repetido ou integração aparente; **Não identificado** = não encontrado/sem validação em execução.

## Escopo analisado

| Item | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Instruções para agentes | `AGENTS.md` | leitura integral retornou arquivo sem conteúdo | Confirmado |
| Backend | `BACKEND/PRPA/PRPA.sln` | solução .NET | Confirmado |
| Frontend | `FRONTEND/package.json`, `FRONTEND/angular.json`, `FRONTEND/src/app` | aplicação Angular `prpa` | Confirmado |
| Documentação atualizada | `MES-ProjectBook/docs/00 - IA/ESTADO_ATUAL.md` | este documento | Confirmado |
| Documentação atualizada | `MES-ProjectBook/docs/00 - IA/CONVENCOES.md` | convenções observadas | Confirmado |

## Tecnologias e versões identificadas

| Tecnologia | Evidência | Versão | Classificação |
|---|---|---:|---|
| .NET / ASP.NET Core Web API | `BACKEND/PRPA/PRPA/PRPA.csproj`, `Program.cs` | `net8.0` | Confirmado |
| Entity Framework Core | `BACKEND/PRPA/PRPA/PRPA.csproj`, `App.Infra.Data.csproj` | `8.0.0` | Confirmado |
| Pomelo.EntityFrameworkCore.MySql | mesmos `.csproj` | `8.0.0` | Confirmado |
| ASP.NET Identity EF Core | `Program.cs`, `ApplicationDbContext.cs` | `8.0.0` | Confirmado |
| JWT Bearer | `PRPA.csproj`, `Program.cs`, `TokenService.cs` | `8.0.0` / `System.IdentityModel.Tokens.Jwt 8.3.0` | Confirmado |
| Swagger/Swashbuckle | `PRPA.csproj`, `Program.cs` | `6.6.2` | Confirmado |
| AutoMapper | `App.Service.csproj`, `MappingProfile.cs` | `12.0.0` | Confirmado |
| FluentValidation | `App.Service.csproj`, `App.Service/Validators` | `11.3.0` | Confirmado |
| Angular | `FRONTEND/package.json`, `angular.json` | `18.2.13` | Confirmado |
| TypeScript | `FRONTEND/package.json` | `^5.5.4` | Confirmado |
| RxJS | `FRONTEND/package.json` | `~7.8.0` | Confirmado |
| Angular Material/CDK | `FRONTEND/package.json` | `18.2.13` | Confirmado |
| PrimeNG/PrimeFlex/PrimeIcons | `FRONTEND/package.json`, `angular.json` | `primeng 17.18.15`, `primeflex 3.3.1`, `primeicons 6.0.1` | Confirmado |
| Syncfusion Angular | `FRONTEND/package.json`, `angular.json` | `28.2.x` | Confirmado |
| Bootstrap | `FRONTEND/package.json`, `src/assets/css/bootstrap.min.css` | `5.2.3` no pacote | Confirmado |
| Chart.js | `FRONTEND/package.json` | `4.3.0` | Confirmado |

## Estrutura das soluções e projetos

| Área | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Solução backend | `BACKEND/PRPA/PRPA.sln` | solução principal | Confirmado |
| API | `BACKEND/PRPA/PRPA/PRPA.csproj` | projeto Web API | Confirmado |
| Domínio | `BACKEND/PRPA/App.Domain/App.Domain.csproj` | entidades e interfaces | Confirmado |
| Serviços | `BACKEND/PRPA/App.Service/App.Service.csproj` | DTOs, validators, services | Confirmado |
| Infraestrutura de dados | `BACKEND/PRPA/App.Infra.Data/App.Infra.Data.csproj` | EF Core, mappings, repositories, migrations | Confirmado |
| IoC | `BACKEND/PRPA/App.Infra.CrossCutting.IoC/App.Infra.CrossCutting.IoC.csproj` | DI e AutoMapper | Confirmado |
| Mensageria/email | `BACKEND/PRPA/App.Infra.Core.Message/App.Infra.Core.Message.csproj` | `MailSender`, `EmailSettings` | Confirmado |
| Arquivos | `BACKEND/PRPA/App.Infra.Core.Files/App.Infra.Core.Files.csproj` | `UploadImagem` | Confirmado |
| Recursos | `BACKEND/PRPA/App.Resources/App.Resources.csproj` | textos de validação | Confirmado |
| Frontend app | `FRONTEND/angular.json` | projeto Angular `prpa`, source root `src` | Confirmado |
| Frontend módulos | `FRONTEND/src/app/application`, `FRONTEND/src/app/core`, `FRONTEND/src/app/shared` | feature modules, core e shared | Confirmado |

## Arquitetura realmente encontrada

| Item | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Backend em camadas | `App.Domain`, `App.Service`, `App.Infra.Data`, `App.Infra.CrossCutting.IoC`, `PRPA` | separação por projeto | Confirmado |
| Repository genérico | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | `BaseRepository<T>` | Confirmado |
| Service genérico | `BACKEND/PRPA/App.Service/Services/BaseServices.cs` | `BaseServices<T>` | Confirmado |
| Unit of Work | `BACKEND/PRPA/App.Infra.Data/Persistence/UnitOfWork.cs` | `UnitOfWork`, `ExecuteAsync`, `SaveAsync`, `Rollback` | Confirmado |
| DTO + AutoMapper | `BACKEND/PRPA/App.Service/DTOs`, `MappingProfile.cs` | mapeamentos DTO -> entidade | Confirmado |
| Validação por FluentValidation | `BACKEND/PRPA/App.Service/Validators` | `*Validator : AbstractValidator<T>` | Confirmado |
| Dois DbContexts | `ProjetoContext.cs`, `ApplicationDbContext.cs` | dados da aplicação e Identity separados | Confirmado |
| API com controllers por entidade | `BACKEND/PRPA/PRPA/Controllers` | `*Controller : BaseApiController` | Confirmado |
| Autenticação JWT/Identity | `Program.cs`, `AuthController.cs`, `TokenService.cs` | login, refresh token, roles | Confirmado |
| Frontend modular lazy-loaded | `app-routing.module.ts`, `application-routing.module.ts` | `loadChildren` | Confirmado |
| Frontend por feature | `FRONTEND/src/app/application/cadastro/*`, `operacao/*`, `configuracoes/*` | `models`, `services`, `components` | Confirmado |
| Menu dinâmico por role | `FRONTEND/src/app/core/menu/components/menulateral/menulateral.component.ts` | `GetMenubyRole`, `GetSubMenuInRole`, árvore por `sequencia` | Confirmado |

## Módulos existentes

| Módulo | Evidência | Classes/componentes relacionados | Classificação |
|---|---|---|---|
| Configurações/Menu | `App.Domain/Entities/Configuracoes`, `MenuController.cs`, `RoleMenuController.cs`, frontend `configuracoes/menu` | `Menu`, `RoleMenu`, listar/cadastrar menu | Confirmado |
| Segurança/Identidade | `AuthController.cs`, `PRPA/Auth`, `ApplicationDbContext.cs`, frontend `core/authentication`, `configuracoes/usuario` | login, refresh, usuários, roles | Confirmado |
| Cadastros geográficos | `EstadoController.cs`, `MunicipioController.cs`, `PaisesController.cs`, frontend `cadastro` | `Estado`, `Municipio`, `Pais` | Confirmado |
| Produto/engenharia | entidades/controllers `Produto*`, `EstruturaProduto`, `ItensEstruturaProduto`; frontend `cadastro/produto*` | produto, versões, grupos, estrutura | Confirmado |
| Produção/roteiro | `RoteiroProducaoController.cs`, `RoteiroOperacaoController.cs`, `RoteiroFluxoController.cs`, frontend `cadastro/roteiroproducao` | workflow de roteiro | Confirmado |
| Capacidade/centro de trabalho | `CentroTrabalhoController.cs`, `GrupoCapacidadeController.cs`, frontend `centrodetrabalho`, `grupocapacidade` | `CentroTrabalho`, `GrupoCapacidade` | Confirmado |
| Clientes e pedidos | `ClienteController.cs`, `PedidoController.cs`, `PedidoItemController.cs`, frontend `cliente`, `pedido` | `Cliente`, `Pedido`, `PedidoItem` | Confirmado |
| Estoque | controllers e frontend de almoxarifado, localização, lote, saldo, movimento, reserva, bloqueio, inventário, recebimento, transferência e ajuste | entidades `*Estoque`, `LoteMaterial`, `Almoxarifado` | Confirmado |
| MES/OEE | `FRONTEND/src/app/application/mes` | `MesHomeComponent`, `OeeDashboardComponent`, `MesService` | Confirmado |
| Proposta/backlog legado | `FRONTEND/src/app/application/proposta/backlog` | kanban, gantt, atividades, clientes | Confirmado |

## Entidades existentes

| Grupo | Entidades | Caminho | Classificação |
|---|---|---|---|
| Base | `BaseEntity` | `BACKEND/PRPA/App.Domain/Entities/BaseEntity.cs` | Confirmado |
| Configurações | `Menu`, `RoleMenu` | `BACKEND/PRPA/App.Domain/Entities/Configuracoes` | Confirmado |
| Geografia | `Estado`, `Municipio`, `Pais` | `BACKEND/PRPA/App.Domain/Entities/PRPA` | Confirmado |
| Produto | `Produto`, `ProdutoTipo`, `ProdutoFamilia`, `ProdutoGrupo`, `ProdutoSubgrupo`, `ProdutoStatus`, `ProdutoVersao`, `ProdutoEspecificacao`, `ProdutoCodigoIdentificacao`, `TipoEspecificacaoProduto`, `TipoCodigoIdentificacaoProduto`, `AreaResponsavelEspecificacao`, `UnidadeMedida`, `Perfil` | `BACKEND/PRPA/App.Domain/Entities/PRPA` | Confirmado |
| Engenharia/produção | `EstruturaProduto`, `ItensEstruturaProduto`, `CentroTrabalho`, `GrupoCapacidade`, `GrupoCapacidadeCentroTrabalho`, `OrdemProducao`, `RoteiroProducao`, `RoteiroOperacao`, `RoteiroFluxo` | `BACKEND/PRPA/App.Domain/Entities/PRPA` | Confirmado |
| Comercial | `Cliente`, `Pedido`, `PedidoItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA` | Confirmado |
| Estoque | `TipoAreaEstoque`, `AreaEstoque`, `TipoLocalizacao`, `Almoxarifado`, `LocalizacaoEstoque`, `LocalizacaoEstoqueTreeNode`, `LoteMaterial`, `SaldoEstoque`, `MovimentoEstoque`, `ReservaEstoque`, `BloqueioEstoque`, `InventarioEstoque`, `RecebimentoEstoque`, `RecebimentoEstoqueItem`, `TransferenciaEstoque`, `TransferenciaEstoqueItem`, `AjusteEstoque`, `AjusteEstoqueItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA` | Confirmado |
| Identity | `ApplicationUser`, `IdentityRole`, modelos de auth | `BACKEND/PRPA/PRPA/Context`, `BACKEND/PRPA/PRPA/Auth/Users` | Confirmado |

## DTOs, controllers, services e repositories

| Item | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| DTOs de criação/atualização por entidade | `BACKEND/PRPA/App.Service/DTOs/*/*CreateDto.cs`, `*UpdateDto.cs` | `AlmoxarifadoCreateDto`, `ProdutoCreateDto`, etc. | Confirmado |
| DTOs específicos de workflow | `BACKEND/PRPA/App.Service/DTOs/RoteiroWorkflow` | `RoteiroWorkflowDto`, `RoteiroOperacaoWorkflowDto`, `RoteiroFluxoWorkflowDto`, `RoteiroValidacaoWorkflowDto` | Confirmado |
| DTO read específico | `BACKEND/PRPA/App.Service/DTOs/LocalizacaoEstoque/LocalizacaoEstoqueReadDto.cs` | `LocalizacaoEstoqueReadDto` | Confirmado |
| Controllers REST por entidade | `BACKEND/PRPA/PRPA/Controllers/*Controller.cs` | `GetAll`, `GetById`, `Post`, `Put`, `Delete` | Confirmado |
| Base protegida por autenticação | `BACKEND/PRPA/PRPA/Controllers/BaseApiController.cs` | `[Authorize]` | Confirmado |
| Muitos endpoints CRUD anônimos | `AlmoxarifadoController.cs`, `RoteiroProducaoController.cs` e similares | `[AllowAnonymous]` | Confirmado |
| Auth e usuários | `BACKEND/PRPA/PRPA/Controllers/AuthController.cs` | `login`, `refresh-token`, `Register`, roles, reset password | Confirmado |
| Roteiro com workflow | `BACKEND/PRPA/PRPA/Controllers/RoteiroProducaoController.cs` | `GetWorkflow`, `PutWorkflow`, `Validar`, `Ativar` | Confirmado |
| Service genérico | `BACKEND/PRPA/App.Service/Services/BaseServices.cs` | `PostAsync`, `PutAsync`, `DeleteAsync`, `GetAllAsync`, `FindAsync` | Confirmado |
| Services específicos | `BACKEND/PRPA/App.Service/Services/*Services.cs` | herdam `BaseServices<TEntity>` | Confirmado |
| Repositório genérico | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | `AddAsync`, `FindAsync`, `Update`, `Remove` | Confirmado |
| Repositories específicos | `BACKEND/PRPA/App.Infra.Data/Repository/*Repository.cs` | herdam `BaseRepository<TEntity>` | Confirmado |
| DI explícita | `BACKEND/PRPA/App.Infra.CrossCutting.IoC/NativeInjectorBootStrapper.cs` | registro manual de services/repositories | Confirmado |

## Configurações do Entity Framework

| Item | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Contexto principal | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | `ProjetoContext` | Confirmado |
| Identity context | `BACKEND/PRPA/PRPA/Context/ApplicationDbContext.cs` | `ApplicationDbContext : IdentityDbContext<ApplicationUser>` | Confirmado |
| Provider MySQL | `BACKEND/PRPA/PRPA/Program.cs` | `UseMySql`, `ServerVersion.AutoDetect` | Confirmado |
| Assembly de migrations | `BACKEND/PRPA/PRPA/Program.cs` | `MigrationsAssembly("App.Infra.Data")` | Confirmado |
| Mappings via Fluent API | `BACKEND/PRPA/App.Infra.Data/Mapping/*Config.cs` | `IEntityTypeConfiguration<TEntity>` | Confirmado |
| DeleteBehavior global | `ProjetoContext.OnModelCreating` | FK com `DeleteBehavior.Restrict` | Confirmado |
| Exceções cascade | `ProjetoContext.OnModelCreating` | `RoteiroProducao -> Operacoes/Fluxos` com `Cascade` | Confirmado |
| Índice único | `ProjetoContext.OnModelCreating` | `RoteiroProducao` por `ProdutoId`, `Codigo`, `Versao` | Confirmado |
| DbSets com nomes legados | `ProjetoContext.cs` | `CPRODUTO`, `CALMOXARIFADO`, `CMENU`, etc. | Confirmado |
| DbSets duplicados para roteiro | `ProjetoContext.cs` | `CROTEIROPRODUCAO` e `RoteirosProducao`; equivalentes para operações/fluxos | Confirmado |

## Migrations

| Grupo | Caminho | Evidência | Classificação |
|---|---|---|---|
| Migrations da aplicação | `BACKEND/PRPA/App.Infra.Data/Migrations` | migrations de 2025-01 a 2026-07, snapshot `ProjetoContextModelSnapshot.cs` | Confirmado |
| Migrations Identity | `BACKEND/PRPA/PRPA/Migrations` | `20250116135642_alterInitial`, `20260508192010_NullableRefreshTokenExpiryTime`, snapshot | Confirmado |
| Refatorações de estoque | `App.Infra.Data/Migrations/20260621*`, `202607*` | almoxarifado, localização, lote, saldo, movimento, reserva, bloqueio, inventário, recebimento, transferência, ajuste | Confirmado |
| Parâmetros removidos | `20260701232621_RemoveParametroValorNavProps.cs`, `20260702173541_RemoveParametroGrupoValorTables.cs` | remoção de tabelas/propriedades | Confirmado |
| Criação de migration nesta etapa | não realizada | - | Confirmado |

## Telas, rotas e menus

| Área | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Login | `FRONTEND/src/app/app-routing.module.ts`, `core/authentication/components/sign-in` | rota `sign-in`, `SignInComponent` | Confirmado |
| Home | `FRONTEND/src/app/application/home` | `HomeComponent` | Confirmado |
| Cadastros | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | rotas `listestado`, `listproduto`, `listalmoxarifado`, `listroteiroproducao`, etc. | Confirmado |
| Operações de estoque | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | entrada, saída, transferência, reserva, bloqueio, inventário, ajuste | Confirmado |
| MES/OEE | `FRONTEND/src/app/application/mes/mes-routing.module.ts` | `MesHomeComponent`, `OeeDashboardComponent` | Confirmado |
| Menu lateral dinâmico | `FRONTEND/src/app/core/menu/components/menulateral/menulateral.component.ts` | monta árvore por role e `sequencia` | Confirmado |
| Configuração de menu | `FRONTEND/src/app/application/configuracoes/menu` | listar/cadastrar menu | Confirmado |
| Usuários e roles | `FRONTEND/src/app/application/configuracoes/usuario` | listar, registrar, alterar, roles, vincular role/menu | Confirmado |
| Dashboard | `FRONTEND/src/app/application/dashboard/dashboard-routing.module.ts` | rotas encontradas apenas comentadas | Não identificado |

## Integrações frontend/backend

| Integração | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Base URL dev | `FRONTEND/src/environments/environment.ts` | `urlserver: http://localhost:5046/api/` | Confirmado |
| Base URL prod | `FRONTEND/src/environments/environment.prod.ts` | `https://back.techforyou.com.br/api/` | Confirmado |
| Proxy local | `FRONTEND/proxy.conf.json` | target `https://localhost:7137` | Confirmado |
| Backend launch URLs | `BACKEND/PRPA/PRPA/Properties/launchSettings.json` | `http://localhost:5046`, `https://localhost:7137` | Confirmado |
| CORS | `BACKEND/PRPA/PRPA/Program.cs` | `localhost:4200`, `admin.techforyou.com.br` | Confirmado |
| JWT no frontend | `FRONTEND/src/app/core/token/token.interceptor.ts` | header `Authorization: Bearer`, redirect 401 para `/sign-in` | Confirmado |
| Services Angular por endpoint | `FRONTEND/src/app/application/**/services/*.service.ts` | `HttpClient` + `environment.urlserver` | Confirmado |
| Almoxarifado | `FRONTEND/.../almoxarifado.service.ts`, `BACKEND/.../AlmoxarifadoController.cs` | endpoint `Almoxarifado` | Confirmado |
| Roteiro de produção | `FRONTEND/.../roteiro-producao.service.ts`, `BACKEND/.../RoteiroProducaoController.cs` | endpoint `roteiros-producao`, workflow/validar/ativar | Confirmado |
| Normalização de payload | `almoxarifado.service.ts`, `menulateral.component.ts` | aceita camelCase/PascalCase e coleções `$values` | Confirmado |

## Funcionalidades implementadas e parciais

| Funcionalidade | Caminho | Evidência | Classificação |
|---|---|---|---|
| CRUD genérico backend | controllers, `BaseServices<T>`, `BaseRepository<T>` | métodos REST e persistência EF | Confirmado |
| Autenticação/login/refresh token | `AuthController.cs`, `TokenService.cs` | `Login`, `RefreshToken` | Confirmado |
| Gestão de usuários/roles | `AuthController.cs`, frontend `configuracoes/usuario` | register, listar users/roles, atualizar role | Confirmado |
| Menu por role | `MenuController.cs`, `RoleMenuController.cs`, frontend `core/menu` | vínculo role/menu e menu lateral | Confirmado |
| Cadastros mestres | backend controllers + frontend `application/cadastro` | geografia, produto, estoque, capacidade, clientes/pedidos | Confirmado |
| Operações de estoque | backend estoque + frontend `application/operacao` | telas e endpoints existem | Provável |
| Roteiro de produção com workflow | `RoteiroProducaoController.cs`, frontend `cadastro/roteiroproducao` | nós, fluxos, validação e ativação | Confirmado |
| Dashboards de produto/ordem/OEE | frontend componentes dashboard e Chart.js | gráficos em componentes específicos | Provável |
| Atualização automática de saldos por operações | frontend `operacao/*` | TODOs pedem confirmação no backend | Provável |
| Histórico detalhado de versões de roteiro | `cadroteiroproducao.component.ts` | mensagem diz depender de endpoint específico | Confirmado |
| Parâmetros `ParametroGrupo/ParametroValor` | frontend mantém módulos; backend migrations removem | divergência frontend/backend | Confirmado |

## Mocks, dados estáticos, TODOs e FIXMEs

| Tipo | Caminho | Classe, método ou componente | Classificação |
|---|---|---|---|
| Dados/demo de Gantt | `FRONTEND/src/app/application/proposta/backlog/components/Gantdata.ts`, `data.ts` | dados estáticos | Confirmado |
| Seed/dummy Identity | `BACKEND/PRPA/App.Infra.CrossCutting.Identity/Models/DummyData.cs` | usuário/role `techforyou` | Confirmado |
| TODO técnico | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | TODO para função recursiva em dependências | Confirmado |
| TODO estoque reserva | `FRONTEND/src/app/application/operacao/reservaestoque/components/reservaestoque/reservaestoque.component.ts` | confirmar atualização de saldo | Confirmado |
| TODO estoque bloqueio | `FRONTEND/src/app/application/operacao/bloqueioestoque/components/bloqueioestoque/bloqueioestoque.component.ts` | confirmar atualização de saldo | Confirmado |
| TODO transferência | `FRONTEND/src/app/application/operacao/transferenciaestoque/components/transferenciaestoque/transferenciaestoque.component.ts` | confirmar movimentos origem/destino | Confirmado |
| TODO ajuste | `FRONTEND/src/app/application/operacao/ajusteestoque/components/ajusteestoque/ajusteestoque.component.ts` | confirmar movimento de ajuste | Confirmado |
| FIXME | busca por `FIXME` em backend/frontend analisados | não encontrado | Não identificado |

## Divergências ou inconsistências identificadas

| Divergência | Caminho | Evidência | Classificação |
|---|---|---|---|
| `AGENTS.md` sem conteúdo retornado | `AGENTS.md` | leitura integral sem texto | Confirmado |
| Codificação com caracteres quebrados | `Program.cs`, `TokenInterceptor.ts`, comentários | textos como `Configuraï¿½ï¿½o` | Confirmado |
| Pasta `Repository` | `BACKEND/PRPA/App.Infra.Data/Repository` | grafia padronizada | Confirmado |
| Controllers protegidos na base, mas muitos endpoints anônimos | `BaseApiController.cs`, controllers CRUD | `[Authorize]` e `[AllowAnonymous]` coexistem | Confirmado |
| Ambiente dev inconsistente com proxy | `environment.ts`, `proxy.conf.json`, `launchSettings.json` | `http://localhost:5046` vs `https://localhost:7137` | Confirmado |
| Domínio/produto com nomes antigos | `angular.json`, `environment.ts`, `AuthController.cs` | `aguavivasports`, `Instituto Atlântico`, `techforyou` | Confirmado |
| Frontend mantém módulos sem backend confirmado | `parametrogrupo`, `parametrovalor`, `arquiteto`, `vendedores`, `fornecedores` | backend atual não confirmou todos | Provável |
| Rotas API com nomes diferentes | `RoteiroProducaoController.cs`, `roteiro-producao.service.ts` | kebab-case e PascalCase coexistem | Confirmado |
| Mistura de estilos UI | `package.json`, `angular.json`, assets | Material, PrimeNG, Syncfusion, Bootstrap e CSS legado | Confirmado |
| Comentários e código comentado extensos | `AuthController.cs`, routing modules | blocos antigos comentados | Confirmado |

## Itens que não puderam ser confirmados

| Item | Caminho | Motivo | Classificação |
|---|---|---|---|
| Banco de dados real e schema aplicado | `appsettings*.json`, migrations | não foi executada conexão/consulta no banco | Não identificado |
| Funcionalidade operacional ponta a ponta | backend/frontend | não houve execução da aplicação ou testes end-to-end | Não identificado |
| Estado do menu cadastrado no banco | `MenuController`, frontend `menulateral` | dados reais dependem do banco | Não identificado |
| Políticas de autorização realmente usadas | `Program.cs`, controllers | muitas rotas usam `[AllowAnonymous]`; validação real depende de execução e dados | Provável |
| Cobertura de testes | arquivos `.spec.ts`, ausência de testes backend identificados | não foram executados testes | Não identificado |
| Migrations aplicadas em produção | `App.Infra.Data/Migrations`, `PRPA/Migrations` | arquivos existem, aplicação real não confirmada | Não identificado |

## Auditoria arquitetural - Planta, Armazem e Estrutura Fisica Industrial - 2026-08-03

AS-0009 foi concluida e DL-0043 foi criada com status `Aprovado`.

Decisao aprovada: Planta e a unidade fisica superior de operacao industrial ou logistica, pertencente a uma Empresa, podendo ser Industrial, Logistica ou Mista. Planta agrupa Almoxarifados/Armazens, areas produtivas, linhas, recursos e areas mistas.

`Almoxarifado`/`CALMOXARIFADO` permanece como Armazem/Warehouse. `WarehouseId` equivale a `AlmoxarifadoId` e nao representa cadastro independente. `PlantId` deve ser derivado pela associacao do Almoxarifado, sem repeticao manual em `LocalizacaoEstoque`.

Estado tecnico: entidade Planta, tabela de Planta, vinculo `Almoxarifado -> Planta`, migration, backfill, backend e frontend ainda nao foram implementados/alterados por esta decisao documental.

A decisao sobre manter, integrar ou eliminar `CLOCALDEESTOQUE` permanece pendente em Architecture Session especifica.

## Arquivos analisados de maior relevância

- `AGENTS.md`
- `BACKEND/PRPA/PRPA/PRPA.csproj`
- `BACKEND/PRPA/App.Infra.Data/App.Infra.Data.csproj`
- `BACKEND/PRPA/App.Service/App.Service.csproj`
- `BACKEND/PRPA/PRPA/Program.cs`
- `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs`
- `BACKEND/PRPA/PRPA/Context/ApplicationDbContext.cs`
- `BACKEND/PRPA/App.Infra.CrossCutting.IoC/NativeInjectorBootStrapper.cs`
- `BACKEND/PRPA/PRPA/Controllers/BaseApiController.cs`
- `BACKEND/PRPA/PRPA/Controllers/AuthController.cs`
- `BACKEND/PRPA/PRPA/Controllers/AlmoxarifadoController.cs`
- `BACKEND/PRPA/PRPA/Controllers/RoteiroProducaoController.cs`
- `BACKEND/PRPA/App.Service/Services/BaseServices.cs`
- `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs`
- `FRONTEND/package.json`
- `FRONTEND/angular.json`
- `FRONTEND/proxy.conf.json`
- `FRONTEND/src/environments/environment.ts`
- `FRONTEND/src/environments/environment.prod.ts`
- `FRONTEND/src/app/app-routing.module.ts`
- `FRONTEND/src/app/application/application-routing.module.ts`
- `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`
- `FRONTEND/src/app/application/operacao/operacao-routing.module.ts`
- `FRONTEND/src/app/application/mes/mes-routing.module.ts`
- `FRONTEND/src/app/core/menu/components/menulateral/menulateral.component.ts`
- `FRONTEND/src/app/core/token/token.interceptor.ts`
- `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts`
- `FRONTEND/src/app/application/cadastro/roteiroproducao/services/roteiro-producao.service.ts`

## Pre-deploy da primeira vertical de Estoque em banco demo

| Item | Evidencia | Classificacao |
|---|---|---|
| Reorganizacao backend | `BACKEND/PRPA/App.Infra.Data/Repository`, `Repository/Estoque`, `Persistence/Estoque`, `Mapping/Estoque`, `Context`, `Migrations` | Confirmado |
| Migration revisada | `BACKEND/PRPA/App.Infra.Data/Migrations/20260727170707_CreateFirstEstoqueVertical.cs` | Confirmado |
| Tabelas da migration isolada | `CIDEMPOTENCYREQUEST`, `CLOCALDEESTOQUE`, `COUTBOXMESSAGE`, `CUNIDADELOGISTICA`, `CMOVIMENTACAODEESTOQUE`, `CUNIDADELOGISTICAMOVEMENTRESERVATION` | Confirmado |
| Toolchain EF local | `.config/dotnet-tools.json` com `dotnet-ef` 8.0.0; EF Core/Pomelo 8.0.0 | Confirmado |
| Script SQL de inspecao | `BACKEND/PRPA/App.Infra.Data/.codex-build/CreateFirstEstoqueVertical.sql` e script auxiliar por predecessor imediato em `.codex-build` | Confirmado, nao versionado |
| Banco demo hospedado | Variaveis `MES_DEMO_DB_*` e gate `MES_DEMO_CONFIRMATION` ausentes nesta execucao | Nao identificado |
| Versao real do servidor | Inspecao remota nao executada por gate ausente | Nao identificado |
| Backup/restauracao | Nao informado nesta execucao | Nao identificado |
| Aplicacao da migration | migration `20260727170707_CreateFirstEstoqueVertical` aplicada anteriormente no ambiente configurado; nenhum `database update` executado nesta etapa de API | Confirmado |
## Perguntas para validação humana

- O `AGENTS.md` deveria estar vazio mesmo ou houve problema de codificação/conteúdo?
- As rotas com `[AllowAnonymous]` em CRUDs são intencionais nesta fase?
- As telas legadas de proposta/comercial ainda fazem parte do escopo do MES?
- `ParametroGrupo` e `ParametroValor` devem ser removidos também do frontend, já que migrations removem estruturas no backend?
- O backend deve usar `http://localhost:5046` ou `https://localhost:7137` como alvo padrão do frontend em desenvolvimento?
- As operações de estoque devem atualizar saldos/movimentos automaticamente no backend?
- O histórico de versões de roteiro precisa de endpoint dedicado?

## API publica da primeira vertical de Estoque

| Item | Evidencia | Status |
|---|---|---|
| Controller publico novo | `BACKEND/PRPA/PRPA/Controllers/EstoqueMovimentacoesController.cs` | Confirmado |
| Rotas expostas | `POST /api/estoque/movimentacoes`; `POST /api/estoque/movimentacoes/{id}/confirmacao`; `GET /api/estoque/movimentacoes/{id}` | Confirmado |
| Casos de uso expostos | `CriarMovimentacaoDeEstoqueHandler`; `ConfirmarMovimentacaoDeEstoqueHandler`; consulta por ID via `IMovimentacaoDeEstoqueRepository` | Confirmado |
| Seguranca | `[Authorize]`; sem `[AllowAnonymous]`; requer usuario autenticado com claim `id` numerica para `ActorId` | Confirmado |
| Idempotencia e correlacao | headers `Idempotency-Key`, `X-Correlation-ID` e `X-Causation-ID` | Confirmado |
| Fora do escopo da entrega de API anterior | entrada completa, saida completa, transferencia completa, saldo legado, nova migration, frontend naquele momento e `database update` | Confirmado |

## Frontend da primeira vertical de Estoque

| Item | Evidencia | Status |
|---|---|---|
| Rota frontend | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` com `movimentacaoestoque-nova` | Confirmado |
| Modulo lazy-loaded | `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/movimentacaoestoque-nova.module.ts`; `movimentacaoestoque-nova-routing.module.ts` | Confirmado |
| Componente | `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/components/movimentacaoestoque-nova/movimentacaoestoque-nova.component.ts` com abas de nova movimentacao/consulta, etapas operacionais, revisao e confirmacao | Confirmado |
| Service HTTP | `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/services/movimentacaoestoque-nova.service.ts` | Confirmado |
| Endpoints consumidos | `POST /api/estoque/movimentacoes`; `POST /api/estoque/movimentacoes/{id}/confirmacao`; `GET /api/estoque/movimentacoes/{id}` | Confirmado |
| Headers | `Authorization` via interceptor; `Idempotency-Key`; `X-Correlation-ID`; `X-Causation-ID` quando aplicavel | Confirmado |
| Escopo mantido | Sem uso de `MovimentoEstoqueService` legado, sem PUT/DELETE, sem mocks e sem endpoints legados para comandos | Confirmado |
| Limitacao conhecida | A API `GET /api/estoque/movimentacoes/{id}` nao retorna a versao da Unidade Logistica; a tela solicita esse valor apenas na confirmacao quando a movimentacao foi carregada por consulta direta. | Confirmado |
| Validacao tecnica | `npm.cmd run build` aprovado; testes globais do frontend falham por specs existentes sem providers, fora da nova vertical; lint nao configurado | Confirmado |

## Consulta operacional da nova vertical de Estoque

| Item | Evidencia | Estado |
|---|---|---|
| API de Unidade Logistica | `BACKEND/PRPA/PRPA/Controllers/EstoqueUnidadesLogisticasController.cs`; `IConsultaOperacionalEstoqueService` | Implementado read-only |
| API de Local de Estoque | `BACKEND/PRPA/PRPA/Controllers/EstoqueLocaisController.cs`; `ConsultaOperacionalEstoqueService` | Implementado read-only |
| Frontend de UL | `FRONTEND/src/app/application/operacao/unidades-logisticas/**` | Implementado |
| Frontend de Local | `FRONTEND/src/app/application/operacao/locais-estoque/**` | Implementado |
| Integracao com movimentacao | `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/**` | Consulta UL/local e preenche ids operacionais, origem e versao esperada |
| Separacao do legado | Consultas usam `CUNIDADELOGISTICA`, `CLOCALDEESTOQUE`, `CMOVIMENTACAODEESTOQUE` | Confirmado |
| Limitacoes do modelo | `LocalDeEstoque` nao possui descricao, tipo, hierarquia fisica ou local pai; `UnidadeLogistica` nao possui descricao/tipo/data de atualizacao | Confirmado |
## Ajuste tecnico da autorizacao da vertical de Estoque - 2026-07-29

| Item | Estado | Evidencia |
|---|---|---|
| Autorizacao granular backend | Implementada por policies ASP.NET Core em `PRPA.Auth.EstoqueAuthorization` | `BACKEND/PRPA/PRPA/Auth/EstoqueAuthorization.cs`; controllers `api/estoque/*` |
| 401 | Preservado para usuario nao autenticado via `[Authorize]` e middleware JWT | `BACKEND/PRPA/PRPA/Program.cs`; controllers novos de Estoque |
| 403 | Aplicavel a usuario autenticado sem role/claim da permissao requerida | `EstoqueAuthorization.ConfigurePolicies` |
| Permissoes aceitas | Claim `permission`, claim `permissions` separada por virgula, ou role com o mesmo nome da permissao | `EstoqueAuthorization.UserHasPermission` |
| Rotas canonicas frontend | `/operacao/movimentacaoestoque-nova`, `/operacao/unidades-logisticas`, `/operacao/locais-estoque` | `FRONTEND/src/app/app-routing.module.ts`; `operacao-routing.module.ts` |
| Duplicidade de montagem | Mantida: `OperacaoModule` esta montado em `/operacao` e tambem em `/home/operacao`; nao foi corrigida por ser preexistente e de maior risco | `FRONTEND/src/app/app-routing.module.ts`; `FRONTEND/src/app/application/application-routing.module.ts` |
| Menu dinamico | Pendente de cadastro manual no banco; nao houve migration nem database update | `Menu.Link`; `menulateral.component.html` |
## Cadastro legado de Locais de Estoque - ajuste pos-salvamento - 2026-07-29

| Item | Estado |
|---|---|
| Tela | `FRONTEND/src/app/application/cadastro/localizacaoestoque/**` |
| Modelo usado | `LocalizacaoEstoque` legado |
| Tabela persistida | `CLOCALIZACAOESTOQUE` |
| Nova vertical | `LocalDeEstoque` continua separado em `CLOCALDEESTOQUE`; nao existe sincronizacao automatica nesta correcao |
| Comportamento pos-salvamento | Mantem Armazem/Area, recarrega hierarquia da area, seleciona o local salvo e permanece em edicao |
| Limitacao | A ocupacao real nao foi implementada/simulada; status ocupado so depende de campo retornado pela API quando existir |

## Atualizacao tecnica - Locais de Estoque legados - 2026-07-29

Implementado endpoint `GET /api/localizacao-estoque/paginado` para consulta paginada de `CLOCALIZACAOESTOQUE`, registrado em IoC por `ILocalizacaoEstoqueConsultaService`. A rota frontend recomendada para menu e `/home/cadastro/locais-estoque`; o mapa/editor fica em `/home/cadastro/locais-estoque/mapa`.

Build frontend validado com `npm.cmd run build`. Build backend compila os projetos alterados, mas a solucao completa nao conclui a etapa de copia do projeto `PRPA` porque o processo `PRPA (54952)` mantem DLLs bloqueadas em `BACKEND/PRPA/PRPA/bin/Debug/net8.0`.
## Atualizacao funcional - Listagem contextual de Locais de Estoque - 2026-07-29

| Item | Estado | Evidencia |
|---|---|---|
| Consulta por contexto | A listagem de `LocalizacaoEstoque` passou a exigir selecao de Armazem e Area de Estoque antes de carregar a grid. | `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/listlocalizacaoestoque.component.ts` |
| Areas por Armazem | Ao selecionar Armazem, a tela carrega Areas pelo endpoint legado `AreaEstoque/por-almoxarifado/{id}` e limpa Area/grid ao trocar o contexto. | `AreaEstoqueService.getPorAlmoxarifado` |
| Grid | Colunas Armazem e Area foram removidas da grid porque o contexto fica no bloco superior. | `listlocalizacaoestoque.component.html` |
| Paginacao | Continua server-side via `GET /api/localizacao-estoque/paginado`, sempre enviando `armazemId`, `areaEstoqueId`, `page` e `pageSize`. | `LocalizacaoEstoqueService.pesquisarLocalizacoes` |
| Navegacao | Mapa, Novo local, Editar e Mapa por linha encaminham `armazemId` e `areaEstoqueId` como query string para o editor hierarquico legado. | `abrirMapa` |
| Modelo | Continua usando `LocalizacaoEstoque` legado e `CLOCALIZACAOESTOQUE`; nao usa `LocalDeEstoque` da nova vertical. | `LocalizacaoEstoqueController`; `LocalizacaoEstoqueConsultaService` |

## Atualizacao funcional - Navegacao e semantica do mapa de Locais de Estoque - 2026-07-30

| Item | Estado | Evidencia |
|---|---|---|
| Abrir mapa | A acao da listagem foi renomeada de `Mapa hierarquico` para `Abrir mapa` e navega para o editor sem ativar criacao. | `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/listlocalizacaoestoque.component.html` |
| Novo local | A acao exige Armazem e Area e navega com `modo=novo`, preservando o contexto por query string. | `listlocalizacaoestoque.component.ts` |
| Primeiro local | Em area sem estrutura, o editor exibe `Criar primeiro local`, iniciando local raiz com Armazem/Area preservados e pai nulo. | `cadlocalizacaoestoque.component.html`; `cadlocalizacaoestoque.component.ts` |
| Filho | `Adicionar abaixo` foi renomeado para `Adicionar filho` e fica habilitado somente com no selecionado e proximo nivel disponivel. | `cadlocalizacaoestoque.component.html`; `podeAdicionarFilho` |
| Raizes multiplas | O backend legado monta multiplas raizes e nao foi identificada trava de raiz unica; por isso `Criar nova raiz` foi mantido como acao secundaria, fora do cabecalho principal. | `LocalizacaoEstoqueServices.GetArvorePorAreaAsync`; `cadlocalizacaoestoque.component.html` |
| Modo compacto | Possui efeito visual real e foi movido para controle secundario de exibicao `Confortavel/Compacta`. | `cadlocalizacaoestoque.component.html`; `cadlocalizacaoestoque.component.scss` |

## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.

## Decisao aprovada - Finalidade configurada dos Locais de Estoque legados - 2026-07-31

Aprovada em `AS-0008` e formalizada em `DL-0042`. Resumo: `LocalizacaoEstoque` passara a ter "finalidade configurada" (`Estrutural`/`Armazenagem`) persistida em nova coluna `finalidadelocalizacao` (smallint, NOT NULL, default `Estrutural`) em `CLOCALIZACAOESTOQUE`. A classificacao efetiva (`ESTRUTURAL`/`ARMAZENA`/`BLOQUEADO`) permanecera calculada em runtime e **nao sera persistida**. O estado `REQUER_FILHO` sera eliminado. `TipoLocalizacao.permitearmazenagem` passa a funcionar apenas como sugestao inicial na criacao de novos locais. Regra efetiva: bloqueado -> `BLOQUEADO`; com filhos -> `ESTRUTURAL`; folha com `finalidade=Armazenagem` -> `ARMAZENA`; folha com `finalidade=Estrutural` -> `ESTRUTURAL`. Um local com filhos nunca armazena efetivamente; a finalidade `Armazenagem` pode permanecer persistida enquanto houver filhos; ao perder o ultimo filho, o local volta a seguir a finalidade persistida. Contrato tecnico: `docs/05 - Estoque/Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`. Esta decisao e uma excecao controlada, aditiva e pontual ao congelamento semantico aprovado em `DL-0041`, nao altera `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`, nao promove `LocalizacaoEstoque` a Aggregate Root e nao cria sincronizacao automatica com a nova vertical. Implementacao pendente: nenhuma migration, nenhum script SQL, nenhuma alteracao de codigo e nenhum `database update` foram aplicados nesta etapa documental.

## Editor hierarquico de Locais de Estoque como workspace continuo - 2026-07-30

A tela `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque` foi ajustada para operar como workspace continuo de configuracao da hierarquia legada `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`.

Modos explicitos do editor: `consulta`, `edicao`, `novo-raiz`, `novo-filho` e `novo-irmao`. O modo passa a orientar titulos, mensagens, acoes e preservacao de contexto.

O contexto de Armazem e Area de Estoque deve permanecer durante selecao de no, edicao, criacao de filho, criacao de irmao, criacao de raiz, salvamento, exclusao e recarga da arvore. A troca de contexto fica explicita por selecao de outro Armazem/Area ou acao `Trocar contexto`.

Criacao de filho: preserva Armazem, Area, arvore e pai destacado; limpa somente campos proprios do novo local; preenche `localizacaoPaiId`; sugere o proximo Tipo de Localizacao; permanece na mesma tela.

Criacao de irmao: usa o mesmo pai do no selecionado, preserva contexto e arvore, sugere o mesmo Tipo de Localizacao da referencia e permanece na mesma tela.

Apos salvar novo local ou edicao, a tela recarrega a hierarquia da Area atual, seleciona o no salvo e permanece no editor. Apos excluir, a tela recarrega a hierarquia e seleciona o pai, o proximo irmao ou deixa a Area em estado vazio com acao `Criar primeiro local`.

A volta para `/home/cadastro/locais-estoque` e uma acao explicita por `Voltar para lista`, preservando query params de Armazem, Area, pagina, pageSize e filtros quando recebidos da grid.

Alteracoes nao salvas passam a solicitar confirmacao antes de selecionar outro no, adicionar filho/irmao, trocar contexto, cancelar ou voltar para a lista. Nao houve alteracao em backend, migration, `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`.

## Decisao arquitetural - Identidade unica de LocalizacaoEstoque - 2026-08-03

AS-0010 foi criada com status `Concluida` e DL-0044 foi criada com status `Aprovado`.

Decisao aprovada: `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE` sera a identidade unica fisica e operacional dos enderecos de estoque. `LocalDeEstoque`/`CLOCALDEESTOQUE` deixa de ser identidade operacional independente e sera descontinuado em transicao futura.

Estado informado: `CLOCALIZACAOESTOQUE` possui 18 registros; `CLOCALDEESTOQUE`, `CUNIDADELOGISTICA` e `CMOVIMENTACAODEESTOQUE` possuem 0 registros. Nao ha dados operacionais para migrar.

A remocao fisica de `CLOCALDEESTOQUE` nao foi realizada e dependera de refatoracao completa, migration incremental, revisao SQL, testes e validacao humana.

Nenhum backend, frontend, migration, snapshot ou banco de dados foi alterado nesta atualizacao documental.
