# Inventario da Implementacao Legada de Locais de Estoque

## 1. Objetivo

Registrar o inventario tecnico e funcional da implementacao existente de Locais de Estoque/Localizacao de Estoque e consolidar sua transicao para a primeira vertical funcional de Estoque.

Este documento nao implementa infraestrutura, nao altera codigo, nao cria migration, nao cria schema fisico e nao autoriza escrita da nova vertical em tabelas legadas.

## 2. Fontes analisadas

| Fonte | Uso |
|---|---|
| AS-0004 | Catalogo de agregados e separacao entre dominio, eventos e projecoes. |
| AS-0005 | Primeira vertical funcional Local -> UL -> Movimentacao -> Confirmacao -> Consulta -> Historico. |
| AS-0006 | Riscos tecnicos do legado e classificacao inicial de prontidao. |
| AS-0007 | Arquitetura tecnica implementavel da primeira vertical. |
| DL-0033 a DL-0040 | Idempotencia, concorrencia, transacao, outbox, coexistencia, LocalDeEstoque, reserva e identificadores. |
| `BACKEND/PRPA/App.Domain/Entities/Estoque` | Dominio novo ja implementado. |
| `BACKEND/PRPA/App.Service/Services/Estoque` | Camada de aplicacao abstrata nova. |
| `BACKEND/PRPA/App.Domain.Tests` | Harness atual de dominio/aplicacao. |
| `BACKEND/PRPA/App.Domain/Entities/PRPA` | Entidades legadas de estoque. |
| `BACKEND/PRPA/App.Infra.Data` | DbContext, mappings, repositories e migrations legadas. |
| `BACKEND/PRPA/PRPA/Controllers` | APIs legadas. |
| `FRONTEND/src/app/application/cadastro` | Telas e services cadastrais legados. |
| `FRONTEND/src/app/application/operacao` | Telas e services operacionais legados. |

## 3. Classificacao

```text
ARQUITETURA CONSOLIDADA COM RESSALVAS
```

A arquitetura de persistencia pode iniciar a implementacao de infraestrutura da primeira vertical conforme DL-0038, DL-0039, DL-0040 e DL-0041.

Ressalvas:

- retencao operacional de idempotencia/outbox permanece bloqueadora de go-live;
- versao efetiva do MySQL deve ser confirmada antes do deploy;
- carga inicial/correlacao entre `CLOCALIZACAOESTOQUE` e `CLOCALDEESTOQUE` deve ser implementada e validada antes de operar em ambiente real;
- telas legadas podem ser reaproveitadas/adaptadas para cadastros e consulta, mas nao para comandos criticos da nova vertical sem novo contrato de API.

## 4. Owner e fronteira

| Item | Owner atual | Owner futuro | Observacao |
|---|---|---|---|
| `CLOCALIZACAOESTOQUE` | Legado PRPA/cadastro de estrutura fisica | Legado | Permanece como fonte cadastral atual e insumo de migracao/correlacao. |
| `CLOCALDEESTOQUE` | Nao existente fisicamente | Novo dominio MES de Estoque | Sera a tabela MES para `LocalDeEstoque`. |
| `LocalizacaoEstoque` | CRUD legado | Legado/adaptador | Nao e AR concorrente do novo dominio. |
| `LocalDeEstoque` | Dominio novo em `App.Domain.Entities.Estoque` | Novo dominio MES de Estoque | AR usado por comandos da primeira vertical. |
| Tela cadastral de localizacao | Frontend legado | Adaptar | Pode continuar editando estrutura legada ate substituicao controlada. |
| Tela de movimentacao da nova vertical | Nao existente | Novo dominio MES de Estoque | Deve usar comandos novos; nao reaproveitar CRUD legado diretamente. |

## 5. Inventario tecnico de componentes

