# AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque

## 1. Identificacao

| Campo | Valor |
|---|---|
| Codigo | AS-0007 |
| Titulo | Arquitetura Tecnica da Primeira Vertical Funcional de Estoque |
| Status | Concluida |
| Data da sessao | 2026-07-24 |
| Data da ultima revisao | 2026-07-24 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Desenho tecnico para remover bloqueadores da AS-0006 e permitir implementacao incremental da primeira vertical de Estoque. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- Codex, no papel de arquiteto tecnico documental.

## 2. Contexto

A AS-0006 classificou a retomada de codificacao da primeira vertical funcional de Estoque como `NOT READY`.

Bloqueadores principais:

- ausencia tecnica de `UnidadeLogistica`;
- ausencia de `MovimentacaoDeEstoque` como Aggregate Root implementavel;
- legado orientado a CRUD de `MovimentoEstoque` e `TransferenciaEstoque`;
- `SaldoEstoque` persistido com risco de ser tratado como fonte primaria;
- endpoints de estoque com `[AllowAnonymous]`;
- ausencia de eventos, idempotencia, concorrencia e testes especificos.

Esta AS-0007 define a arquitetura tecnica minima para a primeira slice implementavel:

```text
Local de Estoque
-> Unidade Logistica
-> Movimentacao
-> Confirmacao
-> Consulta
-> Historico
```

Nenhum codigo, migration, endpoint, dependencia, schema ou refatoracao foi criado nesta sessao.

## 3. Fontes analisadas

### 3.1 Architecture Sessions e Decision Logs

- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0002 - Movimentacoes de Estoque e Operacao Assistida.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0003 - Arquitetura de Integracao Sincronizacao Governanca e Eventos.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0023 - Unidade Logistica como Agregado Fisico.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0024 - Local de Estoque como Aggregate Root.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0030 - Primeira Vertical Funcional do Dominio de Estoque.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0032 - Definition of Ready para Retomada da Codificacao.md`

### 3.2 Documentacao funcional e tecnica

- `MES-ProjectBook/docs/00 - IA/CONVENCOES.md`
- `MES-ProjectBook/docs/00 - IA/AI_CONTEXT.md`
- `MES-ProjectBook/docs/00 - IA/ESTADO_ATUAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Estoque.md`
- `MES-ProjectBook/docs/05 - Estoque/Enderecamento.md`
- `MES-ProjectBook/docs/05 - Estoque/Transferencia.md`
- `MES-ProjectBook/docs/05 - Estoque/Saldos.md`
- `MES-ProjectBook/docs/05 - Estoque/Regras de Negocio.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Entidades/LocalizacaoEstoque.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Entidades/MovimentoEstoque.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Entidades/SaldoEstoque.md`
- `MES-ProjectBook/docs/04 - Cadastros Mestres/LocalizacaoEstoque.md`
- `MES-ProjectBook/docs/20 - Glossario Arquitetural/Glossario Arquitetural do MES.md`

Observacao: alguns documentos funcionais existem, mas nao possuem conteudo alem do arquivo vazio no estado analisado. Nesses casos, a ausencia de conteudo foi tratada como evidencia insuficiente.

### 3.3 Codigo inspecionado

- `BACKEND/PRPA/PRPA.sln`
- `BACKEND/PRPA/PRPA/Program.cs`
- `BACKEND/PRPA/PRPA/Controllers/BaseApiController.cs`
- `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/PRPA/Controllers/SaldoEstoqueController.cs`
- `BACKEND/PRPA/PRPA/Controllers/TransferenciaEstoqueController.cs`
- `BACKEND/PRPA/App.Domain/Entities/BaseEntity.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/TransferenciaEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/TransferenciaEstoqueItem.cs`
- `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/UnitOfWork.cs`
- `BACKEND/PRPA/App.Infra.Data/Repository/BaseRepository.cs`
- `BACKEND/PRPA/App.Service/Services/BaseServices.cs`
- `FRONTEND/src/app/application/operacao/operacao-routing.module.ts`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/saidaestoque/components/saidaestoque/saidaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/transferenciaestoque/components/transferenciaestoque/transferenciaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/reservaestoque/components/reservaestoque/reservaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/bloqueioestoque/components/bloqueioestoque/bloqueioestoque.component.ts`
- `FRONTEND/src/app/application/operacao/ajusteestoque/components/ajusteestoque/ajusteestoque.component.ts`

## 4. Premissas tecnicas

- A stack backend vigente e .NET 8, ASP.NET Core Web API, Entity Framework Core 8 e MySQL via Pomelo.
- O backend atual usa camadas `App.Domain`, `App.Service`, `App.Infra.Data`, `App.Infra.CrossCutting.IoC` e `PRPA`.
- O padrao existente usa entidades `BaseEntity`, repositories genericos, services genericos, DTOs de create/update, validators FluentValidation e controllers REST.
- O `UnitOfWork` existente oferece transacao local e tratamento generico de concorrencia EF, mas nao resolve idempotencia, outbox ou versionamento de agregado.
- O `BaseApiController` possui `[Authorize]`, mas controllers de estoque analisados possuem `[AllowAnonymous]`.
- O frontend vigente e Angular e possui telas legadas de entrada, saida, transferencia, reserva, bloqueio, inventario e ajuste.
- Codigo legado e insumo tecnico, nao autoridade de dominio, conforme DL-0031.

## 5. Escopo da primeira slice

Incluido:

- `LocalDeEstoque` como local valido de origem e destino;
- `UnidadeLogistica` minima, identificavel e rastreavel;
- `MovimentacaoDeEstoque` de uma unica UL entre dois locais;
- criacao da movimentacao;
- confirmacao explicita da movimentacao;
- camada de aplicacao para criacao e confirmacao, dependente apenas de abstracoes;
- idempotencia de comandos;
- concorrencia otimista;
- encaminhamento de eventos para abstracao compativel com outbox futura;
- historico operacional;
- projecoes de posicao da UL e conteudo do local;
- consultas minimas;
- autorizacao conceitual por permissao e escopo;
- testes de dominio e de aplicacao com harness provisorio;

Fora da primeira slice:

- cancelamento de movimentacao;
- rejeicao persistida de comando;
- eventos de cancelamento ou rejeicao;
- migrations, EF Core, mappings, DbContext, repositories concretos, controllers, endpoints e frontend;
- outbox fisica, worker, broker e integracoes externas;
- recebimento completo;
- reserva;
- inventario;
- separacao;
- expedicao;
- integracao ERP/WMS/SCADA/PLC;
- calculo avancado de saldo;
- multiplas unidades de medida em uma mesma movimentacao;
- fracionamento de UL;
- consolidacao de UL;
- multiplas ULs na mesma movimentacao;
- regras avancadas de lote, validade e qualidade;
- operacao offline;
- RFID ou coletor como requisito obrigatorio.

