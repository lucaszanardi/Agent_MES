# Convenções Observadas no Código

Este documento registra apenas padrões realmente observados no código em 2026-07-14. Divergências foram preservadas como divergências; nada aqui transforma padrões inconsistentes em regra oficial.

Legenda: **Confirmado** = observado diretamente; **Provável** = inferido por repetição, sem validação em execução; **Não identificado** = não encontrado.

## Nomenclatura

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Entidades backend em PascalCase | `BACKEND/PRPA/App.Domain/Entities/PRPA` | `Almoxarifado`, `Produto`, `RoteiroProducao` | Confirmado |
| Interfaces com prefixo `I` | `BACKEND/PRPA/App.Domain/Interfaces` | `IAlmoxarifadoServices`, `IRepository<T>` | Confirmado |
| Services backend no plural `Services` | `BACKEND/PRPA/App.Service/Services` | `AlmoxarifadoServices`, `BaseServices<T>` | Confirmado |
| Controllers com sufixo `Controller` | `BACKEND/PRPA/PRPA/Controllers` | `AlmoxarifadoController` | Confirmado |
| DTOs com sufixo `CreateDto` e `UpdateDto` | `BACKEND/PRPA/App.Service/DTOs` | `AlmoxarifadoCreateDto`, `AlmoxarifadoUpdateDto` | Confirmado |
| Validators com sufixo `Validator` | `BACKEND/PRPA/App.Service/Validators` | `AlmoxarifadoValidator` | Confirmado |
| DbSets/tabelas com prefixo `C` em muitos casos | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | `CPRODUTO`, `CALMOXARIFADO`, `CMENU` | Confirmado |
| Frontend usa pastas e classes em minúsculo/concatenação para várias features | `FRONTEND/src/app/application/cadastro` | `almoxarifado`, `listalmoxarifado`, `cadalmoxarifado` | Confirmado |
| Divergência em nomes frontend/backend | `FRONTEND/.../roteiro-producao.service.ts`, `BACKEND/.../RoteiroProducaoController.cs` | endpoint `roteiros-producao` e rota `RoteiroProducao` coexistem | Confirmado |
| Pasta de repositories | `BACKEND/PRPA/App.Infra.Data/Repository` | pasta `Repository` padronizada | Confirmado |

## Organização de pastas

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Backend separado por projetos/camadas | `BACKEND/PRPA` | `App.Domain`, `App.Service`, `App.Infra.Data`, `PRPA` | Confirmado |
| Entidades em `App.Domain/Entities` | `BACKEND/PRPA/App.Domain/Entities` | `PRPA`, `Configuracoes` | Confirmado |
| Interfaces de services/repositories no domínio | `BACKEND/PRPA/App.Domain/Interfaces` | `Interfaces/Services`, `Interfaces/Repositories` | Confirmado |
| DTOs por domínio/entidade | `BACKEND/PRPA/App.Service/DTOs` | `Almoxarifado`, `RoteiroWorkflow`, `Produto` | Confirmado |
| EF mappings separados | `BACKEND/PRPA/App.Infra.Data/Mapping` | `AlmoxarifadoConfig` | Confirmado |
| Migrations do contexto principal na infra | `BACKEND/PRPA/App.Infra.Data/Migrations` | `ProjetoContextModelSnapshot` | Confirmado |
| Migrations de Identity no projeto API | `BACKEND/PRPA/PRPA/Migrations` | `ApplicationDbContextModelSnapshot` | Confirmado |
| Frontend organizado por `core`, `shared`, `application` | `FRONTEND/src/app` | módulos centrais, compartilhados e features | Confirmado |
| Feature frontend com `models`, `services`, `components` | `FRONTEND/src/app/application/cadastro/almoxarifado` | `models`, `services`, `components/list...`, `components/cad...` | Confirmado |
| Divergência: assets/plugins volumosos no repo frontend | `FRONTEND/src/assets/plugins`, `FRONTEND/src/assets/css`, `FRONTEND/src/assets/scss` | CKEditor, waitMe, CSS legado | Confirmado |

