- Implementada a consulta operacional read-only da nova vertical de Estoque: `GET /api/estoque/unidades-logisticas`, `GET /api/estoque/unidades-logisticas/{id}`, `GET /api/estoque/unidades-logisticas/{id}/movimentacoes`, `GET /api/estoque/locais`, `GET /api/estoque/locais/{id}` e `GET /api/estoque/locais/{id}/unidades-logisticas`; frontend com rotas `/operacao/unidades-logisticas` e `/operacao/locais-estoque`, integradas a `/operacao/movimentacaoestoque-nova` para preencher UL, versao esperada, origem e destino; sem migration, sem database update, sem escrita em legado e sem uso de `MovimentoEstoque`/`LocalizacaoEstoque` como autoridade.
# Changelog do Project Book

## 2026-07-28

- Refatorada a experiencia visual do frontend da primeira vertical funcional de Estoque em `FRONTEND/src/app/application/operacao/movimentacaoestoque-nova/**`, mantendo a rota `/operacao/movimentacaoestoque-nova` e organizando a operacao em abas, etapas de criacao, revisao, resultado e confirmacao; sem alterar backend, migrations ou menu no banco.

- Implementada a API publica da primeira vertical funcional de Estoque para `CriarMovimentacaoDeEstoque`, `ConfirmarMovimentacaoDeEstoque` e consulta por ID em `GET/POST /api/estoque/movimentacoes`, com autorizacao obrigatoria, `Idempotency-Key`, correlation ID, tratamento estavel de erros e 75 cenarios do harness aprovados; sem frontend, sem nova migration e sem `database update` nesta etapa.

- Revisado o pre-deploy da migration `20260727170707_CreateFirstEstoqueVertical` apos reorganizacao do backend: toolchain local alinhada para `dotnet-ef` 8.0.0, builds e harness de dominio/aplicacao aprovados com 65 cenarios, script SQL gerado apenas para inspecao em diretorio ignorado e nenhuma migration aplicada.
- Inspecao remota do banco de demonstracao classificada como bloqueada nesta execucao por ausencia das variaveis `MES_DEMO_DB_*` e do gate `MES_DEMO_CONFIRMATION=DEMO_DATABASE_CONFIRMED`; nenhum segredo foi registrado e nenhum DDL foi executado.

## 2026-07-27

- Resolvidas as decisoes bloqueadoras de persistencia da primeira vertical de Estoque: persistencia propria de LocalDeEstoque com correlacao ao legado, exclusividade ativa por tabela de reserva transacional, identificadores fisicos e classificacao da retencao como pendencia operacional de go-live.
- Criado inventario da implementacao legada de Locais de Estoque e registrado o DL-0041, consolidando transicao paralela com carga inicial/correlacao, fonte da verdade e ocupacao calculada.
- Implementada a infraestrutura de persistencia da primeira vertical de Estoque no backend: mapeamentos EF, repositories concretos, reserva transacional, idempotencia persistida, transactional outbox e Unit of Work, sem migrations, sem scripts SQL, sem API, sem frontend e sem workers/publicadores.
- Revisada tecnicamente a infraestrutura EF Core da primeira vertical de Estoque; corrigidas a preservacao de erros estaveis do Unit of Work e a identificacao de duplicidade da reserva transacional, com 65 cenarios aprovados e build da solucao concluido.
- Gerada e revisada a primeira migration EF Core da vertical de Estoque (`20260727170707_CreateFirstEstoqueVertical`), criando somente as seis tabelas aprovadas, com SQL de inspecao temporario e sem executar `database update`.
- Tentada a preparacao da validacao fisica da migration `20260727170707_CreateFirstEstoqueVertical` em MySQL descartavel; teste classificado como `TESTE BLOQUEADO` por ausencia de Docker, cliente/servico MySQL local ou porta MySQL descartavel comprovavel, sem executar `database update`.


## 2026-07-24

- Criada a especificacao tecnica `Contrato de Persistencia da Primeira Vertical de Estoque.md`, cobrindo modelo relacional, concorrencia, exclusividade ativa, idempotencia persistida, transactional outbox, fronteira transacional e compatibilidade com legado, sem implementar infraestrutura.

- Criada a AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque.
- Registrada classificacao final `NOT READY` para retomada da codificacao funcional da primeira vertical de Estoque.
- Atualizados indices e roadmap com a AS-0006.
- Criada a AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.
- Registrada classificacao documental `READY WITH CONDITIONS` para iniciar a primeira slice apos validacao humana.
- Atualizados indices e roadmap com a AS-0007.
- Criados os Decision Logs DL-0033 a DL-0037 para consolidar idempotencia, concorrencia, fronteira transacional, outbox e coexistencia com legado.
- Atualizada a AS-0007 com a classificacao final `READY` para o primeiro incremento de dominio.
- Atualizados indices, roadmap e glossario com os DL-0033 a DL-0037.
- Implementado o primeiro incremento de codigo autorizado pela AS-0007: nucleo de dominio de Estoque em memoria, com `UnidadeLogistica`, `LocalDeEstoque`, `MovimentacaoDeEstoque`, eventos de dominio, erros, Value Objects e testes unitarios auto-contidos, sem migrations, endpoints, persistencia EF, frontend ou integracao com legado.
- Revisado e padronizado o nucleo de dominio de Estoque: movido de `App.Domain/Estoque` para `App.Domain/Entities/Estoque`, namespaces atualizados para `App.Domain.Entities.Estoque`, cancelamento/rejeicao adiados para incremento futuro e harness marcado como provisorio.
- Revisado tecnicamente o nucleo de dominio de Estoque: corrigido envelope comum de eventos com `AggregateVersion` e `WarehouseId`, adicionadas validacoes temporais de criacao/solicitacao/confirmacao e ampliado o harness provisorio para 32 cenarios.
- Alinhada a AS-0007 ao recorte efetivamente aprovado de `CriarMovimentacaoDeEstoque` e `ConfirmarMovimentacaoDeEstoque`; implementada camada de aplicacao abstrata em `App.Service` com commands, handlers, interfaces de repository/unit of work/idempotencia/eventos/exclusividade e harness ampliado para 53 cenarios, sem infraestrutura concreta.
- Reorganizada a camada de aplicacao de Estoque de `App.Service/Estoque` para `App.Service/Services/Estoque`, com namespaces atualizados para `App.Service.Services.Estoque`, sem alteracao funcional, de contratos ou de comportamento.
- Criada a AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.
- Criados os Decision Logs DL-0030, DL-0031 e DL-0032 derivados da AS-0005.
- Atualizados indices, roadmap e glossario com referencias da AS-0005.
- Nenhuma alteracao de backend ou frontend foi realizada.

