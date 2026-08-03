
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
## Cadastro legado de enderecamento fisico - 2026-07-29

A tela de Cadastro de Locais de Estoque em `FRONTEND/src/app/application/cadastro/localizacaoestoque/**` usa o modelo legado `LocalizacaoEstoque` e grava em `CLOCALIZACAOESTOQUE` por meio de `POST /api/localizacao-estoque`. A hierarquia exibida na tela e recarregada por area com `GET /api/localizacao-estoque/por-area/{areaEstoqueId}`.

Comportamento pos-salvamento documentado: manter Armazem e Area selecionados, recarregar a hierarquia, selecionar o local salvo e manter a tela em modo de edicao. Nao ha sincronizacao automatica entre `LocalizacaoEstoque` legado e `LocalDeEstoque` da nova vertical (`CLOCALDEESTOQUE`).

Nota de arquitetura - Planta aprovada: AS-0009 e DL-0043 definem que `Almoxarifado` representa Armazem/Warehouse e que `WarehouseId` equivale a `AlmoxarifadoId`. `PlantId` deve ser derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`, sem repeticao manual em cada localizacao. A implementacao do vinculo `Almoxarifado -> Planta` permanece pendente.

## Listagem paginada do cadastro legado de locais - 2026-07-29

Implementacao comprovada em `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`, `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoque/Consultas/LocalizacaoEstoqueConsultaContracts.cs`, `BACKEND/PRPA/App.Infra.Data/Persistence/LocalizacaoEstoque/LocalizacaoEstoqueConsultaService.cs` e `FRONTEND/src/app/application/cadastro/localizacaoestoque/**`.

Foi criada consulta paginada read-only para `CLOCALIZACAOESTOQUE` em `GET /api/localizacao-estoque/paginado`, com filtros por codigo, nome, armazemId, areaEstoqueId, tipoLocalizacaoId, flags operacionais, bloqueio e permissao de armazenagem. O endpoint legado `GET /api/localizacao-estoque` permanece retornando lista completa para compatibilidade com telas operacionais existentes.

A rota frontend recomendada para menu dinamico e `/home/cadastro/locais-estoque`. A rota `/home/cadastro/listlocalizacaoestoque` foi mantida como compatibilidade. O editor hierarquico especializado fica em `/home/cadastro/locais-estoque/mapa` e `/home/cadastro/locais-estoque/mapa/:id`.
## Listagem contextual do cadastro legado de locais - 2026-07-29

A rota frontend `/home/cadastro/locais-estoque` agora consulta `LocalizacaoEstoque` por contexto de Armazem e Area de Estoque. A Area fica desabilitada ate a selecao do Armazem; ao trocar Armazem, Area e grid sao limpas; ao selecionar Area, a grid carrega automaticamente os Locais daquela combinacao.

A grid final removeu as colunas Armazem e Area e manteve Codigo, Nome, Tipo, Caminho, Capacidade, Armazenagem, Bloqueado e Acoes. Os filtros finais sao Codigo, Nome, Tipo, Armazenagem, Bloqueado e pesquisa rapida, sempre dentro do contexto selecionado. Limpar filtros nao limpa o contexto.

A paginacao continua server-side em `GET /api/localizacao-estoque/paginado`, enviando `armazemId`, `areaEstoqueId`, `page`, `pageSize`, filtros e ordenacao. O editor hierarquico permanece em `/home/cadastro/locais-estoque/mapa` e recebe o contexto por query string para Novo local, Editar e Mapa. O modelo segue sendo `LocalizacaoEstoque` legado em `CLOCALIZACAOESTOQUE`, sem uso de `LocalDeEstoque` da nova vertical.

## Navegacao e semantica do editor hierarquico - 2026-07-30

A listagem de Locais de Estoque agora diferencia `Abrir mapa` e `Novo local`. `Abrir mapa` navega para `/home/cadastro/locais-estoque/mapa` sem iniciar criacao; quando houver Armazem e Area selecionados, esses valores seguem por query string. `Novo local` exige o contexto completo e navega para o mesmo editor com `modo=novo`, mantendo Armazem e Area.

No editor, o cabecalho principal deixou de exibir `Novo local raiz` e `Modo compacto` como acoes principais. O modo compacto foi preservado como controle secundario de exibicao `Confortavel/Compacta`, pois altera campos e layout visiveis. Em Area sem locais, o estado vazio exibe `Criar primeiro local`, que inicia um local raiz com `localizacaoPaiId` nulo. Em Area com estrutura, o usuario seleciona um no e usa `Adicionar filho`, preservando Armazem/Area e sugerindo o proximo tipo pela regra existente. Como o backend legado monta multiplas raizes e nao foi identificada regra de raiz unica, `Criar nova raiz` permanece como acao secundaria.

## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.

## Decisao aprovada - Finalidade configurada dos Locais de Estoque legados - 2026-07-31

Aprovada em `AS-0008` e formalizada em `DL-0042`. Resumo para enderecamento:

- `LocalizacaoEstoque` passara a ter finalidade configurada (`Estrutural`/`Armazenagem`) persistida em nova coluna `finalidadelocalizacao` de `CLOCALIZACAOESTOQUE` (smallint, NOT NULL, default `Estrutural`).
- A classificacao efetiva (`ESTRUTURAL`/`ARMAZENA`/`BLOQUEADO`) permanecera calculada em runtime, **nao persistida**.
- O estado `REQUER_FILHO` sera eliminado em arvore, grid, filtros, DTOs e frontend.
- `TipoLocalizacao.permitearmazenagem` passa a ser apenas sugestao inicial na criacao de novos locais.
- Permite, dentro de uma mesma `AreaEstoque`, ramos com profundidades variaveis e folhas com finalidades diferentes (ex.: `RUA1 -> COLUNA1 (ARMAZENA)`, `RUA2 -> COLUNA2 (ARMAZENA)`, `RUA3 -> COLUNA3 -> ANDAR1 (ARMAZENA)`, com `COLUNA3` estrutural pai de `ANDAR1`).
- Regra efetiva: bloqueado -> `BLOQUEADO`; com filhos -> `ESTRUTURAL`; folha + `Armazenagem` -> `ARMAZENA`; folha + `Estrutural` -> `ESTRUTURAL`.
- Um local com filhos nunca armazena efetivamente; finalidade `Armazenagem` pode permanecer persistida enquanto houver filhos; ao perder o ultimo filho, o local volta a seguir a finalidade persistida.
- Excecao controlada, aditiva e pontual ao congelamento semantico aprovado em `DL-0041`.
- Sem alteracao em `CLOCALDEESTOQUE`, `UnidadeLogistica` ou `MovimentacaoDeEstoque`.
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

## Identidade unica operacional - AS-0010

`LocalizacaoEstoque` passa a ser a identidade unica fisica e operacional dos enderecos de estoque. O cadastro hierarquico deixa de ser etapa apenas cadastral e passa a ser a fonte conceitual para posicionamento de UL e origem/destino de movimentacoes.

A elegibilidade operacional continua derivada da classificacao efetiva: apenas `ARMAZENA` pode armazenar. Localizacoes `ESTRUTURAL` ou `BLOQUEADO` nao podem receber UL nem participar de movimentacao.

Antes de adicionar filho sob localizacao `ARMAZENA`, a implementacao futura devera validar ausencia de UL, saldo, movimentacao ativa, reserva, inventario em andamento e bloqueio operacional incompativel.