| Componente | Caminho | Responsabilidade | Dependencias | Destino | Justificativa |
|---|---|---|---|---|---|
| Entidade `LocalizacaoEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs` | Cadastro/hierarquia fisica, capacidade, permissoes e bloqueio. | `Almoxarifado`, `AreaEstoque`, `TipoLocalizacao`, self parent. | Adaptar | Continua como legado e fonte de carga/correlacao; nao vira AR novo. |
| Entidade `LocalizacaoEstoqueTreeNode` | `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoqueTreeNode.cs` | DTO/estrutura de arvore para UI. | `LocalizacaoEstoque`, `TipoLocalizacao`. | Reaproveitar | Util para visualizacao hierarquica enquanto a tela legado coexistir. |
| Entidade `AreaEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/AreaEstoque.cs` | Agrupa locais por armazem e tipo, com permissoes por area. | `Almoxarifado`, `TipoAreaEstoque`. | Reaproveitar | Estrutura fisica/cadastral valida como insumo de `WarehouseId`/area. |
| Entidade `Almoxarifado` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Almoxarifado.cs` | Armazem/almoxarifado, controle de localizacao e ativo. | Nenhuma navegacao direta. | Reaproveitar | Pode alimentar `WarehouseId`; permanece cadastro legado. |
| Entidade `TipoLocalizacao` | `BACKEND/PRPA/App.Domain/Entities/PRPA/TipoLocalizacao.cs` | Tipo e nivel hierarquico do local, com permissao de armazenagem. | Nenhuma. | Reaproveitar | Sustenta hierarquia e classificacao estrutural. |
| Entidade `TipoAreaEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/TipoAreaEstoque.cs` | Tipo cadastral de area. | Nenhuma. | Reaproveitar | Cadastro estrutural auxiliar. |
| Mapping `LocalizacaoEstoqueConfig` | `BACKEND/PRPA/App.Infra.Data/Mapping/LocalizacaoEstoqueConfig.cs` | Mapeia `CLOCALIZACAOESTOQUE`, FKs e indices. | EF Core, tabelas `CALMOXARIFADO`, `CAREAESTOQUE`, `CTIPOLOCALIZACAO`. | Reaproveitar | Mantem persistencia legada; nova vertical nao deve alterar esse mapping nesta etapa. |
| Mapping `AreaEstoqueConfig` | `BACKEND/PRPA/App.Infra.Data/Mapping/AreaEstoqueConfig.cs` | Mapeia `CAREAESTOQUE`. | EF Core, almoxarifado, tipo area. | Reaproveitar | Fonte estrutural para carga/correlacao. |
| Mapping `AlmoxarifadoConfig` | `BACKEND/PRPA/App.Infra.Data/Mapping/AlmoxarifadoConfig.cs` | Mapeia `CALMOXARIFADO`. | EF Core. | Reaproveitar | Cadastro de armazem continua valido. |
| Mapping `TipoLocalizacaoConfig` | `BACKEND/PRPA/App.Infra.Data/Mapping/TipoLocalizacaoConfig.cs` | Mapeia `CTIPOLOCALIZACAO` e codigo unico. | EF Core. | Reaproveitar | Define niveis/tipos usados pela tela e por carga. |
| Mapping `TipoAreaEstoqueConfig` | `BACKEND/PRPA/App.Infra.Data/Mapping/TipoAreaEstoqueConfig.cs` | Mapeia `CTIPOAREAESTOQUE`. | EF Core. | Reaproveitar | Cadastro auxiliar. |
| DbSet `CLOCALIZACAOESTOQUE` | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | Expõe tabela legada no DbContext principal. | `LocalizacaoEstoqueConfig`. | Reaproveitar | Necessario para legado; nova vertical tera DbSet proprio em etapa futura. |
| DbSets de estrutura | `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | Expõem `CALMOXARIFADO`, `CAREAESTOQUE`, `CTIPOLOCALIZACAO`, `CTIPOAREAESTOQUE`. | Mappings correspondentes. | Reaproveitar | Fontes de referencia/carga. |
| Repository `LocalizacaoEstoqueRepository` | `BACKEND/PRPA/App.Infra.Data/Repository/LocalizacaoEstoqueRepository.cs` | Repository generico legado. | `BaseRepository`, `ProjetoContext`. | Reaproveitar | Pode continuar servindo CRUD legado; nao atende repository do AR novo. |
| Interface `ILocalizacaoEstoqueRepository` | `BACKEND/PRPA/App.Domain/Interfaces/Repositories/ILocalizacaoEstoqueRepository.cs` | Contrato generico legado. | `IRepository<LocalizacaoEstoque>`. | Reaproveitar | Legado somente; nao substituir `ILocalDeEstoqueRepository`. |
| Service `LocalizacaoEstoqueServices` | `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs` | Consultas por area/armazem, arvore, hierarquia, codigo unico e ciclo. | Repositories de localizacao e tipo. | Adaptar | Logica de hierarquia e arvore pode ser reaproveitada por anti-corruption/carga. |
| Interface `ILocalizacaoEstoqueServices` | `BACKEND/PRPA/App.Domain/Interfaces/Services/ILocalizacaoEstoqueServices.cs` | Contrato de service legado. | `IServices<LocalizacaoEstoque>`. | Reaproveitar | Manter para CRUD legado. |
| Validator `LocalizacaoEstoqueValidator` | `BACKEND/PRPA/App.Service/Validators/LocalizacaoEstoqueValidator.cs` | Valida campos obrigatorios e tamanho. | FluentValidation. | Adaptar | Validacoes cadastrais uteis; faltam invariantes MES. |
| DTOs `LocalizacaoEstoque` | `BACKEND/PRPA/App.Service/DTOs/LocalizacaoEstoque/*.cs` | Contratos CRUD de create/update/read. | AutoMapper/controller. | Adaptar | Uteis para tela legada; comandos novos devem usar contratos proprios. |
| Controller `LocalizacaoEstoqueController` | `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs` | API CRUD e consultas por area/armazem/arvore. | Service legado, UnitOfWork, AutoMapper. | Adaptar | Pode continuar para cadastro legado; endpoints possuem `[AllowAnonymous]` e nao servem a comandos criticos. |
| Controllers `AreaEstoque`, `TipoLocalizacao`, `Almoxarifado` | `BACKEND/PRPA/PRPA/Controllers/*.cs` | CRUDs de cadastros estruturais. | Services legados. | Adaptar | Uteis para cadastros e combos; revisar autorizacao antes de uso operacional. |
| Migrations de localizacao | `BACKEND/PRPA/App.Infra.Data/Migrations/20260621233724_Localizacaoestoque.cs`; `20260702233428_AreaEstoqueLocalizacaoEstoque.cs`; `20260703152406_AddAreaEstoqueAndTipoLocalizacao.cs`; `20260706121735_RefatorarEIndicesLocalizacaoEstoque.cs` | Criam/refatoram `CLOCALIZACAOESTOQUE`, area e tipo. | EF Core/Pomelo/MySQL. | Reaproveitar | Historico fisico legado; nao criar nova migration nesta tarefa. |
| Campos antigos `rua`, `coluna`, `nivel`, `posicao` | `20260621233724_Localizacaoestoque.cs`; removidos em `20260706121735_RefatorarEIndicesLocalizacaoEstoque.cs` | Estrutura fixa anterior. | Migration historica. | Descontinuar | Foram removidos; nao devem voltar como fonte primaria. |
| Entidades operacionais com localizacao | `MovimentoEstoque`, `TransferenciaEstoqueItem`, `SaldoEstoque`, `ReservaEstoque`, `BloqueioEstoque`, `InventarioEstoque`, `AjusteEstoqueItem`, `RecebimentoEstoqueItem` em `BACKEND/PRPA/App.Domain/Entities/PRPA` | Referenciam localizacao legado em operacoes/saldos. | `LocalizacaoEstoque`, produto, lote, unidade, almoxarifado. | Migrar | Podem alimentar reconciliacao/read models; nao sao fonte primaria do novo dominio. |
| Mappings operacionais com localizacao | `BACKEND/PRPA/App.Infra.Data/Mapping/*Estoque*Config.cs` | Persistem operacoes/saldos legados com FK para localizacao. | EF Core. | Migrar | Mantem historico/legado; nova vertical nao deve escrever neles inicialmente. |
| Frontend model `LocalizacaoEstoque` | `FRONTEND/src/app/application/cadastro/localizacaoestoque/models/localizacaoestoque.interface.ts` | ViewModel da tela legado. | Campos camel/pascal/minusculos normalizados pelo service. | Adaptar | Pode alimentar UI cadastral; nao equivale ao contrato novo de `LocalDeEstoque`. |
| Frontend service `LocalizacaoEstoqueService` | `FRONTEND/src/app/application/cadastro/localizacaoestoque/services/localizacaoestoque.service.ts` | Consome `api/localizacao-estoque`, normaliza payload e arvore. | HttpClient, environment. | Adaptar | Reaproveitar para cadastro/consulta legado; nova vertical deve ter service proprio depois. |
| Frontend `CadlocalizacaoestoqueComponent` | `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque/cadlocalizacaoestoque.component.ts/html/scss` | Cadastro e mapa hierarquico de locais, com filtros, arvore, capacidade, permissoes e bloqueio. | Services de almoxarifado, area, tipo, unidade e localizacao. | Adaptar | Boa base UX de cadastro; status Livre/Ocupado e derivado/provisorio. |
| Frontend `ListlocalizacaoestoqueComponent` | `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/*` | Lista/entrada para tela de localizacao. | `LocalizacaoEstoqueService`. | Adaptar | Atualmente renderiza o componente de cadastro; pode permanecer como rota legada. |
| Frontend rota/modulo de localizacao | `FRONTEND/src/app/application/cadastro/localizacaoestoque/*.ts` | Lazy module e rotas de cadastro. | Angular Router, SharedModule, PrimeNG. | Reaproveitar | Mantem tela cadastral existente. |
| Rotas de cadastro | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | Expõe `listalmoxarifado`, `listareaestoque`, `listtipolocalizacao`, `listlocalizacaoestoque`. | Angular lazy loading. | Reaproveitar | Rotas estruturais existentes. |
| Rotas operacionais | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Entrada, saida, transferencia, reserva, bloqueio, inventario e ajuste. | Angular lazy loading. | Adaptar | Fluxos legados continuam, mas nova vertical precisa de UX propria. |
| Entrada/Saida de estoque | `FRONTEND/src/app/application/operacao/entradaestoque/**`; `saidaestoque/**` | Criam `MovimentoEstoque` diretamente por produto/lote/local. | Movimento, localizacao, almoxarifado, produto, lote, unidade. | Substituir | Nao operam por UL, idempotencia, eventos ou confirmacao nova. |
| Transferencia estoque | `FRONTEND/src/app/application/operacao/transferenciaestoque/**` | Cria cabecalho e itens por chamadas separadas. | Transferencia, localizacao, produto, lote, unidade. | Substituir | Nao garante atomicidade do processo novo e possui TODO sobre movimentos/saldo. |
| Reserva/Bloqueio/Inventario/Ajuste | `FRONTEND/src/app/application/operacao/*estoque/**` | Operacoes legadas por produto/lote/local. | Localizacao, saldo, parametros, almoxarifado. | Migrar | Podem virar fluxos futuros/projecoes, fora da primeira vertical. |
| Procedures/views/triggers | Busca em `BACKEND/PRPA/App.Infra.Data` | Nao identificados para Localizacao/Local de Estoque. | Nao identificado. | Descontinuar | Nao ha componente encontrado para reaproveitar. |
| Integracoes/importacoes/exportacoes | Busca em backend/frontend | Nao identificadas para Localizacao/Local de Estoque. | Nao identificado. | Descontinuar | Nao ha integracao localizada no escopo analisado. |