## 6. Linguagem ubiqua tecnica

| Termo | Definicao | Responsabilidade | Uso permitido | Legado relacionado | Diferenca semantica |
|---|---|---|---|---|---|
| Local de Estoque | Espaco fisico ou logico governado pelo dominio. | Validar origem, destino, estado, bloqueio e capacidade minima. | Referenciar onde uma UL esta ou pode estar. | `LocalizacaoEstoque`, `Almoxarifado`, `AreaEstoque`. | Nao e apenas caminho/hierarquia; possui autoridade de dominio. |
| Localizacao de Estoque | Referencia de posicionamento ou cadastro fisico existente. | Servir como insumo de LocalDeEstoque na primeira slice. | Reutilizar/adaptar como fonte cadastral. | `LocalizacaoEstoque`. | Nao deve virar AR concorrente de `LocalDeEstoque`. |
| Unidade Logistica | Objeto fisico ou virtual identificavel e rastreavel. | Controlar identidade, conteudo minimo, posicao atual e versao. | Ser o objeto movimentado. | Nao identificado no codigo atual. | Nao e produto, lote, saldo ou movimento. |
| Movimentacao de Estoque | Processo operacional que move uma UL entre locais. | Controlar ciclo, comandos, eventos e confirmacao no recorte atual. | Criar, confirmar e consultar o processo; cancelar fica adiado. | `MovimentoEstoque`, `TransferenciaEstoque`. | Nao e registro CRUD nem linha de saldo. |
| Movimento de Estoque | Registro legado de entrada/saida/transferencia. | Evidencia historica/legada e possivel read model. | Manter temporariamente ou adaptar como consulta. | `MovimentoEstoque`. | Nao substitui `MovimentacaoDeEstoque`. |
| Transferencia de Estoque | Fluxo legado de cabecalho e itens entre almoxarifados/localizacoes. | Insumo para UX e entendimento de transferencia. | Investigar/adaptar ou encapsular. | `TransferenciaEstoque`, `TransferenciaEstoqueItem`. | Nao opera por UL nem garante confirmacao atomicamente. |
| Confirmacao | Aceite operacional de fato fisico observado. | Mudar estado da movimentacao e posicao da UL. | Confirmar chegada da UL ao destino na primeira slice. | TODOs de telas operacionais. | Nao e simples salvar formulario. |
| Origem | Local onde a UL deve estar antes da movimentacao. | Validar presenca atual e permissao de saida. | Campo do comando e da movimentacao. | `localizacaoorigemid`, `almoxarifadoorigemid`. | Deve coincidir com posicao atual da UL. |
| Destino | Local alvo da movimentacao. | Validar existencia, estado, bloqueio e compatibilidade. | Campo do comando e da movimentacao. | `localizacaodestinoid`, `almoxarifadodestinoid`. | Somente vira posicao atual apos confirmacao. |
| Posicao atual | Local confirmado da UL. | Fonte transacional de localizacao atual da UL. | Consultas e validacoes de origem. | `SaldoEstoque.localizacaoestoqueid`. | Nao deve depender exclusivamente de saldo projetado. |
| Historico | Trilha append-only de comandos aceitos, eventos e auditoria. | Rastrear jornada da UL e do local. | Consulta e auditoria. | `MovimentoEstoque` pode ser insumo legado. | Nao deve ser editavel por PUT/DELETE. |
| Saldo | Quantidade agregada por produto/lote/local. | Apoiar consulta, disponibilidade e relatorio. | Projecao, nunca fonte primaria da movimentacao. | `SaldoEstoque`. | Nao autoriza nem bloqueia sozinho movimentacao. |
| Projecao | Modelo de leitura derivado de fatos confirmados. | Consulta rapida e reconstruivel. | Posicao, conteudo do local, historico, saldo. | `SaldoEstoque`, telas de consulta. | Pode estar defasada; comandos revalidam dominio. |
| Unidade de Medida | Unidade da quantidade representada pela UL. | Registrar quantidade minima da UL. | Primeira slice usa uma unidade por UL. | `UnidadeMedida`. | Conversoes ficam fora da slice. |
| Planta | Escopo industrial/operacional. | Separar autorizacao, eventos e dados. | Campo conceitual obrigatorio. | Nao identificado como entidade fisica unica. | Pode ser derivada de armazem enquanto modelo fisico nao existir. |
| Armazem | Agrupador fisico/logistico de locais. | Escopo operacional e autorizacao. | Referencia de origem/destino. | `Almoxarifado`. | Nao substitui LocalDeEstoque. |

Ambiguidades resolvidas para a primeira slice:

- `LocalDeEstoque` e o conceito de dominio; `LocalizacaoEstoque` e o artefato legado que podera suportar sua persistencia inicial.
- `MovimentacaoDeEstoque` e o processo; `MovimentoEstoque` e registro legado.
- `MovimentacaoDeEstoque` nao deve ser implementada como alias de `TransferenciaEstoque`.
- `MovimentacaoEstoque` sem `De` deve ser evitado em novos nomes documentais para reduzir ambiguidades.

## 7. Modelo minimo de UnidadeLogistica

`UnidadeLogistica` e Aggregate Root, conforme AS-0004 e DL-0023.

| Atributo | Classificacao | Observacao |
|---|---|---|
| identificador interno | Obrigatorio | Chave tecnica interna. |
| codigo externo | Opcional | Para integracoes futuras ou etiquetas existentes. |
| codigo de barras | Opcional | Pode coincidir com identificador operacional. |
| tipo | Futuro | Tipo de UL fica fora da primeira slice se nao houver cadastro aprovado. |
| produto ou material | Obrigatorio | Primeira slice representa uma UL simples com um produto/material. |
| lote | Opcional | Usar quando existir lote material. |
| numero de serie | Futuro | Nao exigido na primeira slice. |
| quantidade | Obrigatorio | Quantidade contida/representada pela UL. |
| unidade de medida | Obrigatorio | Uma unidade por UL na primeira slice. |
| posicao atual | Obrigatorio | Referencia ao LocalDeEstoque atual confirmado. |
| status | Obrigatorio | Estados minimos abaixo. |
| planta | Obrigatorio | Escopo operacional e de evento. |
| armazem | Obrigatorio | Pode ser derivado do LocalDeEstoque, mas deve estar consultavel. |
| data de criacao | Obrigatorio | Auditoria minima. |
| versao | Obrigatorio | Controle de concorrencia otimista. |
| informacoes de auditoria | Obrigatorio | Criacao, edicao, ator e timestamps. |

Estados minimos da UL:

- `Ativa`: pode ser movimentada.
- `EmMovimentacao`: possui movimentacao pendente ou confirmacao em andamento.
- `Bloqueada`: nao pode ser movimentada sem politica futura.
- `Encerrada`: nao pode participar de novas movimentacoes.

