# Inventário Funcional do Projeto MES

## 1. Identificação

| Item | Informação | Evidência | Status |
|---|---|---|---|
| Nome do projeto | Projeto MES/MOM com BACKEND `PRPA` e FRONTEND Angular `prpa`. | `BACKEND/PRPA/PRPA/PRPA.csproj`; `FRONTEND/angular.json` | Confirmado |
| Data da análise | 2026-07-14. | Contexto da execução. | Confirmado |
| Repositórios analisados | `BACKEND`, `FRONTEND`, `MES-ProjectBook`. | `AGENTS.md` | Confirmado |
| Branch/commit BACKEND | Não identificado; `BACKEND` não foi reconhecido como repositório Git. | `git -C BACKEND rev-parse --abbrev-ref HEAD` retornou erro. | Não identificado |
| Branch/commit FRONTEND | Branch `main`, commit `3af07e1`. | `git -C FRONTEND rev-parse --abbrev-ref HEAD`; `git -C FRONTEND rev-parse --short HEAD` | Confirmado |
| Escopo | Análise estática cruzada de entidades, DTOs, controllers, services, repositories, validators, mappings, DbContext, migrations, configurações, rotas, componentes, serviços HTTP, mocks e integrações. | `BACKEND/PRPA/**`; `FRONTEND/src/app/**`; `FRONTEND/src/environments/**` | Confirmado |
| Limitações | Não houve execução de aplicação, banco, testes end-to-end ou validação runtime. | Restrição da tarefa. | Confirmado |

Arquivos analisáveis contabilizados: 663 no BACKEND e 1484 no FRONTEND. Também foram identificados 50 controllers, 49 services e 48 validators no BACKEND; 66 services e 125 componentes no FRONTEND.

## 2. Critérios de classificação

### Implementado
Existe fluxo coerente entre backend e frontend, com persistência ou integração real e sem dependência principal de mock.

### Parcialmente implementado
Existe parte relevante do fluxo, mas faltam componentes essenciais, integração, persistência, validação, regra operacional ou conclusão do processo.

### Cadastro estrutural
Existe CRUD ou manutenção de dados mestres, com backend, entidade, persistência e tela, mas o item ainda não representa processo operacional completo.

### Mockado ou estático
A interface exibe dados fixos, arrays locais, exemplos ou respostas simuladas.

### Não identificado
Não foi encontrada evidência suficiente.

### Não implementado
Há referência documental, menu, rota ou dependência esperada no escopo MES/MOM, mas não há implementação funcional correspondente identificada.

## 3. Resumo executivo

| Métrica | Quantidade | Critério |
|---|---:|---|
| Módulos/domínios identificados | 9 | Cadastros, estoque/logística, produção, qualidade, rastreabilidade, OEE/indicadores, manutenção, integrações, administração/segurança. |
| Implementado | 2 | Autenticação/JWT e menu/permissões têm fluxo backend/frontend. |
| Parcialmente implementado | 10 | Fluxos operacionais ou dashboards com lacunas funcionais. |
| Cadastro estrutural | 25 | CRUDs e telas de dados mestres encontrados. |
| Mockado ou estático | 3 | MES/OEE e dados locais de backlog/Gantt. |
| Não identificado | 8 | Regras, endpoints ou módulos sem evidência suficiente. |
| Não implementado | 8 | Integrações industriais, manutenção, qualidade completa e fluxos MES de execução. |

Módulos mais maduros: autenticação, menu/permissões, cadastros de produto, cadastros de estoque e roteiro de produção como estrutura técnica. Principais lacunas: regras transacionais de estoque, execução MES de chão de fábrica, qualidade, manutenção, OEE real e integrações industriais.

## 4. Mapa funcional por domínio

### 4.1 Cadastros mestres