## 6. Analise funcional da tela existente de Locais de Estoque

| Funcionalidade | Implementacao atual | Novo dominio | Acao | Justificativa |
|---|---|---|---|---|
| Selecionar armazem | Combo de `Almoxarifado`. | `WarehouseId`/escopo do local. | Manter | Estrutura de armazem e relevante para `LocalDeEstoque`. |
| Selecionar area | Combo filtrado por armazem. | Area estrutural opcional/referencial. | Manter | Ajuda a segmentar hierarquia e unicidade. |
| Criar local raiz | `novoLocalRaiz()` com tipo sugerido. | Criacao estrutural de local. | Adaptar | Continuar no legado; no novo dominio, criar por fluxo autorizado. |
| Adicionar abaixo | Define `localizacaoPaiId`. | Hierarquia do local. | Adaptar | Reutilizavel para estrutura, mas deve alimentar carga/correlacao. |
| Arvore de localizacoes | Montada em frontend e backend. | Consulta estrutural/read model. | Manter | Boa experiencia de navegacao. |
| Tipo de localizacao | `TipoLocalizacao` com nivel e armazenagem. | Classificacao estrutural. | Manter | Evita campos fixos rua/corredor/coluna/nivel/posicao. |
| Sugestao de proximo tipo | Frontend sugere proximo nivel. | Auxilio UX, nao invariante final. | Adaptar | Backend ja valida hierarquia; sugestao permanece assistiva. |
| Codigo e nome | Campos obrigatorios. | Identidade operacional e descricao. | Manter | `CodigoLocalDeEstoque` deve derivar de `codlocalizacao`. |
| Descricao | Campo opcional. | Atributo informativo. | Manter | Sem conflito. |
| Capacidade/unidade | Campos opcionais. | Capacidade estrutural quando configurada. | Adaptar | Capacidade ausente nao bloqueia primeira slice. |
| Permite entrada/saida/producao | Booleans editaveis. | Restricoes operacionais/derivadas. | Adaptar | Devem influenciar elegibilidade, mas nao substituir comandos. |
| Bloqueada | Boolean editavel. | Estado operacional de bloqueio do local. | Adaptar | Pode alimentar status `Bloqueado` em `CLOCALDEESTOQUE`. |
| Livre/Ocupado | Calculado na UI por `status` se vier da API, senao inferido como livre/estrutural/bloqueado. | Ocupacao deve ser projecao calculada. | Redesenhar | Nao deve ser persistido como estado do local. |
| Deletar local | `DELETE api/localizacao-estoque/{id}`. | Ciclo de vida controlado; historico preservado. | Redesenhar | Exclusao fisica e perigosa para historico. Preferir desativacao futura. |
| `AllowAnonymous` | Endpoints legados anonimos. | Comandos/consultas protegidos por escopo. | Redesenhar | Nao usar em APIs novas da vertical. |
| Entrada/saida/transferencia por localizacao | Telas operacionais usam locais filtrados. | Movimentacao por UL com criar/confirmar. | Redesenhar | Fluxos atuais nao atendem UL, idempotencia, outbox e reserva ativa. |