Respostas obrigatorias:

1. `UnidadeLogistica` e Aggregate Root.
2. Controla identidade, status, posicao atual, quantidade minima e versao.
3. Identidade: identificador interno imutavel; codigo operacional/codigo de barras podem ser chaves alternativas.
4. Conhece seu LocalDeEstoque atual como referencia de identidade, nao como objeto carregado completo.
5. Historico nao fica como colecao interna ilimitada; fica em eventos/historico/projecoes.
6. Presenca simultanea em dois locais e impedida por atualizacao transacional da posicao atual, versao esperada e restricao de movimentacao ativa.
7. Versao controlada por coluna de versao ou campo equivalente de concorrencia otimista.
8. Movimentacao parcial nao faz parte da primeira slice.
9. Uma movimentacao contem uma unica UL na primeira slice.
10. Produtos sem serializacao sao representados por UL virtual/minima contendo produto, lote opcional, quantidade e unidade.

## 8. Modelo minimo de LocalDeEstoque

`LocalDeEstoque` e Aggregate Root, conforme AS-0004 e DL-0024.

Na primeira slice, o codigo legado `LocalizacaoEstoque` deve ser tratado como candidato preferencial para persistir ou compor o LocalDeEstoque, desde que nao seja tratado como AR concorrente.

| Atributo | Classificacao | Observacao |
|---|---|---|
| identificador | Obrigatorio | Chave interna do local. |
| codigo | Obrigatorio | Codigo operacional unico no escopo definido. |
| descricao | Opcional | Apoio a consulta. |
| planta | Obrigatorio | Pode precisar derivacao enquanto nao houver entidade propria. |
| armazem | Obrigatorio | Relacionado a `Almoxarifado`. |
| area | Opcional | Relacionado a `AreaEstoque`. |
| tipo | Opcional | Relacionado a `TipoLocalizacao`. |
| status | Obrigatorio | Ativo, bloqueado, desativado. |
| capacidade | Opcional | Validar se houver capacidade configurada. |
| bloqueio | Obrigatorio | Destino bloqueado rejeita movimentacao. |
| restricoes | Futuro | Compatibilidade avancada fora da slice. |
| hierarquia | Opcional | Usar apenas para consulta/validacao existente. |
| versao | Obrigatorio se o local for alterado por comandos concorrentes | Para leitura de destino pode ser valida por estado atual. |

Respostas obrigatorias:

1. Local de Estoque e Aggregate Root.
2. Invariantes: existencia, ativo, nao bloqueado para destino, hierarquia sem ciclo, capacidade quando configurada.
3. Local nao mantem colecao direta de ULs armazenadas.
4. Ocupacao e projecao/read model.
5. Origem valida quando existe, esta ativa e coincide com posicao atual da UL; destino valida quando existe, esta ativo, nao bloqueado e pertence ao escopo.
6. Locais virtuais podem representar `Transito`, `Recebimento`, `Producao` ou `Expedicao`, mas na primeira slice apenas `Transito` e permitido como conceito tecnico interno.
7. Transito deve ser representado como estado da movimentacao e/ou posicao tecnica temporaria, nao como destino final confirmado.
8. Recebimento, producao e expedicao ficam como extensoes futuras.
9. Capacidade existe na primeira slice somente se ja configurada no local; se ausente, nao bloquear.
10. Bloqueio operacional existe na primeira slice e bloqueia destino.

## 9. Aggregate Root MovimentacaoDeEstoque

`MovimentacaoDeEstoque` e Aggregate Root tecnico da primeira slice, conforme AS-0004, AS-0005 e DL-0025.

Campos minimos:

- `id`;
- `unidadeLogisticaId`;
- `localOrigemId`;
- `localDestinoId`;
- `quantidade`;
- `unidadeMedidaId`;
- `motivo`;
- `tipo`;
- `status`;
- `solicitanteId`;
- `confirmadorId`;
- `dataSolicitacao`;
- `dataConfirmacao`;
- `versao`;
- `idempotencyKeyCriacao`;
- `idempotencyKeyConfirmacao`;
- `correlationId`;
- `causationId`.

Campos de cancelamento, rejeicao persistida, falha persistida ou motivo de cancelamento ficam adiados para incremento futuro.

Estados minimos implementaveis no recorte atualmente aprovado:

- `Solicitada`: movimentacao criada e aguardando confirmacao.
- `Confirmada`: movimentacao concluida; UL posicionada no destino.

Estados `Cancelada` e `Rejeitada`, assim como eventos correspondentes, ficam adiados para incremento futuro. Comandos recusados retornam erro de dominio ou de aplicacao sem persistir novo estado de rejeicao neste recorte.

Estados como `EmTransito`, `AguardandoRetirada` e `AguardandoConfirmacao` permanecem previstos pela AS-0005, mas ficam fora da primeira slice para evitar estados sem comportamento correspondente.

Maquina de estados do recorte atual:

| Estado atual | Comando | Condicoes | Novo estado | Evento |
|---|---|---|---|---|
| Nao existe | CriarMovimentacaoDeEstoque | UL ativa na origem, origem e destino validos, sem movimentacao ativa, idempotencia valida | Solicitada | MovimentacaoDeEstoqueCriada |
| Solicitada | ConfirmarMovimentacaoDeEstoque | versao esperada valida, destino ainda valido, UL ainda vinculada a movimentacao, idempotencia valida | Confirmada | MovimentacaoDeEstoqueConfirmada |

Respostas obrigatorias:

1. Movimentacao e criada e confirmada em comandos separados.
2. Confirmacao imediata pode existir como orquestracao de aplicacao que executa criar e confirmar na mesma transacao logica, mas nao elimina os dois comandos conceituais.
3. Confirma quem possui permissao `estoque.movimentacao.confirmar` no escopo da planta/armazem.
4. Movimentacao confirmada nao pode ser cancelada; correcoes futuras por estorno.
5. Correcoes serao feitas por estorno ou movimentacao compensatoria em AS futura.
6. Origem e destino nao podem ser iguais.
7. Destino bloqueado rejeita confirmacao e, preferencialmente, rejeita criacao.
8. Uma UL nao pode possuir movimentacao pendente simultanea.
9. O agregado controla uma unica UL na primeira slice.
10. O agregado referencia outros agregados por ID e snapshots minimos auditaveis; nao incorpora objetos completos.

## 10. Invariantes

