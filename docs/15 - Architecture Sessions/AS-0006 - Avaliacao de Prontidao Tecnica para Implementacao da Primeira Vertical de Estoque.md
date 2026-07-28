# AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque

## 1. Identificacao

| Campo | Valor |
|---|---|
| Codigo | AS-0006 |
| Titulo | Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque |
| Status | Concluida |
| Data da sessao | 2026-07-24 |
| Data da ultima revisao | 2026-07-24 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Avaliacao de prontidao tecnica apos AS-0005 e DL-0030 a DL-0032. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- Codex, no papel de auditor tecnico documental.

## 2. Contexto

A AS-0005 definiu a primeira vertical funcional do dominio de Estoque:

```text
Local de Estoque
-> Unidade Logistica
-> Movimentacao
-> Confirmacao
-> Consulta
-> Historico
```

O DL-0032 determinou que a retomada da codificacao depende da Definition of Ready registrada na AS-0005 e que a implementacao nao deve iniciar enquanto houver pendencias bloqueadoras em escopo, invariantes, comandos, eventos, autorizacao, persistencia, impacto no legado, migracao e testes.

Esta avaliacao verifica a prontidao tecnica para retomar a codificacao. Ela nao cria decisoes arquiteturais novas, nao altera backend ou frontend e nao substitui a AS-0005.

## 3. Objetivo

Determinar se o projeto esta pronto para iniciar a implementacao da primeira vertical funcional de Estoque conforme AS-0004, AS-0005 e DL-0030 a DL-0032.

Classificacoes possiveis:

- `READY`: codificacao pode iniciar sem bloqueadores conhecidos.
- `READY WITH CONDITIONS`: codificacao pode iniciar apenas em recorte tecnico controlado, com condicoes explicitas.
- `NOT READY`: codificacao da vertical nao deve iniciar ate resolver bloqueadores.

## 4. Fontes analisadas

### 4.1 Project Book

- `MES-ProjectBook/docs/00 - IA/CONVENCOES.md`
- `MES-ProjectBook/docs/00 - IA/AI_CONTEXT.md`
- `MES-ProjectBook/docs/00 - IA/ESTADO_ATUAL.md`
- `MES-ProjectBook/docs/00 - IA/CHANGELOG.md`
- `MES-ProjectBook/docs/11 - Roadmap/Roadmap Geral.md`
- `MES-ProjectBook/docs/12 - Decision Log/README.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0023 - Unidade Logistica como Agregado Fisico.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0024 - Local de Estoque como Aggregate Root.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0030 - Primeira Vertical Funcional do Dominio de Estoque.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0032 - Definition of Ready para Retomada da Codificacao.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `MES-ProjectBook/docs/20 - Glossario Arquitetural/Glossario Arquitetural do MES.md`

### 4.2 Backend

- `BACKEND/PRPA/App.Domain/Entities/BaseEntity.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/TransferenciaEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/TransferenciaEstoqueItem.cs`
- `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/LocalizacaoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/MovimentoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/SaldoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/TransferenciaEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/UnitOfWork.cs`
- `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs`
- `BACKEND/PRPA/App.Service/Services/MovimentoEstoqueServices.cs`
- `BACKEND/PRPA/App.Service/Services/SaldoEstoqueServices.cs`
- `BACKEND/PRPA/App.Service/Services/TransferenciaEstoqueServices.cs`
- `BACKEND/PRPA/App.Service/Validators/LocalizacaoEstoqueValidator.cs`
- `BACKEND/PRPA/App.Service/Validators/MovimentoEstoqueValidator.cs`
- `BACKEND/PRPA/App.Service/Validators/SaldoEstoqueValidator.cs`
- `BACKEND/PRPA/App.Service/Validators/TransferenciaEstoqueValidator.cs`
- `BACKEND/PRPA/PRPA/Controllers/BaseApiController.cs`
- `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/PRPA/Controllers/SaldoEstoqueController.cs`
- `BACKEND/PRPA/PRPA/Controllers/TransferenciaEstoqueController.cs`

### 4.3 Frontend

- `FRONTEND/src/app/application/operacao/operacao-routing.module.ts`
- `FRONTEND/src/app/application/operacao/models/movimentoestoque.interface.ts`
- `FRONTEND/src/app/application/operacao/services/movimentoestoque.service.ts`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/saidaestoque/components/saidaestoque/saidaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/transferenciaestoque/components/transferenciaestoque/transferenciaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/transferenciaestoque/services/transferenciaestoque.service.ts`
- `FRONTEND/src/app/application/operacao/transferenciaestoque/services/transferenciaestoqueitem.service.ts`
- `FRONTEND/src/app/application/operacao/reservaestoque/components/reservaestoque/reservaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/bloqueioestoque/components/bloqueioestoque/bloqueioestoque.component.ts`
- `FRONTEND/src/app/application/operacao/ajusteestoque/components/ajusteestoque/ajusteestoque.component.ts`
- `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque/cadlocalizacaoestoque.component.ts`
- `FRONTEND/src/app/application/cadastro/localizacaoestoque/services/localizacaoestoque.service.ts`

