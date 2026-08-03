
## Consulta operacional read-only da nova vertical de Estoque

Implementacao comprovada em `BACKEND/PRPA/PRPA/Controllers/EstoqueUnidadesLogisticasController.cs`, `BACKEND/PRPA/PRPA/Controllers/EstoqueLocaisController.cs` e `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`.

Endpoints criados:

- `GET /api/estoque/unidades-logisticas/{id}`
- `GET /api/estoque/unidades-logisticas?termo={termo}&page={page}&pageSize={pageSize}`
- `GET /api/estoque/unidades-logisticas/{id}/movimentacoes`
- `GET /api/estoque/locais/{id}`
- `GET /api/estoque/locais?termo={termo}&page={page}&pageSize={pageSize}`
- `GET /api/estoque/locais/{id}/unidades-logisticas`

Campos retornados conforme modelo atual:

- Unidade Logistica: id, codigo, produtoId, quantidade, unidadeMedidaId, status, versao, local atual id/codigo, plantId, warehouseId, dataCriacao e indicador de movimentacao ativa.
- Movimentacao recente da UL: id, origem id/codigo, destino id/codigo, status, dataSolicitacao, dataConfirmacao, versao e correlationId.
- Local de Estoque: id, codigo, status, versao, plantId, warehouseId e quantidade de ULs associadas.

Limitacoes documentadas: o agregado `LocalDeEstoque` nao possui descricao, tipo, hierarquia fisica, local pai, capacidade ou ocupacao; o agregado `UnidadeLogistica` nao possui descricao, tipo ou data de atualizacao. A consulta nao usa `LocalizacaoEstoque`, `MovimentoEstoque` ou `SaldoEstoque` legados como fonte de verdade e nao executa escrita.

Permissoes sugeridas para cadastro operacional/menu, sem insercao em banco nesta etapa: `estoque.unidade-logistica.consultar`, `estoque.unidade-logistica.historico`, `estoque.local.consultar`, `estoque.local.conteudo`.
## Regras tecnicas de autorizacao da vertical atual - 2026-07-29

| Endpoint | Permissao backend |
|---|---|
| `GET /api/estoque/unidades-logisticas` | `estoque.unidade-logistica.consultar` |
| `GET /api/estoque/unidades-logisticas/{id}` | `estoque.unidade-logistica.consultar` |
| `GET /api/estoque/unidades-logisticas/{id}/movimentacoes` | `estoque.unidade-logistica.historico` |
| `GET /api/estoque/locais` | `estoque.local.consultar` |
| `GET /api/estoque/locais/{id}` | `estoque.local.consultar` |
| `GET /api/estoque/locais/{id}/unidades-logisticas` | `estoque.local.conteudo` |
| `GET /api/estoque/movimentacoes/{id}` | `estoque.movimentacao.consultar` |
| `POST /api/estoque/movimentacoes` | `estoque.movimentacao.criar` |
| `POST /api/estoque/movimentacoes/{id}/confirmacao` | `estoque.movimentacao.confirmar` |

As policies preservam 401 para requisicao sem autenticacao e retornam 403 para usuario autenticado sem a permissao/role correspondente. A visibilidade do menu nao substitui a autorizacao backend.
## Regras observadas no cadastro legado de locais - 2026-07-29