## 7. Modelo conceitual consolidado

### 7.1 Estrutura fisica

Pertencem ao dominio estrutural/cadastral:

- Armazem/Almoxarifado;
- Area de Estoque;
- Tipo de Localizacao;
- Hierarquia por `localizacaopaiid`;
- Codigo, nome e descricao;
- Capacidade configurada e unidade de capacidade.

Rua, corredor, coluna, nivel e posicao nao existem como campos atuais no modelo final da tabela: os campos fixos `rua`, `coluna`, `nivel` e `posicao` foram removidos pela migration `20260706121735_RefatorarEIndicesLocalizacaoEstoque.cs`. Quando esses conceitos forem necessarios, devem ser representados por tipos/niveis de localizacao ou por nova decisao.

### 7.2 Estado operacional

Pertencem ao dominio operacional:

- Bloqueado;
- Desativado/ativo quando existir status explicito;
- Em uso por uma UL;
- Em movimentacao;
- Ocupacao atual.

`Bloqueado` pode ser persistido como status/flag do local. `Livre/Ocupado` deve ser calculado por projecao derivada de `UnidadeLogistica.LocalAtualId`, eventos confirmados e, quando necessario, reconciliacao com legado.

### 7.3 Permissoes

`permiteentrada`, `permitesaida` e `permiteproducao` sao restricoes/elegibilidades operacionais configuradas no cadastro estrutural. Elas devem ser lidas pela nova vertical como insumo, mas comandos criticos continuam dependendo de autorizacao por ator/escopo e invariantes de dominio.