- 2026-07-29: Aplicada autorizacao granular backend na vertical atual de Estoque com policies ASP.NET Core para `estoque.unidade-logistica.consultar`, `estoque.unidade-logistica.historico`, `estoque.local.consultar`, `estoque.local.conteudo`, `estoque.movimentacao.consultar`, `estoque.movimentacao.criar` e `estoque.movimentacao.confirmar`; mantido `[Authorize]`, 401 para nao autenticado e 403 para autenticado sem permissao. A duplicidade `/operacao/...` e `/home/operacao/...` foi mantida por ja existir na montagem do frontend; menu recomendado deve usar `/operacao/...`.
- 2026-07-29: Corrigido o comportamento pos-salvamento da tela legada de Cadastro de Locais de Estoque (`LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`): a tela preserva Armazem e Area, recarrega a hierarquia por area, seleciona o local salvo, permanece em modo edicao e exibe estados vazios/loading/erro mais claros. Nao houve alteracao backend, migration, database update ou sincronizacao automatica com `LocalDeEstoque` da nova vertical.

## 2026-07-29 - Listagem paginada de Locais de Estoque legados

- Criada consulta paginada read-only para `LocalizacaoEstoque` em `GET /api/localizacao-estoque/paginado`.
- Criada tela frontend em `/home/cadastro/locais-estoque`, preservando o mapa/editor hierarquico em `/home/cadastro/locais-estoque/mapa`.
- Sem migration, sem execucao de banco, sem commit e sem push.
- Validacao: `npm.cmd run build` concluido; `dotnet build PRPA.sln` bloqueado na copia final por processo `PRPA (54952)` mantendo DLLs em uso.
- 2026-07-29: Ajustada a listagem paginada do Cadastro legado de Locais de Estoque para consulta contextual por Armazem e Area de Estoque. A grid deixou de repetir Armazem/Area como colunas, preservou paginacao server-side em `GET /api/localizacao-estoque/paginado` e passou a encaminhar o contexto ao editor hierarquico. Sem alteracao backend, migration, database update, commit ou push.

- 2026-07-30: Ajustada a navegacao e a semantica dos botoes do Cadastro legado de Locais de Estoque: `Mapa hierarquico` virou `Abrir mapa`, `Novo local` passou a enviar `modo=novo`, o editor passou a diferenciar modo consulta/novo/filho/primeiro local, `Adicionar abaixo` virou `Adicionar filho`, `Criar primeiro local` aparece apenas em Area sem estrutura e o modo compacto foi reposicionado como controle de exibicao. Sem backend, API, migration, database update, commit ou push.

## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.

## Editor hierarquico de Locais de Estoque como workspace continuo - 2026-07-30

A tela `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque` foi ajustada para operar como workspace continuo de configuracao da hierarquia legada `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`.

Modos explicitos do editor: `consulta`, `edicao`, `novo-raiz`, `novo-filho` e `novo-irmao`. O modo passa a orientar titulos, mensagens, acoes e preservacao de contexto.

O contexto de Armazem e Area de Estoque deve permanecer durante selecao de no, edicao, criacao de filho, criacao de irmao, criacao de raiz, salvamento, exclusao e recarga da arvore. A troca de contexto fica explicita por selecao de outro Armazem/Area ou acao `Trocar contexto`.

Criacao de filho: preserva Armazem, Area, arvore e pai destacado; limpa somente campos proprios do novo local; preenche `localizacaoPaiId`; sugere o proximo Tipo de Localizacao; permanece na mesma tela.

Criacao de irmao: usa o mesmo pai do no selecionado, preserva contexto e arvore, sugere o mesmo Tipo de Localizacao da referencia e permanece na mesma tela.

Apos salvar novo local ou edicao, a tela recarrega a hierarquia da Area atual, seleciona o no salvo e permanece no editor. Apos excluir, a tela recarrega a hierarquia e seleciona o pai, o proximo irmao ou deixa a Area em estado vazio com acao `Criar primeiro local`.

A volta para `/home/cadastro/locais-estoque` e uma acao explicita por `Voltar para lista`, preservando query params de Armazem, Area, pagina, pageSize e filtros quando recebidos da grid.

Alteracoes nao salvas passam a solicitar confirmacao antes de selecionar outro no, adicionar filho/irmao, trocar contexto, cancelar ou voltar para a lista. Nao houve alteracao em backend, migration, `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`.