## Padrões de entidades

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Maioria das entidades herda `BaseEntity` | `BACKEND/PRPA/App.Domain/Entities/PRPA/*.cs` | `Almoxarifado : BaseEntity` | Confirmado |
| Entidade de junção sem `BaseEntity` | `BACKEND/PRPA/App.Domain/Entities/Configuracoes/RoleMenu.cs` | `RoleMenu` | Confirmado |
| Campos de auditoria são preenchidos nos controllers | `AlmoxarifadoController.cs`, `RoteiroProducaoController.cs` | `DataCriacao`, `DataEdicao`, `UsuarioCriacao`, `UsuarioEdicao` | Confirmado |
| Relacionamentos configurados em mappings e/ou `OnModelCreating` | `BACKEND/PRPA/App.Infra.Data/Mapping`, `ProjetoContext.cs` | `RoteiroProducao` com operações e fluxos | Confirmado |
| Uso de strings para status/tipos em alguns pontos | `RoteiroProducaoController.cs`, validators de roteiro | `ATIVO`, `OPERACAO`, `INICIO`, `FIM` | Confirmado |

## DTOs

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Separação entre DTO de criação e atualização | `BACKEND/PRPA/App.Service/DTOs/*` | `CreateDto`, `UpdateDto` | Confirmado |
| DTO de leitura não é padrão universal | `BACKEND/PRPA/App.Service/DTOs/LocalizacaoEstoque/LocalizacaoEstoqueReadDto.cs` | exceção encontrada | Confirmado |
| DTOs específicos para workflows complexos | `BACKEND/PRPA/App.Service/DTOs/RoteiroWorkflow` | `RoteiroWorkflowDto` | Confirmado |
| Mapeamento via AutoMapper | `BACKEND/PRPA/App.Infra.CrossCutting.IoC/MappingProfile.cs` | perfis de DTO para entidade | Confirmado |

## Services

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Services herdam de `BaseServices<TEntity>` | `BACKEND/PRPA/App.Service/Services` | `AlmoxarifadoServices : BaseServices<Almoxarifado>` | Confirmado |
| Validação chamada por tipo genérico | `BACKEND/PRPA/App.Service/Services/BaseServices.cs` | `PostAsync<V> where V : AbstractValidator<T>` | Confirmado |
| Services específicos geralmente finos | `BACKEND/PRPA/App.Service/Services/*Services.cs` | apenas construtor e herança em muitos arquivos | Confirmado |
| Exceções com lógica específica existem | `LocalizacaoEstoqueServices.cs`, `RoleMenuServices.cs` | montagem de árvore, remoção por role/menu | Confirmado |

## Controllers

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Controllers herdam `BaseApiController` | `BACKEND/PRPA/PRPA/Controllers` | `AlmoxarifadoController : BaseApiController` | Confirmado |
| Rotas padrão `api/[controller]` em muitos controllers | `AlmoxarifadoController.cs` | `[Route("api/[controller]")]` | Confirmado |
| CRUD com `GetAll`, `GetById`, `Post`, `Put`, `Delete` | controllers de cadastros | métodos REST | Confirmado |
| `UnitOfWork.ExecuteAsync` envolve operações | controllers de cadastros | tratamento de erro/transação | Confirmado |
| Divergência: `[AllowAnonymous]` em endpoints CRUD | `AlmoxarifadoController.cs`, `RoteiroProducaoController.cs` | métodos anônimos apesar da base `[Authorize]` | Confirmado |
| Controllers especiais concentram lógica | `AuthController.cs`, `RoteiroProducaoController.cs` | auth e workflow | Confirmado |

## Repositories

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Repository genérico via EF Core | `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs` | `_dbContext.Set<T>()` | Confirmado |
| Repositories específicos herdam genérico | `BACKEND/PRPA/App.Infra.Data/Repository/*Repository.cs` | `AlmoxarifadoRepository : BaseRepository<Almoxarifado>` | Confirmado |
| Interfaces específicas herdam `IRepository<TEntity>` | `BACKEND/PRPA/App.Domain/Interfaces/Repositories` | `IAlmoxarifadoRepository` | Confirmado |
| Atualização marca entidade como modified | `BaseRepository.cs` | `_dbContext.Entry(entity).State = EntityState.Modified` | Confirmado |
| TODO em atualização de dependências | `BaseRepository.cs` | comentário sobre função recursiva | Confirmado |