| Invariante | Responsavel | Momento da validacao | Erro de dominio | Evidencia |
|---|---|---|---|---|
| Origem diferente do destino | Aplicacao + MovimentacaoDeEstoque | Criacao | `OrigemDestinoIguais` | AS-0005 secao 20 |
| Origem existente e ativa | Aplicacao + LocalDeEstoque | Criacao e confirmacao | `OrigemInvalida` | AS-0004 secao 18 |
| Destino existente e ativo | Aplicacao + LocalDeEstoque | Criacao e confirmacao | `DestinoInvalido` | AS-0004 secao 18 |
| Destino nao bloqueado | LocalDeEstoque | Criacao e confirmacao | `DestinoBloqueado` | AS-0005 INV-AS0005-003 |
| UL existente e ativa | UnidadeLogistica | Criacao e confirmacao | `UnidadeLogisticaInvalida` | DL-0023 |
| UL presente na origem | UnidadeLogistica + Aplicacao | Criacao | `UnidadeForaDaOrigem` | AS-0005 INV-AS0005-004 |
| UL nao presente simultaneamente em outro local | UnidadeLogistica + persistencia | Confirmacao | `PosicaoConcorrente` | AS-0004 secao 31 |
| Ausencia de movimentacao concorrente | MovimentacaoDeEstoque + persistencia | Criacao | `MovimentacaoConcorrente` | AS-0006 bloqueador |
| Confirmacao unica | MovimentacaoDeEstoque + idempotencia | Confirmacao | `MovimentacaoJaConfirmada` | AS-0005 INV-AS0005-007 |
| Idempotencia | Aplicacao + persistencia | Todos comandos | `IdempotencyConflict` | DL-0020 e DL-0032 |
| Versao esperada | Aplicacao + persistencia | Confirmacao | `VersaoConflitante` | AS-0004 secao 31 |
| Quantidade valida | UnidadeLogistica + MovimentacaoDeEstoque | Criacao | `QuantidadeInvalida` | AS-0004 secao 22 |
| Unidade de medida compativel | Aplicacao | Criacao | `UnidadeMedidaIncompativel` | AS-0005 secao 7 |
| Autorizacao | API/aplicacao | Antes do comando | `AcessoNegado` | AS-0004 secao 26 |
| Planta e armazem compativeis | Aplicacao | Criacao e consulta | `EscopoIncompativel` | AS-0003 secao 6.15 |
| Movimentacao confirmada imutavel | MovimentacaoDeEstoque | Atualizacao/cancelamento | `MovimentacaoImutavel` | DL-0007 |
| Historico obrigatorio | Aplicacao + outbox/historico | Comando aceito | `HistoricoNaoRegistrado` | AS-0004 secao 27 |
| Correlation ID obrigatorio | Aplicacao | Entrada do comando | `CorrelationIdObrigatorio` | AS-0004 secao 29 |
| Ator obrigatorio | API/aplicacao | Antes do comando | `AtorObrigatorio` | AS-0004 secao 27 |

Classificacao:

- Regras de dominio: estado da UL, estado da movimentacao, origem/destino, imutabilidade.
- Validacoes de aplicacao: carregamento de agregados, escopo, expected version, correlation ID.
- Validacoes de persistencia: unique constraints, versionamento, FK, idempotency record.
- Autorizacao: permissoes e escopo.
- Infraestrutura: outbox, transacao, logs, reprocessamento.

## 11. Comandos

### 11.1 CriarMovimentacaoDeEstoque

Intencao: registrar uma solicitacao de movimentacao de uma UL entre dois locais.

Campos:

| Campo | Obrigatorio | Observacao |
|---|---|---|
| `unidadeLogisticaId` | Sim | UL a movimentar. |
| `localOrigemId` | Sim | Deve coincidir com posicao atual da UL. |
| `localDestinoId` | Sim | Deve estar ativo e nao bloqueado. |
| `motivo` | Sim | Texto ou codigo parametrizado. |
| `tipo` | Sim | Primeira slice: movimentacao interna simples. |
| `idempotencyKey` | Sim | Escopo por ator, comando e payload. |
| `correlationId` | Sim | Deve acompanhar eventos e logs. |
| `causationId` | Opcional | Obrigatorio quando comando deriva de outro. |
| `expectedUnidadeLogisticaVersion` | Sim | Protege posicao desatualizada. |
| `solicitanteId` | Sim | Ator humano ou sistema. |
| `plantId` | Sim | Escopo operacional. |
| `warehouseId` | Sim | Escopo operacional. |

Agregados envolvidos: `UnidadeLogistica`, `LocalDeEstoque` origem, `LocalDeEstoque` destino, `MovimentacaoDeEstoque`.

Fronteira futura: gravar movimentacao solicitada, atualizar estado da UL para `EmMovimentacao`, gravar idempotency record, historico e outbox na mesma transacao local quando houver persistencia concreta. No incremento atual, a camada de aplicacao apenas delimita essa fronteira por interfaces.

Eventos: `MovimentacaoDeEstoqueCriada`.

Erros: origem/destino invalidos, UL invalida, conflito de versao, movimentacao concorrente, acesso negado, idempotency conflict.

### 11.2 ConfirmarMovimentacaoDeEstoque

Intencao: registrar que a UL chegou fisicamente ao destino e concluir a movimentacao.

Campos:

| Campo | Obrigatorio | Observacao |
|---|---|---|
| `movimentacaoId` | Sim | Movimentacao em estado `Solicitada`. |
| `expectedMovimentacaoVersion` | Sim | Protege confirmacoes simultaneas. |
| `expectedUnidadeLogisticaVersion` | Sim | Protege posicao/estado da UL. |
| `confirmadorId` | Sim | Ator humano ou sistema. |
| `idempotencyKey` | Sim | Deduplica confirmacao. |
| `correlationId` | Sim | Mesma correlacao da jornada ou nova correlacao vinculada. |
| `causationId` | Opcional | Pode apontar para evento/comando anterior. |

Fronteira futura: validar movimentacao, validar UL, validar destino, alterar movimentacao para `Confirmada`, alterar posicao atual da UL para destino, liberar estado `EmMovimentacao`, gravar historico, outbox e idempotency record quando houver persistencia concreta. No incremento atual, handlers dependem apenas de abstracoes.

Eventos: `MovimentacaoDeEstoqueConfirmada` e, no nucleo atual, `PosicaoDaUnidadeLogisticaAlterada` como evento de dominio da UL. A camada de aplicacao deve encaminhar ambos por abstracao, sem publicacao real ou outbox fisica nesta etapa.

Erros: movimentacao inexistente, ja confirmada, versao conflitante, destino bloqueado, acesso negado, idempotency conflict.

### 11.3 CancelarMovimentacaoDeEstoque

Nao faz parte do incremento atualmente aprovado.

Cancelamento, estado `Cancelada`, evento `MovimentacaoDeEstoqueCancelada`, motivo de cancelamento e regras de liberacao por cancelamento ficam adiados para incremento futuro. A camada de aplicacao atual nao deve expor comando, handler, contrato, endpoint ou teste de cancelamento.

## 12. Consultas

Consultas usam read models/projecoes quando possivel e nao devem carregar Aggregate Roots para listagens.