### 7.4 Persistir versus derivar

| Conceito | Persistir | Derivar/calcular | Observacao |
|---|---|---|---|
| Codigo | Sim | Nao | Identidade operacional. |
| Nome/descricao | Sim | Nao | Atributos cadastrais. |
| Armazem | Sim | Pode derivar de legado na carga | `WarehouseId` obrigatorio no novo modelo. |
| Area | Sim/opcional | Pode derivar de legado | Referencia estrutural. |
| Hierarquia/local pai | Sim | Caminho completo derivado | Caminho textual nao deve ser fonte primaria. |
| Capacidade | Sim quando configurada | Ocupacao percentual calculada | Ausencia nao bloqueia primeira slice. |
| Bloqueio | Sim | Status do novo local pode derivar da flag legado | Bloqueio impede destino. |
| Entrada/saida/producao | Sim como configuracao | Elegibilidade de comando deriva dessas flags | Nao substitui autorizacao. |
| Livre/Ocupado | Nao | Sim | Projecao calculada. |

## 8. Estrategia de transicao

| Criterio | A - Substituicao imediata | B - Congelamento + nova implementacao paralela + migracao + substituicao | C - Refatoracao gradual do existente |
|---|---|---|---|
| Risco operacional | Alto | Medio | Alto |
| Preservacao do legado | Baixa | Alta | Media |
| Aderencia a AS-0004/AS-0007 | Media | Alta | Media |
| Complexidade inicial | Media | Alta | Media |
| Risco de contaminar novo dominio | Medio | Baixo | Alto |
| Necessidade de migracao | Alta e imediata | Controlada por etapas | Difusa |
| Reversibilidade | Baixa | Alta | Media |
| Impacto nas telas atuais | Alto | Medio | Medio/alto |
| Clareza de ownership | Alta, mas disruptiva | Alta | Baixa |