## 5. Classificacao final

**Classificacao: `NOT READY`.**

A implementacao da primeira vertical funcional de Estoque nao deve iniciar ainda, porque ha bloqueadores diretos contra o DL-0032:

- `UnidadeLogistica` nao foi identificada como entidade, agregado, DTO, controller, service ou tela no codigo atual.
- `MovimentacaoDeEstoque` nao foi identificada como Aggregate Root com ciclo de vida, comandos, eventos, idempotencia e versionamento.
- `MovimentoEstoque` existente se comporta como registro CRUD de movimento, nao como processo operacional com inicio, transito e confirmacao.
- `SaldoEstoque` existe como estado persistido e manipulavel por CRUD, mas a arquitetura exige saldo como projecao derivada de fatos confirmados.
- Endpoints de estoque analisados usam `[AllowAnonymous]`, apesar de a AS-0004/AS-0005 exigirem autorizacao operacional e autoridade de dominio.
- Eventos, envelope, outbox/inbox, idempotencia, correlacao, causacao e reprocessamento nao foram identificados no codigo de estoque.
- Controle de concorrencia otimista existe apenas como tratamento generico no `UnitOfWork`, sem evidencias de token/versionamento nos agregados de estoque.
- O frontend possui telas operacionais legadas, mas elas dependem de TODOs que pedem confirmacao sobre atualizacao de saldo e geracao de movimentos.
- Nao foram identificados testes especificos da vertical AS-0005.

## 6. Matriz da Definition of Ready

| Criterio da AS-0005/DL-0032 | Evidencia analisada | Status | Observacao |
|---|---|---|---|
| Escopo da vertical definido | AS-0005 secoes 2, 5, 11 e 39; DL-0030 | Coberto | Escopo conceitual definido. |
| Linguagem ubiqua definida | AS-0005 secoes 12 e 18; AS-0004 secoes 9 a 12 | Coberto | Termos centrais definidos. |
| Aggregate Roots definidos | AS-0005 secao 18 | Coberto conceitualmente | Codigo ainda nao possui `UnidadeLogistica` nem `MovimentacaoDeEstoque`. |
| Invariantes definidas | AS-0005 secao 20 | Coberto conceitualmente | Nao implementadas nos validators/services analisados. |
| Comandos definidos | AS-0005 secao 23 | Coberto conceitualmente | Nao ha handlers/contratos de comando no codigo. |
| Eventos definidos | AS-0005 secao 24 | Coberto conceitualmente | Nao ha infraestrutura/eventos no codigo de estoque. |
| Estados separados de eventos | AS-0004 secoes 28 a 30; AS-0005 secoes 22 a 24 | Coberto conceitualmente | Codigo legado usa status parametrico sem ciclo equivalente. |
| Contratos de aplicacao definidos | AS-0005 secao 30 | Parcial | Contratos sao conceituais; payloads tecnicos nao definidos. |
| Autorizacao operacional definida | AS-0005 secoes 27 e 29 | Parcial | Regras conceituais existem; policies/roles/escopos tecnicos nao definidos. |
| Persistencia tecnica definida | AS-0005 secao 33 | Parcial | Persistencia conceitual definida; schema e estrategia tecnica pendentes. |
| Impacto do legado classificado | AS-0005 secao 40; DL-0031 | Parcial | Necessita classificacao item a item antes de codificar. |
| Migracao definida | AS-0005 secao 34 | Ausente | Nao ha plano de migracao/schema para UL/movimentacao/projecoes. |
| Slice de implementacao escolhida | AS-0005 secao 37 | Parcial | Sequencia conceitual existe, mas nao ha desenho tecnico executavel. |
| Criterios de aceite tecnicos | AS-0005 secao 38 | Parcial | Aceite conceitual existe; cenarios automatizados nao existem. |
| Testes definidos | AS-0005 secao 36 | Parcial | Tipos de teste definidos, sem suites/casos implementados. |
| Dependencias externas bloqueantes | AS-0005 secoes 31 e 32 | Coberto | Integracoes externas fora do recorte. |
| Bloqueadores classificados | Esta AS-0006 secoes 19 e 20 | Coberto nesta avaliacao | Bloqueadores tecnicos registrados abaixo. |