- Localizacoes de Estoque legadas pertencem a uma Area de Estoque e podem ter `localizacaopaiid` nulo para representar raiz.
- A tela deve carregar Areas ao selecionar Armazem e carregar a hierarquia imediatamente ao selecionar Area.
- Apos salvar, o frontend deve recarregar a hierarquia da mesma Area e selecionar o Local de Estoque salvo.
- O status `ocupado` nao deve ser simulado: somente pode ser exibido quando houver dado real retornado pela API; caso contrario, a tela usa `bloqueada` e permissao de armazenagem/tipo para indicar bloqueado, estrutural ou livre.
- Esta regra se aplica ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE` e nao altera o aggregate root `LocalDeEstoque` da nova vertical.

## Regras observadas na listagem paginada de locais legados - 2026-07-29

- A listagem operacional do cadastro legado deve consultar `CLOCALIZACAOESTOQUE` de forma paginada no backend, sem carregar toda a tabela no frontend.
- O endpoint paginado exige autenticacao por `[Authorize]`; nao foi criada policy granular especifica para esta rota nesta etapa.
- O endpoint legado `GET /api/localizacao-estoque` continua anonimo e inalterado para preservar consumidores existentes.
- O campo de status real nao existe em `LocalizacaoEstoque`; a listagem usa `bloqueada` e `TipoLocalizacao.permitearmazenagem` para indicar bloqueio/armazenagem sem simular ocupacao.
- A consulta paginada e read-only e nao sincroniza registros com `LocalDeEstoque`, `UnidadeLogistica`, `SaldoEstoque` ou `MovimentacaoDeEstoque` da nova vertical.
## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.

## Decisao aprovada - Finalidade configurada dos Locais de Estoque legados - 2026-07-31

Aprovada em `AS-0008` e formalizada em `DL-0042`. Resumo de regras aprovadas:

- `LocalizacaoEstoque` passara a ter finalidade configurada pelo operador (`Estrutural`/`Armazenagem`), persistida em nova coluna `finalidadelocalizacao` (smallint, NOT NULL, default `Estrutural`) em `CLOCALIZACAOESTOQUE`.
- A classificacao efetiva (`ESTRUTURAL`/`ARMAZENA`/`BLOQUEADO`) permanecera calculada em runtime, **nao persistida**.
- Regra efetiva:
  1. Se `bloqueada=true`: classificacao = `BLOQUEADO`.
  2. Se possuir filhos: classificacao = `ESTRUTURAL`.
  3. Se folha com `finalidade=Armazenagem`: classificacao = `ARMAZENA`.
  4. Se folha com `finalidade=Estrutural`: classificacao = `ESTRUTURAL`.
- O estado `REQUER_FILHO` sera eliminado em todas as camadas (backend, frontend, DTOs, badges).
- `TipoLocalizacao.permitearmazenagem` passa a funcionar apenas como sugestao inicial de finalidade para novos locais.
- O usuario pode alterar a finalidade enquanto o local for folha (sem filhos).
- Um local com filhos nunca armazena efetivamente; a finalidade `Armazenagem` pode permanecer persistida enquanto houver filhos.
- Ao perder o ultimo filho, o local volta a seguir a finalidade persistida.
- Diferentes ramos da mesma `AreaEstoque` podem terminar em profundidades diferentes.
- Excecao controlada, aditiva e pontual ao congelamento semantico do legado aprovado em `DL-0041`.
- Sem alteracao em `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`.
- Contrato tecnico: `docs/05 - Estoque/Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`.

Implementacao pendente: nenhum codigo, migration, script SQL, endpoint, frontend ou `database update` foi aplicado nesta etapa documental. Aplicacao requer validacao humana previa conforme DL-0042.

## Editor hierarquico de Locais de Estoque como workspace continuo - 2026-07-30

A tela `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque` foi ajustada para operar como workspace continuo de configuracao da hierarquia legada `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`.

Modos explicitos do editor: `consulta`, `edicao`, `novo-raiz`, `novo-filho` e `novo-irmao`. O modo passa a orientar titulos, mensagens, acoes e preservacao de contexto.

O contexto de Armazem e Area de Estoque deve permanecer durante selecao de no, edicao, criacao de filho, criacao de irmao, criacao de raiz, salvamento, exclusao e recarga da arvore. A troca de contexto fica explicita por selecao de outro Armazem/Area ou acao `Trocar contexto`.

Criacao de filho: preserva Armazem, Area, arvore e pai destacado; limpa somente campos proprios do novo local; preenche `localizacaoPaiId`; sugere o proximo Tipo de Localizacao; permanece na mesma tela.

Criacao de irmao: usa o mesmo pai do no selecionado, preserva contexto e arvore, sugere o mesmo Tipo de Localizacao da referencia e permanece na mesma tela.

Apos salvar novo local ou edicao, a tela recarrega a hierarquia da Area atual, seleciona o no salvo e permanece no editor. Apos excluir, a tela recarrega a hierarquia e seleciona o pai, o proximo irmao ou deixa a Area em estado vazio com acao `Criar primeiro local`.

A volta para `/home/cadastro/locais-estoque` e uma acao explicita por `Voltar para lista`, preservando query params de Armazem, Area, pagina, pageSize e filtros quando recebidos da grid.

Alteracoes nao salvas passam a solicitar confirmacao antes de selecionar outro no, adicionar filho/irmao, trocar contexto, cancelar ou voltar para a lista. Nao houve alteracao em backend, migration, `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`.

## Identidade unica e elegibilidade operacional - 2026-08-03

AS-0010 e DL-0044 aprovam `LocalizacaoEstoque` como identidade unica fisica e operacional dos enderecos de estoque.

Regras documentais:

- somente classificacao efetiva `ARMAZENA` pode receber Unidade Logistica;
- `ESTRUTURAL` nao recebe estoque e nao pode ser origem/destino;
- `BLOQUEADO` nao recebe estoque e nao pode ser origem/destino;
- entrada exige `permiteentrada = true`, capacidade disponivel quando aplicavel e escopo valido de Planta/Almoxarifado;
- saida exige `permitesaida = true`, existencia da UL na localizacao e ausencia de impedimento operacional;
- uso produtivo deve respeitar `permiteproducao` e dependera de detalhamento futuro do modulo de Producao;
- alteracao hierarquica que tornaria uma localizacao armazenadora em estrutural deve ser bloqueada se houver dependencia operacional.

Nenhuma implementacao foi realizada nesta atualizacao documental.