| Consulta | Filtros | Paginacao | Ordenacao | Campos retornados | Origem dos dados | Consistencia | Autorizacao | Indices conceituais |
|---|---|---|---|---|---|---|---|---|
| Movimentacao por ID | `movimentacaoId` | Nao | Nao | id, UL, origem, destino, status, datas, atores, versao | tabela/projecao de movimentacao | Forte ou eventual controlada | `estoque.movimentacao.consultar` | PK |
| Posicao atual da UL | `unidadeLogisticaId`, `codigo` | Nao | Nao | UL, status, local atual, produto, lote, quantidade, versao | UL + projecao | Forte para comando; eventual para tela | `estoque.local.consultar` | UL id, codigo |
| Historico da UL | UL, periodo | Sim | data desc | eventos, locais, atores, correlationId | historico/projecao | Eventual reconstruivel | `estoque.historico.consultar` | UL + data |
| Conteudo do Local | local, status UL | Sim | codigo UL | ULs presentes, produto, lote, quantidade | projecao ConteudoDoLocal | Eventual | `estoque.local.consultar` | local + status |
| Movimentacoes por periodo | periodo, local, ator | Sim | data desc | resumo da movimentacao | projecao/listagem | Eventual | `estoque.movimentacao.consultar` | data, local, status |
| Movimentacoes por status | status, planta, armazem | Sim | data asc/desc | pendentes e confirmadas; canceladas em incremento futuro | projecao/listagem | Eventual | `estoque.movimentacao.consultar` | status + data |

## 13. Eventos

Envelope minimo:

- `eventId`;
- `eventName`;
- `schemaVersion`;
- `aggregateType`;
- `aggregateId`;
- `aggregateVersion`;
- `occurredAt`;
- `correlationId`;
- `causationId`;
- `actorId`;
- `plantId`;
- `warehouseId`;
- `payload`.

Catalogo minimo:

| Evento | Tipo | Aggregate Root | Momento | Payload minimo | Consumidores |
|---|---|---|---|---|---|
| `MovimentacaoDeEstoqueCriada` | Domain Event | MovimentacaoDeEstoque | Apos criacao aceita | movimentacaoId, UL, origem, destino, motivo, solicitante, versao | historico, outbox, projecoes |
| `MovimentacaoDeEstoqueConfirmada` | Domain Event | MovimentacaoDeEstoque | Apos confirmacao | movimentacaoId, UL, origem, destino, confirmador, data, versao | historico, posicao UL, conteudo local, saldo futuro |
| `MovimentacaoDeEstoqueRejeitada` | Evento de auditoria futuro | Nao faz parte do recorte atual | Adiado | Nao definido neste incremento | auditoria, observabilidade futura |

Eventos avaliados e nao adotados como obrigatorios na primeira slice:

- `UnidadeLogisticaMovimentada`: redundante com `MovimentacaoDeEstoqueConfirmada` na primeira slice.
- `PosicaoDaUnidadeLogisticaAlterada`: pode ser derivado internamente no mesmo evento de confirmacao. Criar evento separado somente se houver consumidor claro.
- Integration Events externos: fora da primeira slice.

## 14. Consistencia e transacao

Fronteira transacional da confirmacao:

1. Sao alterados dois Aggregate Roots: `MovimentacaoDeEstoque` e `UnidadeLogistica`. `LocalDeEstoque` e lido/validado.
2. A posicao da UL e alterada na mesma transacao da confirmacao.
3. A movimentacao e confirmada na mesma transacao.
4. Historico minimo e gravado na mesma transacao.
5. Outbox fisica sera gravada na mesma transacao em incremento futuro; no incremento atual de aplicacao, eventos sao encaminhados apenas para uma abstracao compativel com outbox futura.
6. Projecoes podem ser atualizadas sincrona ou assincronamente; para a primeira slice, pode haver atualizacao sincrona local das projecoes criticas se nao houver worker de outbox pronto.
7. Falha apos persistencia e antes da publicacao e tratada pela outbox pendente.
8. Reprocessamento usa `eventId`, `correlationId`, estado da outbox e idempotencia de consumidores.
9. Reconciliacao de projecoes reprocessa eventos de movimentacao por UL/local/periodo.
10. Estado parcialmente confirmado devera ser impedido por transacao local quando houver persistencia concreta; no incremento atual, `IUnitOfWork` e interfaces de repository apenas delimitam essa fronteira futura.

Alternativas avaliadas:

| Alternativa | Avaliacao |
|---|---|
| Atualizar apenas Movimentacao e projetar posicao depois | Rejeitada para primeira slice; permitiria posicao transacional defasada. |
| Atualizar UL e Movimentacao na mesma transacao local | Recomendada; cabe na stack EF/MySQL existente. |
| Usar transacao distribuida | Rejeitada; AS-0004 evita dependencia de transacao distribuida global. |
| Publicar evento diretamente sem outbox | Rejeitada; risco de perda apos commit. |

## 15. Idempotencia

Estrategia minima:

- todo comando mutavel exige `idempotencyKey`;
- chave com escopo por `commandName`, `actorId`, `plantId`, `warehouseId` e hash canonico do payload;
- persistir `IdempotencyRecord` ou `CommandInbox`;
- constraint unica por escopo e chave;
- armazenar payload hash, status de processamento, resultado resumido, erro e timestamps;
- repeticao com mesmo payload retorna resultado anterior;
- repeticao com payload divergente retorna `IdempotencyConflict`;
- validade logica minima: manter enquanto houver relevancia operacional/auditoria; expurgo so com politica futura;
- auditoria registra repeticoes.

Cenarios:

| Cenario | Comportamento |
|---|---|
| Criacao duplicada | Retornar movimentacao ja criada se payload igual. |
| Confirmacao duplicada | Retornar confirmacao anterior se payload igual. |
| Reenvio apos timeout | Consultar idempotency record e retornar resultado conhecido. |
| Reprocessamento de integracao | Usar eventId/messageId quando existir integracao futura. |
| Repeticao de mensagem | Consumidor idempotente por eventId. |
| Repeticao por usuario | Mesmo tratamento por idempotency key. |

## 16. Concorrencia

Estrategia recomendada:

- adicionar versionamento otimista conceitual em `UnidadeLogistica` e `MovimentacaoDeEstoque`;
- comandos recebem expected version;
- confirmar compara versao esperada da movimentacao e da UL;
- impedir duas movimentacoes pendentes para a mesma UL com restricao unica conceitual de movimentacao ativa;
- usar transacao local EF/MySQL;
- utilizar indice unico filtrado/logico para `unidadeLogisticaId` + status ativo quando suportado, ou tabela/flag controlada transacionalmente se o banco nao suportar filtro;
- projecao atrasada nunca substitui leitura transacional da UL para comando.

Alternativas rejeitadas:

- confiar apenas em `SaldoEstoque`;
- confiar apenas no frontend;
- usar lock pessimista amplo por armazem;
- criar fila serial global de estoque;
- permitir confirmacao sem expected version.

## 17. Saldo e projecoes

Regras:

- saldo nao e fonte primaria da movimentacao;
- saldo e projecao quando aplicavel;
- posicao atual da UL e estado transacional da `UnidadeLogistica`;
- conteudo do local e projecao derivada da posicao atual e eventos;
- historico da UL e do local deriva de eventos/historico append-only;
- consolidacao por produto fica em projecao de saldo futura;
- reconstrucoes usam eventos/historico e estado transacional da UL;
- reconciliacao compara projecoes contra estado transacional e historico.

Coexistencia com `SaldoEstoque` legado:

- manter temporariamente como consulta legada;
- nao usar para validar movimentacao da nova vertical;
- nao atualizar diretamente por comandos de movimentacao sem estrategia explicita;
- quando necessario, atualizar como projecao derivada de `MovimentacaoDeEstoqueConfirmada`;
- sinalizar divergencias entre saldo legado e posicao de UL como pendencia de reconciliacao, nao como sobrescrita automatica.

## 18. Persistencia conceitual

Modelo de dominio, persistencia e leitura devem ser separados.

| Estrutura | Tipo | Finalidade | Chave | Campos principais | Constraints/indices | Versionamento/auditoria |
|---|---|---|---|---|---|---|
| `UnidadeLogistica` | Dominio/persistencia | Fonte transacional da UL | id | codigo, produto, lote, quantidade, unidade, localAtual, status, planta, armazem | codigo unico por planta; localAtual indexado | versao, criacao, edicao, ator |
| `LocalDeEstoque` | Dominio/persistencia | Local governado | id | codigo, almoxarifado, area, tipo, status, bloqueio, capacidade | codigo unico por escopo; status | versao se mutavel |
| `MovimentacaoDeEstoque` | Dominio/persistencia | Processo de movimentacao | id | UL, origem, destino, status, motivo, tipo, datas, atores, correlationId | UL+status ativo; status+data | versao, auditoria |
| `HistoricoUnidadeLogistica` | Historico/read | Trilha append-only | id/eventId | UL, local anterior, local novo, evento, ator, data | UL+data; local+data | append-only |
| `OutboxEvent` | Integracao interna | Publicacao confiavel | eventId | envelope, payload, status, tentativas, erro | status+data; eventId unico | timestamps |
| `CommandInbox` ou `IdempotencyRecord` | Infra/aplicacao | Idempotencia | id | key, command, actor, payloadHash, status, result | chave unica por escopo | timestamps |
| `ProjecaoPosicaoUL` | Read model | Consulta rapida da posicao | unidadeLogisticaId | local, status, ultimaMovimentacao | local/status | reconstruivel |
| `ProjecaoConteudoLocal` | Read model | Conteudo por local | localDeEstoqueId + UL | UL, produto, lote, quantidade | local+produto | reconstruivel |

Nenhuma tabela fisica e criada por esta AS.

## 19. Seguranca

Mecanismo atual:

- Identity/JWT configurado em `BACKEND/PRPA/PRPA/Program.cs`;
- policies atuais `AdminOnly` e `UserOnly`;
- `BaseApiController` com `[Authorize]`;
- controllers de estoque legados com `[AllowAnonymous]`.

Permissoes conceituais minimas:

- `estoque.movimentacao.criar`;
- `estoque.movimentacao.confirmar`;
- `estoque.movimentacao.consultar`;
- `estoque.historico.consultar`;
- `estoque.local.consultar`.

Regras:

- ator humano deve vir do token autenticado;
- integracao de sistema futura deve possuir identidade propria;
- escopo por planta e armazem deve ser validado antes do comando;
- correlation ID e source sao obrigatorios;
- remover `[AllowAnonymous]` dos novos endpoints da vertical;
- endpoints legados nao devem ser reaproveitados para comandos criticos sem revisao de autorizacao;
- auditoria deve registrar ator, permissao avaliada, escopo, comando e resultado.

Esta AS nao altera codigo nem remove atributos existentes.

## 20. Coexistencia com legado

| Elemento legado | Significado atual | Conceito novo relacionado | Estrategia |
|---|---|---|---|
| `LocalizacaoEstoque` | Cadastro/hierarquia fisica com bloqueio/capacidade | `LocalDeEstoque` | Adaptar como persistencia inicial ou facade; evitar AR concorrente. |
| `MovimentoEstoque` | Registro CRUD de movimento | Historico/projecao legada | Manter temporariamente; nao usar como AR da nova vertical. |
| `TransferenciaEstoque` | Cabecalho de transferencia | Caso de uso futuro de movimentacao | Investigar/adaptar; nao basear primeira slice nele. |
| `TransferenciaEstoqueItem` | Item por produto/lote/local | Futuro movimento multi-item | Investigar; fora da primeira slice. |
| `SaldoEstoque` | Estado persistido de saldo | Projecao de saldo | Encapsular como leitura legada; nao fonte primaria. |
| Controllers CRUD de estoque | CRUD anonimo | APIs legadas | Manter temporariamente; criar APIs de comando em recorte novo depois de aprovado. |
| Services genericos | CRUD/validacao simples | Application services | Adaptar ou criar services especificos para comandos. |
| Repositories genericos | Persistencia EF simples | Repositories de agregados | Reutilizar parcialmente, mas agregar metodos especificos de concorrencia. |
| DTOs create/update | Contratos CRUD | Contratos de comando | Substituir por comandos explicitos na nova vertical. |
| Telas operacionais | Fluxos parciais de entrada/saida/transferencia | UX da vertical | Encapsular ou substituir gradualmente. |
| Tabelas legadas | Persistencia atual | Fonte de migracao/read model | Manter temporariamente; migrar por strangler. |

Estrategia strangler:

1. Introduzir arquitetura nova em recorte isolado de dominio/aplicacao.
2. Ler locais e produtos/lotes legados como referencias.
3. Criar ULs minimas para piloto controlado.
4. Movimentar UL pela nova vertical sem depender de `MovimentoEstoque`.
5. Produzir historico/projecoes novas.
6. Expor consultas novas ao frontend em tela controlada.
7. Reconciliar com saldo/movimento legado.
8. Descontinuar fluxos legados apenas apos cobertura funcional e decisao humana.

## 21. Testes obrigatorios

### 21.1 Testes de dominio

- criacao valida;
- origem igual ao destino;
- unidade fora da origem;
- unidade bloqueada;
- destino bloqueado;
- confirmacao valida;
- confirmacao duplicada;
- versao desatualizada;
- movimentacao concorrente;
- idempotencia;
- evento gerado.

### 21.2 Testes de aplicacao

