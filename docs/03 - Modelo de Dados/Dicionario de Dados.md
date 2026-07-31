
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
## Regra hierarquica de armazenagem dos locais legados - 2026-07-30

A regra se aplica somente ao legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`. Nao altera `LocalDeEstoque`, nao cria migration, nao executa `database update` e nao adiciona coluna persistida de armazenagem.

Semantica implementada: local com filhos e sempre estrutural e nao recebe armazenagem direta; local folha pode armazenar apenas quando nao esta bloqueado e seu `TipoLocalizacao.permitearmazenagem` indica que o tipo pode encerrar a hierarquia; local folha bloqueado fica bloqueado; local folha cujo tipo nao permite terminal fica como `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deve ser interpretado como permissao do tipo para ser terminal, nao como garantia isolada de armazenagem em qualquer no. A armazenagem real e calculada em runtime por `LocalizacaoEstoqueServices.GetArvorePorAreaAsync` e pela consulta paginada `LocalizacaoEstoqueConsultaService.SearchAsync`.

Ao criar filho, o pai passa a ser classificado como estrutural por possuir filhos. Ao excluir filho, a classificacao do pai e recalculada nas proximas leituras; a exclusao de localizacao que ainda possui filhos e bloqueada no service legado.

Diagnostico de dados: nao foi executada correcao automatica nem script de banco. Inconsistencias existentes devem ser avaliadas por consulta read-only antes de qualquer normalizacao operacional.