| Item | Status | Objetivo funcional | Entidades/APIs/Telas | Persistência/validações | Pendências | Evidências |
|---|---|---|---|---|---|---|
| Produto | Cadastro estrutural | Manter dados mestres de produto. | `Produto`; `ProdutoController`; `listproduto`, `cadproduto`. | `CPRODUTO`; `ProdutoValidator`. | Não representa processo operacional completo. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs`; `BACKEND/PRPA/PRPA/Controllers/ProdutoController.cs`; `FRONTEND/src/app/application/cadastro/produto/**` |
| Família/grupo/subgrupo/tipo/status de produto | Cadastro estrutural | Classificar produtos. | Entidades/controllers ProdutoFamilia, ProdutoGrupo, ProdutoSubgrupo, ProdutoTipo, ProdutoStatus; rotas `listproduto*`. | DbSets/mappings/validators correspondentes. | Categoria separada não identificada. | `BACKEND/PRPA/App.Domain/Entities/PRPA/*Produto*.cs`; `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` |
| Unidade de medida | Cadastro estrutural | Manter unidade e casas decimais. | `UnidadeMedida`; `UnidadeMedidaController`; `listunidademedida`. | `CUNIDADEMEDIDA`; `UnidadeMedidaValidator`. | Conversão entre unidades não identificada. | `BACKEND/PRPA/App.Domain/Entities/PRPA/UnidadeMedida.cs`; `BACKEND/PRPA/App.Service/Validators/UnidadeMedidaValidator.cs` |
| Códigos de identificação | Cadastro estrutural | Manter códigos por produto/tipo. | `ProdutoCodigoIdentificacao`, `TipoCodigoIdentificacaoProduto`; controllers e telas correspondentes. | DbSets/validators correspondentes. | Uso operacional não identificado. | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoCodigoIdentificacao.cs`; `FRONTEND/src/app/application/cadastro/produtocodigoidentificacao/**` |
| Especificações de produto | Cadastro estrutural | Manter especificações, tipos e área responsável. | `ProdutoEspecificacao`, `TipoEspecificacaoProduto`, `AreaResponsavelEspecificacao`. | DbSets/validators correspondentes. | Não há fluxo de qualidade usando essas especificações. | `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoEspecificacao.cs`; `FRONTEND/src/app/application/cadastro/produtoespecificacao/**` |
| Almoxarifado | Cadastro estrutural | Manter armazéns/almoxarifados. | `Almoxarifado`; `AlmoxarifadoController`; `listalmoxarifado`. | `CALMOXARIFADO`; `AlmoxarifadoValidator`. | Regras operacionais não confirmadas. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Almoxarifado.cs`; `FRONTEND/src/app/application/cadastro/almoxarifado/**` |
| Tipo/área/localização/lote de estoque | Cadastro estrutural | Manter estrutura física e lote. | `TipoAreaEstoque`, `AreaEstoque`, `TipoLocalizacao`, `LocalizacaoEstoque`, `LoteMaterial`; controllers e telas correspondentes. | DbSets/validators/mappings correspondentes. | Divergência provável em `AreaEstoque` vs `area-estoque`; capacidade e rastreabilidade completa não confirmadas. | `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs`; `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`; `FRONTEND/src/app/application/cadastro/localizacaoestoque/**` |
| Cliente | Cadastro estrutural | Manter clientes. | `Cliente`; `ClienteController`; `listcliente`; backlog também consome `Cliente`. | `CCLIENTE`; `ClienteValidator`. | Escopo comercial/backlog precisa validação. | `BACKEND/PRPA/App.Domain/Entities/PRPA/Cliente.cs`; `FRONTEND/src/app/application/proposta/backlog/services/backlog.service.ts` |
| Recursos | Cadastro estrutural | Manter centro de trabalho, grupo e vínculo. | `CentroTrabalho`, `GrupoCapacidade`, `GrupoCapacidadeCentroTrabalho`; controllers/telas correspondentes. | DbSets/validators/mappings. | Uso na execução MES não identificado. | `BACKEND/PRPA/App.Domain/Entities/PRPA/CentroTrabalho.cs`; `FRONTEND/src/app/application/cadastro/centrodetrabalho/**` |
| Usuários, perfis e permissões | Implementado | Autenticação, usuários, roles, menu e permissão por role. | Identity, `Menu`, `RoleMenu`, `Perfil`; `AuthController`, `MenuController`, `RoleMenuController`; telas de usuário/menu/perfil. | Identity e `ProjetoContext`; validators de perfil/menu/role. | Revisar endpoints com `[AllowAnonymous]`. | `BACKEND/PRPA/PRPA/Controllers/AuthController.cs`; `FRONTEND/src/app/application/configuracoes/usuario/**`; `FRONTEND/src/app/core/menu/services/menu.service.ts` |
| Fornecedor e cadastros legados | Não identificado | Rotas/telas existem no frontend. | `Fornecedor`, `Senioridade`, `Funcoes`, `Reajuste`, `ParametroGrupo`, `ParametroValor`, arquitetos, vendedores, férias. | Backend correspondente não confirmado; parâmetros têm migration de remoção. | Validar se são escopo atual ou legado. | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; `BACKEND/PRPA/App.Infra.Data/Migrations/20260702173541_RemoveParametroGrupoValorTables.cs` |

### 4.2 Estoque e logística interna

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Movimentacao e consultas operacionais da nova vertical de Estoque | Parcialmente implementado | `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque`; controllers `EstoqueMovimentacoesController`, `EstoqueUnidadesLogisticasController`, `EstoqueLocaisController`; frontend `movimentacaoestoque-nova/**`, `unidades-logisticas/**`, `locais-estoque/**`. | API publica para criar/consultar/confirmar movimentacao e consultas read-only paginadas de UL/local; integracao preenche UnidadeLogisticaId, versao esperada, LocalOrigemId e LocalDestinoId quando os dados sao localizados. Sem entrada/saida/transferencia completas, sem saldos legados e sem escrita no legado. |
| Saldo | Parcialmente implementado | `SaldoEstoque.cs`; `SaldoEstoqueController.cs`; services de saldo em operações. | Atualização automática por operações não confirmada. |
| Entrada e saída | Parcialmente implementado | `MovimentoEstoque.cs`; `MovimentoEstoqueController.cs`; `entradaestoque/**`; `saidaestoque/**`. | Impacto em saldo/disponibilidade não confirmado. |
| Transferência | Parcialmente implementado | `TransferenciaEstoque*`; controllers; `transferenciaestoque/**`. | TODO pede confirmar geração automática de movimentos de saída/entrada. |
| Ajuste | Parcialmente implementado | `AjusteEstoque*`; controllers; `ajusteestoque/**`. | TODO pede confirmar geração de `MovimentoEstoque`. |
| Bloqueio/desbloqueio | Parcialmente implementado | `BloqueioEstoque.cs`; `BloqueioEstoqueController.cs`; `bloqueioestoque/**`. | TODO pede confirmar atualização de saldo; desbloqueio separado não identificado. |
| Reserva | Parcialmente implementado | `ReservaEstoque.cs`; `ReservaEstoqueController.cs`; `reservaestoque/**`. | TODO pede confirmar atualização de saldo. |
| Endereçamento | Cadastro estrutural | Almoxarifado, área, tipo de localização, localização e árvore por área. | Capacidade existe em campos, mas regra operacional não confirmada. |
| Inventário | Parcialmente implementado | `InventarioEstoque.cs`; `InventarioEstoqueController.cs`; `inventarioestoque/**`. | Service frontend chama `InventarioEstoqueItem`, mas controller de item não foi identificado. |
| Recebimento | Parcialmente implementado | `RecebimentoEstoque*`; controllers correspondentes. | Tela operacional de recebimento não identificada no roteamento principal. |
| Expedição, picking, FIFO, FEFO | Não implementado/Não identificado | Nenhum módulo funcional específico identificado; há validade/lote, mas sem regra FIFO/FEFO. | Necessita validação humana. |

### 4.3 Produção MES

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Ordem de produção | Parcialmente implementado | `OrdemProducao.cs`; `OrdemProducaoController.cs`; `ordemproducao/**`. | Sem execução operacional, início/fim ou apontamento confirmados. |
| Roteiro/operações/fluxo | Parcialmente implementado | `RoteiroProducao`, `RoteiroOperacao`, `RoteiroFluxo`; endpoints `workflow`, `validar`, `ativar`; `roteiroproducao/**`. | Estrutura de roteiro, não execução MES. |
| Recursos | Cadastro estrutural | `CentroTrabalho`, `GrupoCapacidade`, vínculo entre ambos. | Uso em apontamento não identificado. |
| Apontamento, consumo, produção, refugo, retrabalho, setup, paradas, coleta de dados, integração chão de fábrica | Não implementado | Não foram encontrados controllers/telas/entidades específicas. | Confirmar escopo MES. |

### 4.4 Qualidade

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Status de qualidade em lote | Parcialmente implementado | Campo `statusqualidadeid` em `LoteMaterial.cs`. | Catálogo, regra e tela de qualidade não identificados. |
| Inspeção de recebimento | Não identificado | `RecebimentoEstoqueItem` tem quantidades aceita/rejeitada. | Plano/coleta/aprovação de inspeção não identificados. |
| Plano de inspeção, não conformidade, CAPA, SPC/CEP, inspeção de processo/final | Não implementado | Sem módulos funcionais identificados. | Confirmar escopo. |

### 4.5 Rastreabilidade

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Lote | Cadastro estrutural | `LoteMaterial.cs`; `LoteMaterialController.cs`; tela `lotematerial`. | Genealogia não confirmada. |
| Serial | Parcialmente implementado | `Produto.ControlaSerial`. | Entidade/fluxo de serial não identificado. |
| Origem/destino | Parcialmente implementado | `MovimentoEstoque` registra origem/destino e movimento de origem. | Consulta direta/reversa não identificada. |
| Auditoria | Parcialmente implementado | `BaseEntity` tem criação/edição e usuários. | Auditoria de eventos não identificada. |
| Genealogia, recall, consumo-produção | Não implementado | Sem implementação específica encontrada. | Confirmar escopo. |

### 4.6 OEE e indicadores

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Dashboard OEE | Mockado ou estático | `FRONTEND/src/app/application/mes/services/mes.service.ts` usa `return of([...])`; `oee-dashboard/**`. | Sem backend/persistência real identificados. |
| Dashboard de produto | Parcialmente implementado | `produto-dashboard/**`; dados derivados de services de produto. | Indicador cadastral, não MES/OEE. |
| Dashboard de ordem de produção | Parcialmente implementado | `ordemproducao-dashboard/**`. | Depende da maturidade da ordem. |
| MTBF, MTTR, downtime, performance real, lead time | Não implementado | Sem módulos funcionais específicos. | Confirmar escopo. |

### 4.7 Manutenção

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Ativos, planos, ordens de manutenção, preventiva, corretiva, preditiva, apontamentos e falhas | Não implementado | Não foram identificadas entidades/controllers/telas de manutenção. | Confirmar se manutenção pertence ao produto atual. |

### 4.8 Integrações

| Integração | Status | Evidências | Pendências |
|---|---|---|---|
| Frontend/API | Implementado | `environment.ts`; `environment.prod.ts`; services Angular com `HttpClient`; controllers backend. | Divergências de endpoint precisam validação. |
| Banco MySQL | Implementado | `Program.cs` usa `UseMySql`; `ProjetoContext.cs`. | Banco runtime não inspecionado. |
| JWT | Implementado | `Program.cs`; `AuthController.cs`; `token.interceptor.ts`. | Revisar exposição anônima. |
| E-mail | Parcialmente implementado | `App.Infra.Core.Message/MailSender.cs`; endpoints forgot/reset/confirm em `AuthController`. | Envio real/configuração externa não validada. |
| Arquivos/imagens | Parcialmente implementado | `UseStaticFiles`; `UploadImagem.cs`; `wwwroot/Imagens`; `urlimagem`/`urlarquivos`. | Contrato completo de upload/download não consolidado. |
| ERP, OPC UA, MQTT, SCADA, PLC, WebSocket/SignalR, mensageria | Não implementado | Busca no código não identificou uso funcional. | Confirmar roadmap. |

### 4.9 Administração e segurança

| Fluxo | Status | Evidências | Pendências |
|---|---|---|---|
| Autenticação | Implementado | `AuthController`, `LoginModel`, JWT no `Program.cs`, `SignInComponent`, `AuthenticationService`. | Runtime não validado. |
| Autorização/roles/perfis/menu | Implementado | `RoleMenuController`, `MenuController`, `PerfilController`, telas de configuração. | Revisar `[AllowAnonymous]` em cadastros. |
| Logs/auditoria | Parcialmente implementado | `ExceptionMiddleware`; `BaseEntity`. | Logging persistente e auditoria detalhada não identificados. |
| Parâmetros | Não identificado | Rotas frontend existem; migration remove tabelas de parâmetros. | Validar escopo. |
| Multiempresa/multiplanta | Não implementado | Nenhuma entidade/tela/configuração específica. | Confirmar requisito. |

## 5. Matriz consolidada de funcionalidades

| Domínio | Funcionalidade | Status | Backend | Frontend | Persistência | Integração real | Evidências | Pendências |
|---|---|---|---|---|---|---|---|---|
| Administração | Autenticação JWT | Implementado | Sim | Sim | Identity | Sim | `AuthController.cs`; `Program.cs`; `authentication.service.ts`; `token.interceptor.ts` | Validar runtime. |
| Administração | Menu/roles/permissões | Implementado | Sim | Sim | Sim | Sim | `MenuController.cs`; `RoleMenuController.cs`; `menu.service.ts` | Revisar autorização. |
| Cadastros | Produto e classificações | Cadastro estrutural | Sim | Sim | Sim | Sim | `Produto*.cs`; `cadastro/produto/**` | Não é processo operacional. |
| Cadastros | Estoque mestre | Cadastro estrutural | Sim | Sim | Sim | Sim | `Almoxarifado`, `AreaEstoque`, `LocalizacaoEstoque`, `LoteMaterial` | Validar rota `area-estoque`. |
| Cadastros | Cliente | Cadastro estrutural | Sim | Sim | Sim | Sim | `ClienteController.cs`; `cliente/**`; `backlog.service.ts` | Escopo comercial. |
| Cadastros | Fornecedor/legados | Não identificado | Não confirmado | Sim | Não identificado | Não identificado | `fornecedores.service.ts`; rotas legadas | Validar escopo. |
| Produção | Roteiro/workflow | Parcialmente implementado | Sim | Sim | Sim | Sim | `RoteiroProducaoController.cs`; `roteiro-producao.service.ts` | Não executa chão de fábrica. |
| Produção | Ordem de produção | Parcialmente implementado | Sim | Sim | Sim | Sim | `OrdemProducaoController.cs`; `ordemproducao/**` | Sem apontamento. |
| Estoque | Entrada/saída | Parcialmente implementado | Sim | Sim | Sim | Sim | `MovimentoEstoqueController.cs`; `entradaestoque/**`; `saidaestoque/**` | Saldo não confirmado. |
| Estoque | Transferência | Parcialmente implementado | Sim | Sim | Sim | Sim | `TransferenciaEstoqueController.cs`; `transferenciaestoque/**` | TODO de movimentos automáticos. |
| Estoque | Reserva/bloqueio/ajuste/inventário | Parcialmente implementado | Sim | Sim | Sim | Sim | Controllers e componentes de operação | Regras de saldo pendentes. |
| Qualidade | Qualidade por lote | Parcialmente implementado | Campo existe | Não identificado | Parcial | Não identificado | `LoteMaterial.cs` | Módulo ausente. |
| OEE | Dashboard OEE | Mockado ou estático | Não | Sim | Não | Não | `mes.service.ts`; `oee-dashboard/**` | Criar backend se aprovado. |
| Proposta | Backlog/Gantt/Kanban | Mockado ou estático | Não confirmado | Sim | Parcial | Parcial | `backlog.service.ts`; `data.ts`; `Gantdata.ts` | `BacklogController` não identificado. |
| Integrações | E-mail auth | Parcialmente implementado | Sim | Sim | Não identificado | Provável | `MailSender.cs`; `AuthController.cs` | Configuração real não validada. |
| Integrações | ERP/OPC/MQTT/SCADA/PLC | Não implementado | Não | Não | Não | Não | Busca no código | Confirmar roadmap. |
| Manutenção | Manutenção industrial | Não implementado | Não | Não | Não | Não | Busca no código | Confirmar escopo. |

## 6. Fluxos ponta a ponta identificados

| Fluxo | Entrada/tela | Serviço frontend | Endpoint/controller | Service/repository/DbContext | Entidade/tabela | Status | Lacunas |
|---|---|---|---|---|---|---|---|
| Cadastro de produto | `CadprodutoComponent` | `produto.service.ts` | `ProdutoController.cs` | `ProdutoServices.cs`; `ProdutoRepository.cs`; `ProjetoContext.cs` | `Produto`; `CPRODUTO` | Cadastro estrutural | Não comprova processo MES. |
| Cadastro de almoxarifado | `CadalmoxarifadoComponent` | `almoxarifado.service.ts` | `AlmoxarifadoController.cs` | `AlmoxarifadoServices.cs`; `AlmoxarifadoRepository.cs` | `Almoxarifado`; `CALMOXARIFADO` | Cadastro estrutural | Sem regras operacionais confirmadas. |
| Cadastro de área | `areaestoque/**` | `areaestoque.service.ts` | `AreaEstoqueController.cs` | `AreaEstoqueServices.cs`; `AreaEstoqueRepository.cs` | `AreaEstoque`; `AreasEstoque` | Cadastro estrutural | Divergência `area-estoque` vs `AreaEstoque`. |
| Cadastro de tipo de localização | `tipolocalizacao/**` | service correspondente | `TipoLocalizacaoController.cs` | `TipoLocalizacaoServices.cs`; repository | `TipoLocalizacao`; `CTIPOLOCALIZACAO` | Cadastro estrutural | Validar runtime. |
| Cadastro de localização | `localizacaoestoque/**` | `localizacaoestoque.service.ts` | `LocalizacaoEstoqueController.cs` | `LocalizacaoEstoqueServices.cs`; repository | `LocalizacaoEstoque`; `CLOCALIZACAOESTOQUE` | Cadastro estrutural | Capacidade sem regra confirmada. |
| Autenticação | `SignInComponent` | `AuthenticationService` | `Auth/login`; `AuthController.cs` | Identity/JWT em `Program.cs` | Identity | Implementado | Runtime não validado. |
| Transferência | `TransferenciaestoqueComponent` | `transferenciaestoque*.service.ts` | `TransferenciaEstoque*Controller.cs` | Services/repositories correspondentes | `TransferenciaEstoque*` | Parcialmente implementado | TODO sobre movimentos automáticos. |
| Roteiro de produção | `CadroteiroproducaoComponent` | `roteiro-producao.service.ts` | `RoteiroProducaoController.cs` | Services/repositories de roteiro | `RoteiroProducao`, `RoteiroOperacao`, `RoteiroFluxo` | Parcialmente implementado | Estrutura, não execução MES. |

## 7. Funcionalidades aparentemente concluídas

| Funcionalidade | Justificativa | Evidências |
|---|---|---|
| Autenticação JWT | Há tela, service HTTP, endpoint, configuração JWT e interceptor. | `FRONTEND/src/app/core/authentication/services/authentication.service.ts`; `FRONTEND/src/app/core/token/token.interceptor.ts`; `BACKEND/PRPA/PRPA/Controllers/AuthController.cs`; `BACKEND/PRPA/PRPA/Program.cs` |
| Menu/roles/permissões | Há entidades, controllers, telas e consumo de menus por role. | `BACKEND/PRPA/PRPA/Controllers/MenuController.cs`; `RoleMenuController.cs`; `FRONTEND/src/app/core/menu/services/menu.service.ts`; `FRONTEND/src/app/application/configuracoes/usuario/**` |

## 8. Funcionalidades parciais

| Funcionalidade | O que existe | O que falta/risco |
|---|---|---|
| Operações de estoque | Telas, services, controllers, entidades e validações. | Regras de saldo/movimento não confirmadas; TODOs no frontend. |
| Roteiro de produção | Workflow, validação e ativação. | Sem apontamento/execução. |
| Ordem de produção | Entidade, controller, lista e dashboard. | Sem início/fim/status operacional consolidado. |
| Recebimento | Entidades/controllers. | Tela operacional não localizada. |
| Qualidade | Campos de qualidade em lote/recebimento. | Módulo de qualidade ausente. |
| Backlog/proposta | Telas, kanban/gantt e services. | `BacklogController` não identificado; dados locais existem. |

## 9. Mocks, dados estáticos e protótipos

| Arquivo | Origem dos dados | Impacto | Status |
|---|---|---|---|
| `FRONTEND/src/app/application/mes/services/mes.service.ts` | `return of([...])` | Alimenta MES/OEE sem backend real. | Mockado ou estático |
| `FRONTEND/src/app/application/proposta/backlog/components/data.ts` | Estruturas locais e geração comentada. | Indica protótipo/dados locais. | Mockado ou estático |
| `FRONTEND/src/app/application/proposta/backlog/components/Gantdata.ts` | Dados locais de Gantt. | Pode alimentar Gantt/backlog. | Mockado ou estático |
| `FRONTEND/src/app/application/cadastro/feriasarquiteto/components/listferiasarquiteto/listferiasarquiteto.component.ts` | Comentário sugere chamada futura de serviço. | Fluxo incompleto. | Parcialmente implementado |

## 10. Menus, rotas e telas sem suporte completo

| Item | Evidência | Situação |
|---|---|---|
| `Fornecedor` | `FRONTEND/src/app/application/cadastro/fornecedores/services/fornecedores.service.ts`; ausência de controller correspondente. | Não identificado |
| `Senioridade`, `Funcoes`, `Reajuste` | Services frontend existem; controllers não identificados. | Não identificado |
| `ParametroGrupo`/`ParametroValor` | Rotas/services frontend; migration remove tabelas. | Necessita validação humana |
| `Backlog` | `backlog.service.ts` chama `Backlog`; controller não identificado. | Parcial/Não identificado |
| `InventarioEstoqueItem` | Service frontend chama endpoint; controller não identificado. | Parcial/Não identificado |
| `area-estoque` | Frontend usa kebab-case; backend observado usa `AreaEstoqueController` com `api/[controller]`. | Necessita validação humana |
| Histórico detalhado de roteiro | Mensagem em `cadroteiroproducao.component.ts` informa dependência de endpoint específico. | Parcialmente implementado |

## 11. Entidades sem uso funcional identificado

| Entidade/item | Evidência | Status |
|---|---|---|
| `LocalizacaoEstoqueTreeNode` | `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoqueTreeNode.cs` | Uso provável em árvore, mas fluxo interno não detalhado. |
| `RecebimentoEstoque` e `RecebimentoEstoqueItem` | Entidades/controllers existem. | Uso funcional parcial; tela não localizada. |
| Campos `status*`, `tipo*`, `motivo*` em estoque | Entidades de estoque. | Catálogos correspondentes não identificados. |

## 12. Endpoints sem consumidor identificado

| Endpoint/controller | Evidência | Status |
|---|---|---|
| `RecebimentoEstoqueController`, `RecebimentoEstoqueItemController` | `BACKEND/PRPA/PRPA/Controllers/RecebimentoEstoque*.cs` | Consumidor frontend não identificado no roteamento principal. |
| `RoteiroOperacaoController`, `RoteiroFluxoController` | Controllers existem. | Frontend usa principalmente `RoteiroProducaoController` via workflow. |
| `SaldoEstoqueController` | Controller existe. | Consumido como consulta em operações, não como tela própria. |

## 13. Telas sem endpoint identificado

| Tela/serviço | Evidência | Status |
|---|---|---|
| Fornecedores | `FRONTEND/src/app/application/cadastro/fornecedores/**` | Não identificado |
| Senioridade | `FRONTEND/src/app/application/cadastro/senioridade/**` | Não identificado |
| Funções | `FRONTEND/src/app/application/cadastro/funcoes/**` | Não identificado |
| Reajuste | `FRONTEND/src/app/application/cadastro/reajuste/**` | Não identificado |
| Parâmetros | `parametrogrupo/**`; `parametrovalor/**` | Divergente com migrations de remoção |
| Backlog | `FRONTEND/src/app/application/proposta/backlog/**` | Não identificado |
| OEE/MES dashboard | `FRONTEND/src/app/application/mes/**` | Mockado ou estático |

## 14. Pendências de validação humana

1. O módulo de fornecedor faz parte do escopo atual ou é legado?
2. `ParametroGrupo` e `ParametroValor` devem existir, apesar da migration de remoção?
3. O endpoint correto de área de estoque deve ser `AreaEstoque` ou `area-estoque`?
4. Transferência deve gerar movimentos de saída e entrada automaticamente?
5. Reserva deve atualizar `qtdreservada` e `qtddisponivel`?
6. Bloqueio deve atualizar `qtdbloqueada` e `qtddisponivel`?
7. Ajuste aprovado deve gerar `MovimentoEstoque`?
8. O dashboard OEE é protótipo ou deve usar dados reais?
9. Ordem de produção deve virar fluxo MES de execução?
10. Qualidade, manutenção e integrações industriais fazem parte do MVP?
11. Backlog/proposta pertence ao produto MES?
12. Endpoints com `[AllowAnonymous]` em cadastros devem continuar públicos?

## 15. Recomendações de documentação

| Documento | Recomendação |
|---|---|
| `MES-ProjectBook/docs/05 - Estoque/Estoque.md` | Detalhar regras aprovadas de saldo, reserva, bloqueio, transferência e ajuste. |
| `MES-ProjectBook/docs/06 - Produção/` | Documentar roteiro versus execução/apontamento MES. |
| `MES-ProjectBook/docs/07 - Qualidade/` | Criar visão do módulo se qualidade entrar no escopo. |
| `MES-ProjectBook/docs/10 - Integrações/Integrações.md` | Registrar decisões sobre ERP, OPC UA, MQTT, SCADA, PLC e e-mail. |
| `MES-ProjectBook/docs/14 - Padrões de Desenvolvimento/Backend.md` | Revisar autorização e rotas após decisão técnica. |
| Roadmap/Backlog | Atualizar somente após validação humana das lacunas. |

## 16. Evidências analisadas

| Repositório | Evidências principais |
|---|---|
| BACKEND | `BACKEND/PRPA/PRPA/Controllers/*.cs`; `BACKEND/PRPA/PRPA/Program.cs`; `BACKEND/PRPA/App.Domain/Entities/**`; `BACKEND/PRPA/App.Service/DTOs/**`; `BACKEND/PRPA/App.Service/Services/*.cs`; `BACKEND/PRPA/App.Service/Validators/*.cs`; `BACKEND/PRPA/App.Infra.Data/Repository/*.cs`; `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs`; mappings e migrations; Identity; middleware; arquivos de e-mail/upload. |
| FRONTEND | `FRONTEND/src/app/app-routing.module.ts`; `application-routing.module.ts`; `cadastro-routing.module.ts`; `operacao-routing.module.ts`; `mes-routing.module.ts`; `FRONTEND/src/app/core/**`; `FRONTEND/src/app/application/cadastro/**`; `FRONTEND/src/app/application/operacao/**`; `FRONTEND/src/app/application/mes/**`; `FRONTEND/src/app/application/proposta/backlog/**`; `FRONTEND/src/environments/**`; `FRONTEND/proxy.conf.json`. |

## 17. Conclusão

O nível de maturidade funcional observado é intermediário para cadastros mestres e administração, mas inicial/parcial para processos MES/MOM operacionais. Os módulos mais consolidados são autenticação, menu/permissões, cadastros de produto, cadastros de estoque e estrutura de roteiro de produção. Os módulos mais incompletos são execução MES de produção, qualidade, manutenção, integrações industriais, OEE real e regras transacionais de estoque.

Riscos principais: considerar CRUD como funcionalidade operacional concluída, tratar dashboards mockados como indicadores reais, manter rotas frontend sem backend correspondente e deixar regras de saldo ambíguas. Próximos passos recomendados, sem implementar nada: validar as pendências humanas, decidir escopo de módulos legados, confirmar regras transacionais de estoque, revisar autorização de endpoints e priorizar documentação funcional dos fluxos aprovados.

## Resumo final da execução

| Item | Resultado |
|---|---|
| Arquivo criado/atualizado | `MES-ProjectBook/docs/01 - Visão Geral do Projeto/INVENTARIO_FUNCIONAL.md` |
| Arquivos analisados no BACKEND | 663 arquivos; 50 controllers; 49 services; 48 validators |
| Arquivos analisados no FRONTEND | 1484 arquivos; 66 services; 125 componentes |
| Módulos identificados | 9 |
| Funcionalidades por status | Implementado: 2; Parcialmente implementado: 10; Cadastro estrutural: 25; Mockado ou estático: 3; Não identificado: 8; Não implementado: 8 |
| Mocks encontrados | `mes.service.ts`; `data.ts`; `Gantdata.ts` |
| Principais divergências | `AreaEstoque` vs `area-estoque`; endpoints frontend sem controller confirmado; parâmetros com migration de remoção; OEE mockado sem backend |
| Perguntas para validação humana | Regras de saldo/movimento; escopo de fornecedor/parâmetros/backlog; autorização anônima; OEE real; qualidade/manutenção/integrações industriais |

## Ajuste tecnico de autorizacao - Estoque - 2026-07-29

| Funcionalidade | Estado | Evidencia | Observacao |
|---|---|---|---|
| Nova vertical operacional de Estoque | Parcial/implementada tecnicamente | `api/estoque/movimentacoes`, `api/estoque/unidades-logisticas`, `api/estoque/locais`; rotas `/operacao/...` | Endpoints novos exigem policies granulares; menu ainda depende de cadastro manual. |
| Autorizacao 401/403 | Implementada no backend da vertical | `PRPA.Auth.EstoqueAuthorization`; controllers novos | 401 para nao autenticado; 403 para autenticado sem permissao. |
| Duplicidade de rotas operacionais | Identificada e mantida | `app-routing.module.ts`; `application-routing.module.ts` | Usar `/operacao/...` como canonico no menu. |
## Atualizacao funcional - Cadastro legado de Locais de Estoque - 2026-07-29

| Funcionalidade | Estado | Observacao |
|---|---|---|
| Cadastro de Locais de Estoque legado | Ajustado | Usa `LocalizacaoEstoque` e persiste em `CLOCALIZACAOESTOQUE`; apos salvar, preserva filtros, recarrega a hierarquia e seleciona o registro salvo. |
| Integracao com nova vertical de Estoque | Nao implementada | `LocalDeEstoque` permanece aggregate root separado em `CLOCALDEESTOQUE`; nao ha sincronizacao automatica entre os modelos. |
| Visao em lista futura | Recomendada | Pode listar Codigo, Nome, Tipo, Caminho completo, Capacidade, Unidade, Entrada, Saida, Producao, Bloqueado e Status sem misturar com `LocalDeEstoque`. |

## Atualizacao de inventario - Cadastro legado de Locais de Estoque - 2026-07-29

A funcionalidade de enderecamento legado `LocalizacaoEstoque` passou a ter tela de listagem paginada no frontend e endpoint read-only dedicado em `GET /api/localizacao-estoque/paginado`. A tela de mapa/cadastro hierarquico foi preservada como editor especializado.

Evidencias: `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`; `BACKEND/PRPA/App.Infra.Data/Persistence/LocalizacaoEstoque/LocalizacaoEstoqueConsultaService.cs`; `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/listlocalizacaoestoque.component.ts`; `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`.

Status: cadastro estrutural com consulta paginada read-only. Pendencias: cadastro da URL no menu dinamico, validacao runtime com API liberada e eventual policy granular se a regra de permissao for aprovada.
## Atualizacao de inventario - Listagem contextual de Locais de Estoque - 2026-07-29

A listagem do cadastro legado de Locais de Estoque em `/home/cadastro/locais-estoque` passou a funcionar por contexto de Armazem e Area de Estoque. O usuario seleciona primeiro o Armazem, a tela carrega somente as Areas daquele Armazem e a grid consulta apenas os Locais da combinacao selecionada.

Colunas finais da grid: Codigo, Nome, Tipo, Caminho, Capacidade, Armazenagem, Bloqueado e Acoes. As colunas Armazem e Area foram removidas por estarem definidas no contexto superior. A paginacao permanece server-side no endpoint legado `GET /api/localizacao-estoque/paginado`, com `armazemId` e `areaEstoqueId` enviados em todas as consultas.

Evidencias: `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/listlocalizacaoestoque.component.ts`; `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/listlocalizacaoestoque.component.html`; `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`.

## Atualizacao de inventario - Navegacao do mapa de Locais de Estoque - 2026-07-30

A listagem `/home/cadastro/locais-estoque` passou a diferenciar as intencoes de navegacao: `Abrir mapa` abre o editor hierarquico em modo de consulta/manutencao, enquanto `Novo local` exige Armazem e Area e abre o editor com `modo=novo` na query string.

No editor hierarquico, `Criar primeiro local` fica visivel apenas quando a Area selecionada nao possui estrutura; `Adicionar filho` substitui `Adicionar abaixo` e cria novo local abaixo do no selecionado; `Criar nova raiz` foi mantido como acao secundaria porque o backend legado permite multiplas raizes por Area; o modo compacto foi preservado como opcao de exibicao `Confortavel/Compacta`.

Evidencias: `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/**`; `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque/**`; `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs`.

## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.

## Decisao aprovada - Finalidade configurada dos Locais de Estoque legados - 2026-07-31

Aprovada em `AS-0008` e formalizada em `DL-0042`. Resumo funcional: o operador podera configurar, por localizacao, a finalidade `Estrutural` ou `Armazenagem`. A classificacao efetiva (`ESTRUTURAL`/`ARMAZENA`/`BLOQUEADO`) sera calculada em runtime e nao sera persistida. O estado `REQUER_FILHO` sera eliminado. `TipoLocalizacao.permitearmazenagem` passa a ser apenas sugestao inicial na criacao de novos locais. Permite, dentro de uma mesma Area de Estoque, ramos com profundidades variaveis e folhas com finalidades diferentes (ex.: `COLUNA1` e `ANDAR1` como armazenadores enquanto `COLUNA3` e estrutural pai de `ANDAR1`). A regra prevê: local bloqueado -> `BLOQUEADO`; local com filhos -> `ESTRUTURAL` efetivo (finalidade persistida pode continuar `Armazenagem`); folha com `finalidade=Armazenagem` -> `ARMAZENA`; folha com `finalidade=Estrutural` -> `ESTRUTURAL`; ao perder o ultimo filho, o local volta a seguir a finalidade persistida. Contrato tecnico em `docs/05 - Estoque/Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`. Excecao controlada, aditiva e pontual ao congelamento semantico do legado aprovado em `DL-0041`. Sem alteracao em `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`. Implementacao pendente: nenhum codigo, migration, script SQL ou `database update` foi aplicado nesta etapa documental.

## Editor hierarquico de Locais de Estoque como workspace continuo - 2026-07-30

A tela `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque` foi ajustada para operar como workspace continuo de configuracao da hierarquia legada `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`.

Modos explicitos do editor: `consulta`, `edicao`, `novo-raiz`, `novo-filho` e `novo-irmao`. O modo passa a orientar titulos, mensagens, acoes e preservacao de contexto.

O contexto de Armazem e Area de Estoque deve permanecer durante selecao de no, edicao, criacao de filho, criacao de irmao, criacao de raiz, salvamento, exclusao e recarga da arvore. A troca de contexto fica explicita por selecao de outro Armazem/Area ou acao `Trocar contexto`.

Criacao de filho: preserva Armazem, Area, arvore e pai destacado; limpa somente campos proprios do novo local; preenche `localizacaoPaiId`; sugere o proximo Tipo de Localizacao; permanece na mesma tela.

Criacao de irmao: usa o mesmo pai do no selecionado, preserva contexto e arvore, sugere o mesmo Tipo de Localizacao da referencia e permanece na mesma tela.

Apos salvar novo local ou edicao, a tela recarrega a hierarquia da Area atual, seleciona o no salvo e permanece no editor. Apos excluir, a tela recarrega a hierarquia e seleciona o pai, o proximo irmao ou deixa a Area em estado vazio com acao `Criar primeiro local`.

A volta para `/home/cadastro/locais-estoque` e uma acao explicita por `Voltar para lista`, preservando query params de Armazem, Area, pagina, pageSize e filtros quando recebidos da grid.

Alteracoes nao salvas passam a solicitar confirmacao antes de selecionar outro no, adicionar filho/irmao, trocar contexto, cancelar ou voltar para a lista. Nao houve alteracao em backend, migration, `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`.