## 7. Inventario tecnico backend

| Item | Caminho | Papel atual identificado | Aderencia a AS-0005 |
|---|---|---|---|
| `LocalizacaoEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs` | Cadastro/hierarquia fisica com capacidade, permissao de entrada/saida/producao e bloqueio. | Reutilizavel com adaptacao como insumo para `LocalDeEstoque`; nao equivale automaticamente ao AR aprovado. |
| `LocalizacaoEstoqueServices` | `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs` | Consulta por area/almoxarifado, arvore, validacao de codigo unico e ciclo hierarquico. | Parcialmente aderente a invariantes de hierarquia de local. |
| `MovimentoEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs` | Registro de movimento com produto, lote, origem, destino, quantidade, documento, data e estorno. | Divergente como substituto de `MovimentacaoDeEstoque`; falta ciclo de vida, comandos, confirmacoes e idempotencia. |
| `MovimentoEstoqueController` | `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs` | CRUD anonimo de movimento. | Nao aderente para a vertical aprovada. |
| `SaldoEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs` | Estado persistido de saldo fisico, reservado, bloqueado e disponivel. | Pode ser insumo de leitura, mas nao deve ser fonte primaria de alteracao. |
| `TransferenciaEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/TransferenciaEstoque.cs` | Cabecalho de transferencia com status, origem/destino, datas e usuarios. | Pode inspirar fluxo, mas nao representa `MovimentacaoDeEstoque` aprovada. |
| `TransferenciaEstoqueItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA/TransferenciaEstoqueItem.cs` | Itens de transferencia por produto/lote/local/quantidade. | Pode ser insumo, mas opera por produto/lote/local, nao por UL. |
| `TransferenciaEstoqueController` | `BACKEND/PRPA/PRPA/Controllers/TransferenciaEstoqueController.cs` | CRUD anonimo de transferencia. | Nao atende comandos e transicoes da AS-0005. |
| `UnitOfWork` | `BACKEND/PRPA/App.Infra.Data/Persistence/UnitOfWork.cs` | Transacao local e tratamento generico de concorrencia EF. | Reutilizavel com adaptacao; nao substitui versionamento/idempotencia de agregado. |
| Migrations de estoque | `BACKEND/PRPA/App.Infra.Data/Migrations` | Tabelas legadas de estoque ja modeladas. | Precisam ser avaliadas antes de nova migration; nao definem schema AS-0005. |

## 8. Arquitetura aprovada versus codigo encontrado

| Tema aprovado | Documento/secao | Codigo encontrado | Avaliacao |
|---|---|---|---|
| Estoque e dominio, nao AR unico | AS-0004 secao 8; DL-0022 | Codigo possui entidades CRUD por tabela. | Nao ha agregado `Estoque`, o que evita erro direto, mas tambem nao ha dominio de estoque modelado conforme aprovado. |
| Saldo como projecao | AS-0004 secoes 10 e 32; DL-0022 | `SaldoEstoque` e controller CRUD. | Risco alto de saldo ser tratado como fonte primaria. |
| UL como principal agregado fisico | AS-0004 secao 17; DL-0023 | Nao identificado `UnidadeLogistica`. | Bloqueador. |
| LocalDeEstoque como AR | AS-0004 secao 18; DL-0024 | `LocalizacaoEstoque` cadastral/hierarquica. | Parcial; exige decisao tecnica de mapeamento sem transformar sinonimo indevido. |
| Movimentacao como processo | AS-0004 secao 21; DL-0025 | `MovimentoEstoque` e `TransferenciaEstoque` CRUD. | Bloqueador para a vertical. |
| Eventos e envelope comum | AS-0004 secoes 28 a 30; AS-0005 secao 24 | Nao identificado no codigo de estoque. | Bloqueador. |
| Idempotencia e concorrencia | AS-0004 secao 31; AS-0005 secao 28 | Tratamento generico no `UnitOfWork`; sem chave idempotente nem versionamento de agregado. | Bloqueador. |
| Autorizacao e autoridade de dominio | AS-0004 secoes 26 e 27; AS-0005 secao 29 | Controllers de estoque com `[AllowAnonymous]`. | Bloqueador. |