Estrategia escolhida: **Alternativa B**.

Fases:

1. Congelar evolucao semantica do legado de Localizacao de Estoque, mantendo apenas correcao operacional necessaria.
2. Criar `CLOCALDEESTOQUE` em etapa futura de infraestrutura, sem FK fisica obrigatoria para `CLOCALIZACAOESTOQUE`.
3. Executar carga inicial a partir de `CLOCALIZACAOESTOQUE`, `CAREAESTOQUE`, `CALMOXARIFADO` e `CTIPOLOCALIZACAO`.
4. Manter correlacao opcional `LegacyLocalizacaoEstoqueId` ou equivalente documental aprovado no contrato de persistencia.
5. Operar a primeira vertical somente sobre `CLOCALDEESTOQUE`, `CUNIDADELOGISTICA`, `CMOVIMENTACAODEESTOQUE`, reserva, idempotencia e outbox.
6. Reconciliar consultas e divergencias com legado sem sobrescrita automatica.
7. Substituir telas/fluxos legados apenas depois de equivalencia funcional, autorizacao, historico e decisao humana.

## 9. Fonte da verdade

| Informacao | Fonte atual | Fonte futura | Migracao |
|---|---|---|---|
| Codigo | `CLOCALIZACAOESTOQUE.codlocalizacao` | `CLOCALDEESTOQUE.Codigo` | Carga inicial e correlacao. |
| Nome | `CLOCALIZACAOESTOQUE.nome` | `CLOCALDEESTOQUE.Nome` ou atributo equivalente se aprovado | Carga inicial. |
| Hierarquia | `CLOCALIZACAOESTOQUE.localizacaopaiid` + `CTIPOLOCALIZACAO.nivelhierarquico` | `CLOCALDEESTOQUE`/read model estrutural, se aprovado | Migrar ids correlacionados; caminho textual derivado. |
| Area | `CLOCALIZACAOESTOQUE.areaestoqueid` | Referencia/campo em `CLOCALDEESTOQUE` quando necessario | Carga inicial. |
| Armazem | `CLOCALIZACAOESTOQUE.almoxarifadoid`/`CALMOXARIFADO` | `WarehouseId` em `CLOCALDEESTOQUE` | Carga inicial; `WarehouseId` nao deve ficar nulo. |
| Capacidade | `CLOCALIZACAOESTOQUE.capacidade` e `unidadecapacidadeid` | Campos de capacidade em `CLOCALDEESTOQUE`, se mantidos | Carga inicial; ausencia nao bloqueia. |
| Tipo | `CLOCALIZACAOESTOQUE.tipolocalizacaoid`/`CTIPOLOCALIZACAO` | Tipo estrutural ou snapshot no novo local | Carga inicial/referencia. |
| Bloqueio | `CLOCALIZACAOESTOQUE.bloqueada` | `LocalDeEstoqueStatus.Bloqueado` | Converter flag para status. |
| Entrada | `CLOCALIZACAOESTOQUE.permiteentrada` e possivelmente area | Restricao/elegibilidade lida pelo dominio | Migrar como configuracao. |
| Saida | `CLOCALIZACAOESTOQUE.permitesaida` e possivelmente area | Restricao/elegibilidade lida pelo dominio | Migrar como configuracao. |
| Producao | `CLOCALIZACAOESTOQUE.permiteproducao` e possivelmente area | Restricao/elegibilidade futura | Migrar como configuracao; fora da primeira movimentacao se nao usado. |
| Livre/Ocupado | UI tenta inferir por `status`, saldos/operacoes podem indicar indiretamente | Projecao calculada a partir de ULs e eventos confirmados | Nao migrar como coluna autoritativa. Recalcular. |
| Local Pai | `CLOCALIZACAOESTOQUE.localizacaopaiid` | Correlacao de pai no novo modelo se aprovada | Migrar mantendo mapa legado->novo. |