- autorizacao por permissao;
- escopo por planta/armazem;
- carregamento dos agregados;
- transacao de criacao;
- transacao de confirmacao;
- repository especifico;
- outbox;
- erro de dominio;
- conflito de versao;
- repeticao do comando.

### 21.3 Testes de integracao

- persistencia de UL, movimentacao, historico e outbox;
- constraints de idempotencia;
- constraint/logica de movimentacao ativa;
- concorrencia de confirmacao;
- rollback;
- atualizacao de projecoes;
- reprocessamento de outbox.

### 21.4 Teste end-to-end minimo

1. consultar posicao inicial da UL;
2. criar movimentacao;
3. confirmar movimentacao;
4. consultar movimentacao;
5. consultar nova posicao;
6. consultar historico;
7. consultar conteudo do destino.

Criterios de aceite:

- origem e destino diferentes;
- UL muda de local somente na confirmacao;
- comandos duplicados nao duplicam eventos;
- confirmacao concorrente retorna conflito;
- historico possui correlationId, actorId e timestamps;
- consulta do destino mostra a UL apos confirmacao;
- endpoints novos exigem autenticacao/autorizacao.

## 22. Primeira vertical slice implementavel

Slice aprovada tecnicamente por esta AS para proxima execucao:

> Movimentar uma unica Unidade Logistica de um Local de Estoque para outro, mediante criacao e confirmacao explicita, com idempotencia, concorrencia otimista, eventos encaminhados por abstracao, historico conceitual e consulta futura da nova posicao.

Fluxo:

```text
Consultar Local origem
-> Consultar/identificar UL
-> Consultar Local destino
-> CriarMovimentacaoDeEstoque
-> MovimentacaoDeEstoqueCriada
-> ConfirmarMovimentacaoDeEstoque
-> MovimentacaoDeEstoqueConfirmada
-> Atualizar posicao transacional da UL
-> Encaminhar eventos para abstracao compativel com historico/outbox futura
-> Projetar posicao e conteudo do local
-> Consultar movimentacao, posicao e historico
```

Fora deliberadamente:

- retirada/transito em etapas;
- multi-UL;
- parcial;
- reserva;
- saldo avancado;
- integracao ERP;
- frontend completo de coletor.

## 23. Plano de implementacao incremental

| Etapa | Entregavel | Dependencias | Criterio de conclusao | Risco |
|---|---|---|---|---|
| 1 | Contratos e nomenclatura | AS-0007 aprovada | comandos, eventos, erros e DTOs conceituais revisados | ambiguidade com legado |
| 2 | Value Objects | Etapa 1 | IDs, IdempotencyKey, CorrelationId, ExpectedVersion modelados | excesso de abstracao |
| 3 | Aggregate Roots | Etapa 2 | UL, LocalDeEstoque e Movimentacao com invariantes | acoplamento ao EF |
| 4 | Invariantes | Etapa 3 | testes de dominio passando | lacunas de regra |
| 5 | Comandos | Etapa 4 | Criar/Confirmar/Cancelar definidos | comandos virarem CRUD |
| 6 | Handlers | Etapa 5 | transacao de aplicacao desenhada/implementada | transacao parcial |
| 7 | Repositories | Etapa 6 | metodos especificos por agregado | repository generico insuficiente |
| 8 | Persistencia | Etapa 7 | mappings planejados e revisados | migration prematura |
| 9 | Migrations | Etapa 8 | migration revisada antes de aplicar | schema incompleto |
| 10 | Idempotencia | Etapa 8 | constraint e replay testados | payload divergente |
| 11 | Concorrencia | Etapa 10 | expected version e conflito testados | condicao de corrida |
| 12 | Eventos | Etapa 11 | eventos no passado e envelope | redundancia de eventos |
| 13 | Outbox | Etapa 12 | evento persistido atomicamente | publicacao perdida |
| 14 | Projecoes | Etapa 13 | posicao e conteudo consultaveis | defasagem mal comunicada |
| 15 | Consultas | Etapa 14 | read models paginados/autorizados | consultas via AR |
| 16 | APIs | Etapa 15 | endpoints de comando/consulta protegidos | repetir padrao AllowAnonymous |
| 17 | Autorizacao | Etapa 16 | policies/permissoes por escopo | roles fixas insuficientes |
| 18 | Testes | Todas | suite minima automatizada | cobertura superficial |
| 19 | Integracao frontend | Etapa 16 | tela minima usa contratos novos | reaproveitar UX legada demais |
| 20 | Observabilidade | Etapa 13 | logs, correlationId, metricas minimas | diagnostico pobre |

Nao iniciar por tabelas ou controllers.

## 24. Definition of Ready revisada

Historico:

- AS-0006: `NOT READY`.
- AS-0007 antes dos DLs tecnicos: `READY WITH CONDITIONS`.
- AS-0007 apos DL-0033 a DL-0037: `READY` para iniciar o primeiro incremento de codigo definido nesta sessao.

| Criterio | AS-0006 | AS-0007 | Decisao consolidada | Status final |
|---|---|---|---|---|
| Escopo | Bloqueado por falta de desenho tecnico | Slice unica UL entre locais | DL-0030 e AS-0007 | Atendido |
| Linguagem ubiqua | Parcial | Termos tecnicos e legado diferenciados | AS-0007 secao 6 | Atendido |
| Agregados | Parcial | UL, LocalDeEstoque e MovimentacaoDeEstoque definidos | DL-0023, DL-0024, DL-0025 e AS-0007 | Atendido |
| Invariantes | Parcial | Matriz de invariantes | AS-0007 secao 10 | Atendido |
| Comandos | Parcial | Criar e confirmar no recorte atual; cancelar adiado | AS-0007 secao 11 | Atendido |
| Eventos | Parcial | Eventos minimos e envelope | DL-0036 e AS-0007 secao 13 | Atendido |
| Idempotencia | Bloqueador | Estrategia recomendada | DL-0033 | Atendido |
| Concorrencia | Bloqueador | Concorrencia otimista e exclusividade de UL | DL-0034 | Atendido |
| Transacao | Bloqueador tecnico | Fronteira local definida | DL-0035 | Atendido |
| Persistencia | Parcial | Modelo conceitual e estruturas | AS-0007 secao 18; DL-0033 a DL-0036 | Atendido para iniciar incremento de dominio; parcial para migrations |
| Outbox | Bloqueador | Transactional Outbox definida | DL-0036 | Atendido |
| Projecoes | Parcial | Posicao, conteudo e historico projetados | AS-0007 secoes 12, 17 e 18 | Atendido |
| Historico | Parcial | Historico transacional na mesma transacao | DL-0035 | Atendido |
| Autorizacao | Parcial | Permissoes minimas e regra de nao usar AllowAnonymous | AS-0007 secao 19; DL-0029 | Atendido para desenho; implementacao pendente |
| Legado | Bloqueador | Strangler e anticorrupcao | DL-0037 | Atendido |
| Testes | Ausente/parcial | Testes obrigatorios definidos | AS-0007 secao 21 | Atendido para iniciar primeiro incremento |
| Observabilidade | Parcial | CorrelationId, outbox e auditoria definidos | DL-0033, DL-0036 | Atendido para desenho inicial |
| Migration readiness | Ausente | Migrations condicionadas a dominio/testes | AS-0007 secao 23 | Parcialmente atendido |
| API readiness | Ausente | APIs devem vir depois de dominio/aplicacao | AS-0007 secao 23 | Parcialmente atendido |