## 9. Agregados

### 9.1 `LocalDeEstoque`

Responsabilidade aprovada: governar endereco ou espaco fisico do estoque, hierarquia, capacidade, restricoes, compatibilidade e estado operacional.

Evidencia conceitual: AS-0004 secao 18; AS-0005 secao 18; DL-0024.

Evidencia no codigo: `LocalizacaoEstoque` possui dados proximos, mas a AS-0004 separa `LocalDeEstoque` de `LocalizacaoEstoque`. O codigo atual nao comprova que `LocalizacaoEstoque` seja o AR final.

Invariantes parcialmente suportadas: hierarquia sem ciclo, codigo unico por area, bloqueio e capacidade existem como dados/validacoes.

Pendencias: definir se sera reutilizado, adaptado ou substituido; definir contrato com UL e movimentacao.

### 9.2 `UnidadeLogistica`

Responsabilidade aprovada: representar objeto fisico identificavel, manipulavel e rastreavel.

Evidencia conceitual: AS-0004 secao 17; AS-0005 secao 18; DL-0023.

Evidencia no codigo: nao identificada entidade, controller, service, DTO, tela ou migration especifica.

Invariantes pendentes: identidade de UL, estado existencial, localizacao atual, conteudo, bloqueio operacional, impossibilidade de duas movimentacoes ativas simultaneas.

Pendencias: bloqueador principal da vertical.

### 9.3 `MovimentacaoDeEstoque`

Responsabilidade aprovada: conduzir processo operacional com inicio, retirada, transito, confirmacao, conclusao, excecoes, eventos e auditoria.

Evidencia conceitual: AS-0004 secao 21; AS-0005 secoes 18, 22, 23 e 24; DL-0025.

Evidencia no codigo: `MovimentoEstoque` e `TransferenciaEstoque` existem, mas nao possuem o ciclo aprovado nem operam sobre UL como agregado fisico.

Invariantes pendentes: bloqueio de movimentacao simultanea por UL, transicoes validas, confirmacao idempotente, localizacao final somente apos chegada confirmada.

Pendencias: definir novo AR ou adaptacao profunda de legado.

## 10. Fluxo transacional esperado

Fluxo aprovado pela AS-0005:

```text
PlanejarMovimentacao
-> MovimentacaoPlanejada
-> IniciarMovimentacao
-> MovimentacaoIniciada
-> ConfirmarRetirada
-> UnidadeLogisticaRetirada
-> IniciarTransito
-> TransitoDaMovimentacaoIniciado
-> ConfirmarChegada
-> MovimentacaoConcluida
```

No codigo atual, foram identificados CRUDs de `MovimentoEstoque`, `TransferenciaEstoque` e `TransferenciaEstoqueItem`. A tela de transferencia cria cabecalho e depois itens por chamadas HTTP separadas. Isso nao garante atomicidade da operacao completa, nao confirma retirada/chegada e nao emite eventos de dominio.

Conclusao: o fluxo transacional da vertical nao esta tecnicamente pronto.

## 11. Contratos conceituais de aplicacao

A AS-0005 define comandos, eventos e consumidores conceituais, mas nao define payloads tecnicos, endpoints, handlers, DTOs finais ou contratos de erro.

Contratos existentes no codigo:

- DTOs CRUD de `MovimentoEstoque`.
- DTOs CRUD de `TransferenciaEstoque`.
- DTOs CRUD de `TransferenciaEstoqueItem`.
- Interfaces TypeScript correspondentes no frontend.

Conclusao: contratos existentes sao insumos legados, nao contratos da vertical aprovada.

## 12. Persistencia

Persistencia encontrada:

- tabelas/mappings para `CLOCALIZACAOESTOQUE`, `CMOVIMENTOESTOQUE`, `CSALDOESTOQUE`, `CTRANSFERENCIAESTOQUE` e itens relacionados;
- migrations de estoque em `BACKEND/PRPA/App.Infra.Data/Migrations`;
- `ProjetoContext` com DbSets e mappings de entidades de estoque.

Persistencia ausente para a vertical:

- schema de `UnidadeLogistica`;
- schema de `MovimentacaoDeEstoque` com ciclo aprovado;
- armazenamento de eventos ou outbox;
- chaves de idempotencia;
- versionamento de agregado;
- projecoes derivadas reconstruiveis;
- historico imutavel de eventos da vertical.