## 10. Politica de dados

| Estrategia | Resultado |
|---|---|
| Cadastro independente | Rejeitada como estrategia inicial, pois duplicaria estrutura e exigiria recadastro manual. |
| Carga inicial | Aprovada para alimentar `CLOCALDEESTOQUE` a partir do legado. |
| Sincronizacao continua | Nao aprovada para a primeira implementacao; pode ser futura se o legado continuar recebendo edicoes. |
| Importacao pontual | Permitida como mecanismo tecnico da carga inicial, sem definir ferramenta/script nesta etapa. |
| Correlacao | Obrigatoria como ponte de rastreabilidade entre legado e novo modelo. |

Quem grava:

- antes da virada: telas/APIs legadas gravam `CLOCALIZACAOESTOQUE`;
- na carga inicial: rotina futura de migracao/importacao grava `CLOCALDEESTOQUE`;
- apos ativacao da nova vertical: comandos/infraestrutura MES gravam `CLOCALDEESTOQUE` apenas conforme contratos aprovados;
- a nova vertical nao escreve em `CLOCALIZACAOESTOQUE`.

Quem le:

- legado continua lendo `CLOCALIZACAOESTOQUE`;
- nova vertical le `CLOCALDEESTOQUE`;
- anti-corruption/carga pode ler legado para correlacao/reconciliacao.

## 11. Ocupacao

Decisao: **Livre/Ocupado sera calculado, nao persistido como estado autoritativo de `LocalDeEstoque`.**

Fonte da informacao:

- estado transacional de `UnidadeLogistica.LocalAtualId`;
- eventos confirmados de movimentacao;
- reserva ativa quando a UL estiver em processo de movimentacao;
- reconciliacao com saldos/movimentos legados apenas como apoio, nao como autoridade.

Justificativa:

- ocupacao muda por eventos operacionais, nao por edicao cadastral do local;
- persistir Livre/Ocupado no local criaria risco de divergencia com ULs e historico;
- capacidade e bloqueio pertencem ao local, mas ocupacao pertence ao estado operacional/projecao.