Conclusao de prontidao arquitetural:

```text
READY
```

Esta classificacao autoriza apenas o primeiro incremento recomendado na secao 28. Nao autoriza iniciar por migrations, endpoints publicos, telas ou integracao com legado.

## 25. Decision Logs

Decision Logs tecnicos consolidados:

- DL-0033 - Estrategia de Idempotencia dos Comandos da Primeira Vertical de Estoque.
- DL-0034 - Concorrencia Otimista e Exclusividade de Movimentacao de Unidade Logistica.
- DL-0035 - Fronteira Transacional da Confirmacao de Movimentacao de Estoque.
- DL-0036 - Transactional Outbox para Eventos da Primeira Vertical de Estoque.
- DL-0037 - Estrategia de Coexistencia com o Legado de Estoque.

## 26. Riscos remanescentes

- Mapeamento fisico de `LocalDeEstoque` sobre `LocalizacaoEstoque` pode exigir ajuste fino.
- MySQL/EF pode limitar constraint unica filtrada para movimentacao ativa; DL-0034 define alternativas transacionais equivalentes.
- Autorizacao por permissao granular ainda nao existe fisicamente.
- Projecoes podem exigir worker/outbox que ainda nao existe; DL-0036 permite persistir outbox antes do publicador.
- Criar UL virtual para produtos sem serializacao pode exigir migracao de dados ou tela de seed operacional.
- Testes backend de dominio podem exigir estrutura de projeto de teste ainda nao identificada.

Nenhum item acima bloqueia o primeiro incremento recomendado na secao 28, pois esse incremento limita-se ao nucleo de dominio e testes unitarios.

## 27. Criterios de aceite da AS-0007

- Define modelo minimo de `UnidadeLogistica`.
- Define modelo minimo de `LocalDeEstoque`.
- Define `MovimentacaoDeEstoque` como AR tecnico da slice.
- Define comandos e consultas.
- Define invariantes, estados e eventos minimos.
- Define idempotencia, concorrencia, transacao, outbox e projecoes.
- Define coexistencia com legado.
- Define testes obrigatorios e plano incremental.
- Consolida os DL-0033 a DL-0037.
- Reavalia a Definition of Ready como `READY` para o primeiro incremento.
- Nao altera backend, frontend, migrations, endpoints, dependencias ou schemas.

## 28. Primeiro incremento de codigo recomendado

Opcao recomendada: **Opcao A - Nucleo de dominio**.

Justificativa baseada no estado real do backend:

- nao foi identificado projeto de testes backend;
- nao existem `UnidadeLogistica` e `MovimentacaoDeEstoque` no codigo;
- nao existem outbox, idempotencia ou versionamento nos agregados de estoque;
- os controllers atuais de estoque sao CRUD e usam `[AllowAnonymous]`;
- iniciar por vertical minima completa exigiria dominio, persistencia, migrations, endpoints, outbox, autorizacao e frontend de uma vez, aumentando risco de consolidar atalhos.

Objetivo:

Implementar o nucleo de dominio de `UnidadeLogistica` e `MovimentacaoDeEstoque`, com estados, invariantes, eventos de dominio conceituais e testes unitarios, ainda sem API publica e sem integracao com legado.

Escopo:

- Value Objects minimos: identificadores, `IdempotencyKey`, `CorrelationId`, `ExpectedVersion`;
- Aggregate Root `UnidadeLogistica`;
- Aggregate Root `MovimentacaoDeEstoque`;
- estados minimos: `Solicitada`, `Confirmada`, `Cancelada`, `Rejeitada`;
- comandos conceituais no nivel de dominio/aplicacao interna;
- eventos de dominio em memoria ou estrutura conceitual interna;
- testes unitarios de invariantes e transicoes.

Exclusoes:

- migrations;
- endpoints publicos;
- frontend;
- escrita em tabelas legadas;
- outbox fisica;
- workers;
- integracao ERP;
- atualizacao de `SaldoEstoque`.

Criterio de conclusao:

- testes unitarios cobrindo criacao valida, origem igual ao destino, UL fora da origem, destino bloqueado, confirmacao valida, confirmacao duplicada, versao desatualizada e movimentacao concorrente em nivel de dominio.


### 28.1 Alinhamento do primeiro incremento apos revisao tecnica

Apos a revisao tecnica do nucleo de dominio, o primeiro incremento efetivamente aprovado fica restrito a:

- `CriarMovimentacaoDeEstoque`;
- `ConfirmarMovimentacaoDeEstoque`;
- camada de aplicacao dependente apenas de abstracoes;
- repositories apenas como interfaces;
- idempotencia apenas como abstracao;
- eventos apenas encaminhados para abstracao;
- exclusividade de movimentacao apenas como abstracao compativel com garantia transacional futura.

Nao fazem parte deste incremento: cancelamento, rejeicao persistida, migrations, EF Core, mappings, DbContext, repositories concretos, controllers, endpoints, frontend, worker, broker, outbox fisica, integracao com legado, escrita em tabelas legadas ou alteracao de schema.

A protecao em memoria por status `EmMovimentacao` no dominio ajuda a preservar a invariante durante o harness, mas nao substitui concorrencia real. A garantia real de exclusividade dependera de persistencia, transacao local e constraints ou mecanismo equivalente em incremento futuro, conforme DL-0034 e DL-0035.
## 29. Conclusao

A AS-0007 remove os bloqueadores arquiteturais da AS-0006 e fornece uma arquitetura tecnica implementavel para a primeira slice, agora consolidada pelos Decision Logs DL-0033 a DL-0037.

A prontidao arquitetural para iniciar o primeiro incremento e:

```text
READY
```

A prontidao e arquitetural. O codigo continua sem `UnidadeLogistica`, `MovimentacaoDeEstoque`, eventos, idempotencia, concorrencia, outbox e testes implementados ate que uma proxima execucao realize a codificacao aprovada.

## 30. Proximos passos

1. Revisao humana da AS-0007 atualizada e dos DL-0033 a DL-0037.
2. Implementar o primeiro incremento recomendado na secao 28.
3. Comecar por contratos internos, testes e agregados, nao por tabelas/controllers.
4. Preservar coexistencia com legado via strangler incremental.
5. Criar migrations e endpoints somente apos o nucleo de dominio estar testado.