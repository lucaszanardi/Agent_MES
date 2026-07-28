# Backend

Este documento registra apenas padroes realmente observados no codigo do BACKEND. Divergencias sao registradas como divergencias, nao como regras oficiais.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Tecnologias observadas

| Item | Evidencia | Classificacao |
|---|---|---|
| Backend em .NET 8.0. | `BACKEND/PRPA/PRPA/PRPA.csproj` - `TargetFramework net8.0` | Confirmado |
| ASP.NET Core Web API com controllers. | `BACKEND/PRPA/PRPA/Controllers/*.cs`; `BACKEND/PRPA/PRPA/Program.cs` | Confirmado |
| Entity Framework Core com MySQL/Pomelo. | `BACKEND/PRPA/PRPA/PRPA.csproj`; `BACKEND/PRPA/PRPA/Program.cs` | Confirmado |
| ASP.NET Identity e JWT Bearer. | `BACKEND/PRPA/PRPA/Program.cs`; `BACKEND/PRPA/PRPA/PRPA.csproj` | Confirmado |
| AutoMapper e FluentValidation.AspNetCore aparecem como dependencias da camada de servico. | `BACKEND/PRPA/App.Service/App.Service.csproj` | Confirmado |
| Swagger/Swashbuckle configurado para desenvolvimento. | `BACKEND/PRPA/PRPA/PRPA.csproj`; `BACKEND/PRPA/PRPA/Program.cs` | Confirmado |

## Organizacao de projetos

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Solucao separada por camadas `App.Domain`, `App.Service`, `App.Infra.Data`, `App.Infra.CrossCutting.IoC` e projeto API `PRPA`. | Estrutura `BACKEND/PRPA/*`; arquivos `.csproj` correspondentes | Confirmado |
| Entidades ficam em `App.Domain/Entities`, com subpastas `PRPA` e `Config`. | `BACKEND/PRPA/App.Domain/Entities/PRPA/*.cs`; `BACKEND/PRPA/App.Domain/Entities/Config/*.cs` | Confirmado |
| Mapeamentos EF ficam em `App.Infra.Data/Map/PRPA` e usam classes `*Config`. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/*Config.cs` | Confirmado |
| Repositorios ficam em `App.Infra.Data/Repository`. | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | Confirmado |
| Controllers ficam em `PRPA/Controllers`. | `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |

## Entidades e DTOs

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Entidades usam propriedades publicas C# e herdam `BaseEntity` em muitos casos. | `BACKEND/PRPA/App.Domain/Entities/BaseEntity.cs`; entidades em `PRPA/*.cs` | Confirmado |
| Varias entidades usam nomes de campos em minusculo para FKs, por exemplo `produtoid`, `almoxarifadoid`, `localizacaoestoqueid`. | `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`; `LocalizacaoEstoque.cs`; `AreaEstoque.cs` | Confirmado |
| Tambem existem propriedades em PascalCase, especialmente campos descritivos e navegacoes. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs`; `Almoxarifado.cs` | Confirmado |
| DTOs/ViewModels existem fora das entidades e sao usados por controllers/services. | `BACKEND/PRPA/App.Domain/DTOs`; `BACKEND/PRPA/App.Domain/ViewModels` | Confirmado |
| Nao ha um unico padrao de nomenclatura para campos, pois minusculo e PascalCase coexistem. | Entidades em `BACKEND/PRPA/App.Domain/Entities/PRPA/*.cs` | Confirmado |

## Services, repositories e unidade de trabalho

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Servico generico `BaseServices<T>` implementa CRUD assíncrono com validacao generica. | `BACKEND/PRPA/App.Service/Services/BaseServices.cs` | Confirmado |
| Repositorio generico `BaseRepository<T>` encapsula operacoes EF como `AddAsync`, `FindAsync`, `Update` e `Remove`. | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | Confirmado |
| IoC registra `IServices<>` -> `BaseServices<>`, `IRepository<>` -> `BaseRepository<>` e `IUnitOfWork` -> `UnitOfWork`. | `BACKEND/PRPA/App.Infra.CrossCutting.IoC/NativeInjectorBootStrapper.cs` | Confirmado |
| Existem services/repositories especificos registrados para varios dominios. | `BACKEND/PRPA/App.Infra.CrossCutting.IoC/NativeInjectorBootStrapper.cs` | Confirmado |

## Controllers e APIs

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Muitos controllers expõem CRUD REST com `GET`, `GET {id}`, `POST`, `PUT` e `DELETE`. | `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |
| `BaseApiController` usa `[Authorize]` e injeta `IUnitOfWork`. | `BACKEND/PRPA/PRPA/Controllers/BaseApiController.cs` | Confirmado |
| Muitos endpoints especificos usam `[AllowAnonymous]`, mesmo com base autorizada. | `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |
| Rotas variam entre `api/[controller]`, kebab-case e aliases especificos. | `BACKEND/PRPA/PRPA/Controllers/RoteiroProducaoController.cs`; `TipoLocalizacaoController.cs`; controllers CRUD | Confirmado |
| Nao ha um unico padrao de rota observado. | `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |

## Entity Framework

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Configuracoes usam Fluent API por classe `IEntityTypeConfiguration<T>`. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/*Config.cs` | Confirmado |
| `ProjetoContext` aplica configuracoes explicitamente em `OnModelCreating`. | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | Confirmado |
| Delete behavior global e `Restrict`, com excecoes especificas como cascade em roteiro. | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs`; `RoteiroProducaoConfig.cs` | Confirmado |
| Migrations existem no projeto de infra de dados. | `BACKEND/PRPA/App.Infra.Data/Migrations/*` | Confirmado |

## Erros e validacoes

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Middleware global de excecao e registrado. | `BACKEND/PRPA/PRPA/Program.cs` - `UseMiddleware<ExceptionMiddleware>()` | Confirmado |
| Validacao com FluentValidation aparece no servico generico. | `BACKEND/PRPA/App.Service/Services/BaseServices.cs` | Confirmado |
| Regras completas de validacao de negocio por modulo nao foram consolidadas nesta leitura. | Validadores existem, mas nao foram todos detalhados. | Nao identificado |

## Divergencias registradas

| Divergencia | Evidencia | Classificacao |
|---|---|---|
| Pasta de repositorio esta padronizada como `Repository`. | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | Confirmado |
| Nomenclatura de FKs mistura minusculo (`produtoid`) e PascalCase (`ProdutoId`). | Entidades e configs em `BACKEND/PRPA/App.Domain/Entities/PRPA` e `BACKEND/PRPA/App.Infra.Data/Map/PRPA` | Confirmado |
| Convencao de rotas nao e uniforme. | Controllers em `BACKEND/PRPA/PRPA/Controllers/*.cs` | Confirmado |