## 12. Ciclo de vida da reserva transacional

| Fase | Regra |
|---|---|
| Criacao | Ao criar `MovimentacaoDeEstoque` em estado `Solicitada`, inserir reserva em `CUNIDADELOGISTICAMOVEMENTRESERVATION` na mesma transacao. |
| Concorrencia | Constraint unica `WarehouseId, UnidadeLogisticaId` rejeita segunda movimentacao ativa da mesma UL no mesmo armazem. |
| Rollback | Se qualquer parte da transacao falhar, a reserva nao deve permanecer gravada. |
| Confirmacao | Ao confirmar a movimentacao, atualizar UL e movimentacao, gravar outbox/idempotencia e liberar a reserva na mesma transacao. |
| Remocao | Remocao normal ocorre por confirmacao; fluxos futuros de cancelamento/rejeicao tambem deverao liberar reserva. |
| Recovery | Registros presos exigem rotina operacional futura, com status/data/tentativas suficientes para diagnostico. |
| Idempotencia | Replay de criacao/confirmacao nao pode criar reserva duplicada nem liberar reserva de outra movimentacao. |

## 13. Identificadores

| Identificador | Estrategia definitiva |
|---|---|
| `MovimentacaoDeEstoqueId` | `int`, alocado antes da criacao do agregado/evento por infraestrutura/aplicacao. |
| `UnidadeLogisticaId` | `int`, existente ao carregar a UL; novas ULs exigem alocacao antes de eventos. |
| `LocalDeEstoqueId` | `int`, gerado para `CLOCALDEESTOQUE`; correlacao opcional com legado nao substitui identidade nova. |
| `ActorId` | `int`, obtido da identidade/autenticacao; sem FK direta aprovada nesta etapa. |
| `CorrelationId` | GUID em `char(36)`, fornecido pelo chamador/orquestrador. |
| `CausationId` | GUID em `char(36)`, fornecido pelo chamador/orquestrador; pode receber correlation quando nao houver causa externa. |
| `EventId` | GUID em `char(36)`, gerado pelo dominio e unico na outbox. |

Inconsistencia registrada: o dominio e a aplicacao ja exigem `MovimentacaoDeEstoqueId` antes de criar eventos. Portanto, a infraestrutura futura nao pode depender de auto-incremento pos-insert para esse AR sem mecanismo previo de alocacao.

## 14. Lacunas residuais

| Lacuna | Classificacao | Observacao |
|---|---|---|
| Politica final de retencao de idempotencia/outbox | Bloqueadora do go-live | Nao bloqueia implementacao da infraestrutura. |
| Job de limpeza/recovery/dead-letter | Bloqueadora do go-live | Necessario antes de producao. |
| Versao efetiva do MySQL | Nao bloqueadora | A reserva evita dependencia de indice filtrado/coluna gerada, mas deploy deve validar ambiente. |
| Carga inicial concreta | Bloqueadora da operacao real | Nao bloqueia criar mappings/migrations; bloqueia usar dados reais sem plano executado. |
| Autorizacao granular dos novos endpoints | Bloqueadora de API/go-live | Fora da tarefa atual, mas obrigatoria antes de expor comandos. |
| UX nova de movimentacao por UL | Nao bloqueadora da infraestrutura | Deve ser desenhada antes de frontend novo. |
| Sincronizacao continua legado->novo | Nao bloqueadora | Nao definida para primeira implementacao; se legado continuar mutavel, vira decisao futura. |

## 15. Conclusao

A implementacao legada de Locais de Estoque deve ser preservada como cadastro estrutural e fonte de migracao/correlacao, mas nao deve ser promovida a owner da primeira vertical MES.

A transicao oficial e paralela e controlada: legado congelado semanticamente, nova tabela `CLOCALDEESTOQUE`, carga inicial, correlacao, operacao da nova vertical em tabelas MES e substituicao gradual dos fluxos legados somente apos cobertura funcional e decisao humana.