## Frontend

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Angular com módulos e lazy loading | `FRONTEND/src/app/**/*routing.module.ts` | `loadChildren` | Confirmado |
| Componentes tradicionais, sem standalone na maioria | arquivos `*.component.ts` | `standalone: false` em componentes recentes | Confirmado |
| Serviços por feature com `HttpClient` | `FRONTEND/src/app/application/**/services/*.service.ts` | `AlmoxarifadoService`, `RoteiroProducaoService` | Confirmado |
| Interfaces TypeScript por feature | `FRONTEND/src/app/application/**/models/*.interface.ts` | `Almoxarifado`, `Produto`, `RoteiroProducao` | Confirmado |
| Rotas de listagem começam com `list...` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `listproduto`, `listalmoxarifado` | Confirmado |
| Telas de cadastro começam com `cad...` | feature components | `cadalmoxarifado`, `cadproduto` | Confirmado |
| Normalização defensiva de respostas | `menulateral.component.ts`, `almoxarifado.service.ts` | aceita `$values`, PascalCase/camelCase | Confirmado |
| Uso de RxJS para lifecycle | `menulateral.component.ts` | `Subject`, `takeUntil`, `forkJoin` | Confirmado |
| Divergência: vários frameworks visuais juntos | `package.json`, `angular.json`, `src/assets` | Material, PrimeNG, Syncfusion, Bootstrap, CSS legado | Confirmado |

## APIs

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Base da API vem de `environment.urlserver` | `FRONTEND/src/environments/environment.ts` | `http://localhost:5046/api/` | Confirmado |
| Endpoints PascalCase em vários services | `FRONTEND/.../almoxarifado.service.ts` | `Almoxarifado` | Confirmado |
| Endpoint kebab-case para roteiro | `FRONTEND/.../roteiro-producao.service.ts` | `roteiros-producao` | Confirmado |
| Backend aceita rota alternativa em roteiro | `BACKEND/.../RoteiroProducaoController.cs` | `api/roteiros-producao` e `api/RoteiroProducao` | Confirmado |
| Token JWT via interceptor | `FRONTEND/src/app/core/token/token.interceptor.ts` | header `Authorization` | Confirmado |
| CORS configurado no backend | `BACKEND/PRPA/PRPA/Program.cs` | origens localhost e techforyou | Confirmado |

## Tratamento de erros

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| Middleware global de exceção | `BACKEND/PRPA/PRPA/Program.cs`, `PRPA/Middleware/ExceptionMiddleware.cs` | `app.UseMiddleware<ExceptionMiddleware>()` | Confirmado |
| UnitOfWork retorna erro para controller | controllers CRUD | `if (!string.IsNullOrEmpty(erro)) return BadRequest(erro)` | Confirmado |
| Alguns controllers usam try/catch manual | `AlmoxarifadoController.cs` | `Delete` com rollback e `BadRequest(ex.Message)` | Confirmado |
| Frontend intercepta 401 | `FRONTEND/src/app/core/token/token.interceptor.ts` | limpa token, logout e navega para `/sign-in` | Confirmado |
| Divergência em mensagens | backend/frontend | mensagens em português, inglês e caracteres quebrados | Confirmado |

## Validações

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| FluentValidation no backend | `BACKEND/PRPA/App.Service/Validators` | `RuleFor` em validators | Confirmado |
| Validators chamados pelos services | `BaseServices.cs` | `validator.ValidateAndThrow(entity)` | Confirmado |
| Validação de workflow no controller | `RoteiroProducaoController.cs` | `ValidarWorkflowAsync`, `ValidarWorkflowParaSalvarAsync` | Confirmado |
| Validações frontend com Angular forms | componentes `cad*` | `FormGroup`, campos obrigatórios em componentes observados | Provável |
| Validação de senha customizada | `FRONTEND/src/app/core/validators/password.validator.ts` | validator específico | Confirmado |

## Estilos visuais

| Padrão observado | Caminho | Exemplo | Classificação |
|---|---|---|---|
| SCSS por componente | `FRONTEND/src/app/**/*.component.scss` | arquivos ao lado dos componentes | Confirmado |
| Estilos globais | `FRONTEND/src/styles.scss`, `FRONTEND/src/assets/scss`, `FRONTEND/src/assets/css` | UI kit e CSS global | Confirmado |
| Bootstrap e classes legadas | templates HTML e assets | `row clearfix`, `alert alert-info`, `body` | Confirmado |
| PrimeNG/PrimeIcons disponíveis | `package.json`, `angular.json` | dependências e temas | Confirmado |
| Syncfusion para Gantt/Kanban/Grid | `package.json` | `ej2-angular-gantt`, `ej2-angular-kanban`, `ej2-angular-grids` | Confirmado |
| Chart.js para dashboards | `package.json`, componentes dashboard | gráficos em produto/ordem/OEE | Provável |
| Divergência de identidade visual | `angular.json`, `environment.ts`, assets | nomes `prpa`, `aguavivasports`, `techforyou` coexistem | Confirmado |