Conclusao: persistencia nao esta pronta para codificacao da vertical.

## 13. Idempotencia e concorrencia

Evidencia encontrada:

- `UnitOfWork` possui tratamento generico de `DbUpdateConcurrencyException`.
- O Identity possui `ConcurrencyStamp`, mas isso pertence ao contexto de usuarios.

Nao identificado:

- `IdempotencyKey` em comandos de estoque;
- indice unico para idempotencia;
- outbox/inbox;
- deduplicacao por `eventId`;
- token de versao em `UnidadeLogistica` ou `MovimentacaoDeEstoque`;
- protecao de duas movimentacoes ativas simultaneas por UL.

Conclusao: idempotencia e concorrencia sao bloqueadores.

## 14. Eventos

Eventos aprovados para a vertical: AS-0005 secao 24.

Envelope aprovado: AS-0004 secao 29 e AS-0005 secao 24.

Campos obrigatorios do envelope:

- `eventId`
- `eventType`
- `eventVersion`
- `occurredAt`
- `aggregateId`
- `aggregateType`
- `correlationId`
- `causationId`
- `tenantId`
- `plantId`
- `actorId`
- `source`

Nao identificado no codigo:

- classes de evento da vertical;
- publicacao de eventos;
- outbox;
- versionamento de eventos;
- correlacao e causacao;
- idempotencia de consumidores;
- separacao tecnica entre comando e evento.

Conclusao: a arquitetura de eventos esta aprovada conceitualmente, mas nao pronta tecnicamente.

## 15. Seguranca e autorizacao

Evidencia encontrada:

- `BaseApiController` possui `[Authorize]`.
- Controllers de estoque analisados usam `[AllowAnonymous]` nos metodos CRUD.
- Sistema de Identity/JWT e roles existe no projeto.

Lacunas:

- autorizacao por comando operacional;
- escopo por planta/armazem;
- segregacao entre solicitante, executor e aprovador;
- autoridade de dominio separada da permissao administrativa;
- auditoria operacional da vertical.

Conclusao: seguranca e autorizacao sao bloqueadores para codificacao da vertical.

## 16. Testes

Evidencia encontrada:

- existem specs Angular genericos em `FRONTEND/src/app`;
- nao foram identificados testes backend de dominio para Estoque;
- nao foram identificados testes automatizados da vertical AS-0005.

Testes exigidos pela AS-0005 secao 36 ainda pendentes:

- transicoes de estado de `MovimentacaoDeEstoque`;
- invariantes de UL em movimentacao ativa;
- local bloqueado/desativado/capacidade;
- idempotencia de confirmacao;
- concorrencia;
- projecoes de consulta e historico;
- autorizacao por comando;
- testes de contrato backend/frontend.

Conclusao: cobertura de testes nao esta pronta.

## 17. Frontend e experiencia operacional

O frontend possui rotas e telas de operacao de estoque:

- entrada;
- saida;
- transferencia;
- reserva;
- bloqueio;
- inventario;
- ajuste.

Essas telas sao relevantes como insumo, mas nao representam a vertical aprovada porque:

- entrada e saida criam `MovimentoEstoque` diretamente;
- transferencia cria `TransferenciaEstoque` e `TransferenciaEstoqueItem`, nao uma `MovimentacaoDeEstoque` orientada a UL;
- existem TODOs pedindo confirmacao sobre geracao automatica de movimentos e atualizacao de saldo;
- nao ha fluxo guiado por comandos da AS-0005;
- nao ha apresentacao comprovada de movimentacoes abertas, transito, confirmacao e historico imutavel conforme AS-0005.

Conclusao: frontend nao esta pronto para implementar a vertical sem desenho tecnico previo.

## 18. Primeira vertical slice recomendada

Como a classificacao e `NOT READY`, a proxima atividade nao deve ser codificacao funcional da vertical. A primeira slice tecnica recomendada e uma etapa de preparacao, sem implementar regra de negocio final:

1. Classificar cada artefato legado de estoque como `reutilizar`, `adaptar`, `substituir`, `descontinuar` ou `investigar`, conforme DL-0031.
2. Definir desenho tecnico minimo de `UnidadeLogistica`, `LocalDeEstoque` e `MovimentacaoDeEstoque`.
3. Definir persistencia minima, idempotencia, versionamento e eventos da vertical.
4. Definir contratos de comandos e consultas.
5. Definir autorizacao e auditoria operacional.
6. Definir suite minima de testes antes de migrations e endpoints.

Essa slice e preparatoria, nao uma autorizacao para implementar backend ou frontend produtivo.

## 19. Bloqueadores

| Bloqueador | Severidade | Evidencia |
|---|---|---|
| Ausencia de `UnidadeLogistica` no codigo | Alta | Busca em `BACKEND/PRPA` e `FRONTEND/src/app`; AS-0005 secao 18 exige UL. |
| Ausencia de `MovimentacaoDeEstoque` como AR | Alta | Existe `MovimentoEstoque`, mas sem ciclo/comandos/eventos. |
| Saldo persistido com CRUD pode ser usado como fonte primaria | Alta | `SaldoEstoque` e `SaldoEstoqueController`; DL-0022 exige saldo como projecao. |
| Endpoints de estoque anonimos | Alta | Controllers de estoque com `[AllowAnonymous]`; AS-0004 secoes 26 e 27. |
| Idempotencia nao implementada | Alta | Nao identificado `IdempotencyKey` tecnico nem indice de deduplicacao. |
| Eventos/outbox nao implementados | Alta | Nao identificado mecanismo de eventos de dominio em estoque. |
| Concorrencia de UL nao protegida | Alta | Nao ha UL nem controle de movimentacao ativa por UL. |
| Migrations/schema da vertical nao definidos | Alta | AS-0005 secao 33 deixa detalhes tecnicos pendentes. |
| Testes especificos ausentes | Media | Specs frontend genericos; testes de dominio nao identificados. |
| TODOs funcionais em telas de estoque | Media | Transferencia, reserva, bloqueio e ajuste dependem de confirmacao backend. |

## 20. Riscos

- Comecar por CRUD legado e cristalizar uma arquitetura contraria a AS-0004.
- Tratar `MovimentoEstoque` como evento, estado e comando ao mesmo tempo.
- Usar `SaldoEstoque` como bloqueio concorrente unico.
- Criar migrations para entidades incompletas e precisar refazer schema.
- Construir telas antes de definir comandos e confirmacoes.
- Liberar endpoints anonimos para operacoes criticas.
- Gerar historico editavel ou apagavel por `PUT`/`DELETE`.
- Ignorar idempotencia em confirmacoes repetidas por falha de rede.
- Misturar `LocalDeEstoque` e `LocalizacaoEstoque` sem justificativa formal.

## 21. Sequencia recomendada antes da codificacao

1. Aprovar esta avaliacao de prontidao.
2. Classificar legado de estoque conforme DL-0031.
3. Produzir arquitetura tecnica de backend para a vertical.
4. Produzir arquitetura tecnica de frontend para a vertical.
5. Definir contratos de comando, consulta, erro e evento.
6. Definir persistencia, migrations planejadas, versionamento e idempotencia.
7. Definir autorizacao, auditoria e escopos.
8. Definir testes obrigatorios e criterios de aceite executaveis.
9. Somente entao iniciar implementacao.

## 22. Conclusao

A AS-0004 e a AS-0005 fornecem uma base conceitual suficiente para orientar a primeira vertical funcional de Estoque, mas o estado tecnico atual do backend e frontend ainda nao satisfaz a Definition of Ready do DL-0032.

Portanto, a recomendacao formal desta avaliacao e:

**Nao retomar a codificacao funcional da primeira vertical de Estoque neste momento.**

Classificacao final:

```text
NOT READY
```

## 23. Decision Logs relacionados

Esta avaliacao nao cria Decision Log novo, pois nao registra decisao arquitetural aprovada. Ela referencia:

- DL-0022 - Estoque como Dominio e Saldo como Projecao
- DL-0023 - Unidade Logistica como Agregado Fisico
- DL-0024 - Local de Estoque como Aggregate Root
- DL-0025 - Movimentacao de Estoque como Processo Operacional
- DL-0029 - Autoridade de Dominio Auditoria e Eventos
- DL-0030 - Primeira Vertical Funcional do Dominio de Estoque
- DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao
- DL-0032 - Definition of Ready para Retomada da Codificacao

## 24. Alteracoes realizadas nesta sessao

- Criada esta avaliacao documental de prontidao tecnica.
- Atualizados indices e changelog do Project Book.
- Nenhum arquivo de backend foi alterado.
- Nenhum arquivo de frontend foi alterado.
- Nenhuma migration, endpoint, entidade, tela, dependencia, commit ou staging foi criado.
