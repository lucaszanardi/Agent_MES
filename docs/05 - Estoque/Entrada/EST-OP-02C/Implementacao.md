# EST-OP-02C — Implementação e evidências

## Fluxo real

Frontend → API → Service → MovimentoEstoque → SaldoEstoque.

1. EntradaestoqueComponent chama confirmarEntrada, monta prepararPayload e usa MovimentoEstoqueService.cadastrarMovimentoEstoque.
2. O serviço Angular envia POST para api/MovimentoEstoque/entrada-direta, conforme a base configurada em environment.urlserver.
3. MovimentoEstoqueController.PostEntradaDireta recebe MovimentoEstoqueCreateDto, mapeia para MovimentoEstoque e preenche auditoria.
4. IUnitOfWork.ExecuteAsync envolve SincronizarEntradaDiretaAsync. A infraestrutura contém BeginTransaction, SaveAsync, Commit e rollback em exceção.
5. EntradaDiretaSincronizacaoServices valida referências, lote e armazenagem; calcula conversão; adiciona movimento; busca/cria saldo pela chave lógica; incrementa físico e recalcula disponível.
6. O controller prevê resposta 201 CreatedAtRoute e BadRequest para erro retornado pela unidade de trabalho. O HTTP 500 atual permanece sem causa identificada.

O POST genérico api/MovimentoEstoque permanece separado, usando MovimentoEstoqueServices sem a sincronização específica. Não estender a garantia de entrada-direta a qualquer POST de movimento.

## Componentes e persistência

Os caminhos completos estão na seção final, agrupados pela responsabilidade.

| Camada | Nome real / responsabilidade |
|---|---|
| API | MovimentoEstoqueController e UnidadeMedidaController. |
| Aplicação | EntradaDiretaSincronizacaoServices; UnidadeMedidaServices fornece unidades recebíveis e conversões ativas/vigentes. |
| Contrato | MovimentoEstoqueCreateDto; MappingProfile ignora usuarioid legado. |
| Domínio | MovimentoEstoque, SaldoEstoque, Produto, UnidadeMedida, ConversaoUnidade, ProdutoConversaoUnidade e ClassificacaoLocalizacaoHelper. |
| EF | MovimentoEstoqueConfig → CMOVIMENTOESTOQUE; SaldoEstoqueConfig → CSALDOESTOQUE; ConversaoUnidadeConfig → CCONVERSAOUNIDADE; ProdutoConversaoUnidadeConfig → CPRODUTOCONVERSAOUNIDADE. |
| Angular | EntradaestoqueComponent, template HTML, MovimentoEstoqueService e UnidadeMedidaService. |
| Testes | EntradaDiretaSincronizacaoTestScenarios, com sete Facts usando mocks. |

Migrations inspecionadas: 20260621235659_saldoestoque e 20260622000002_movimentoestoque criam as tabelas legadas; 20260904185516_AddConversaoUnidadeAndDimension adiciona dimensão, conversões globais e campos históricos; 20260904234311_AddProdutoConversaoUnidade adiciona conversão por produto e referência no movimento. Existência no repositório não comprova aplicação no banco atual. Nenhuma migration foi executada nesta tarefa.

## Sub-marcos

| Marco | Estado real |
|---|---|
| EST-OP-02C.1 — Entrada Direta → Movimento + Saldo | Implementado no endpoint específico. Validação anterior informada pelo solicitante; o relatório 1-V arquivado registra API indisponível e runtime não executado naquela verificação. |
| EST-OP-02C.1B — Unidade de Estoque automática | Implementada na tela a partir de Produto.UnidadeMedidaId e no serviço como unidade oficial do saldo. |
| EST-OP-02C.1C — Conversões globais | Modelo, migration, consultas e cálculo implementados. Validação anterior informada pelo solicitante; evidência autônoma de execução runtime do 1C não identificada nos relatórios inspecionados. |
| EST-OP-02C.1D — Conversões por produto | Implementação localizada, com prioridade sobre global. Relatório registra builds PASS e 7/7 testes PASS; runtime pendente. Há divergência de contrato e HTTP 500 informado atualmente. |
| EST-OP-02C.2 — UL opcional | Planejado após estabilização/homologação do 1D; não implementado na Entrada. |

## Divergência crítica: payload, DTO e cálculo

prepararPayload envia quantidade = quantidadeEstoqueCalculada e unidademedidaid = unidade de estoque. Também envia quantidadeInformada, unidadeInformadaId, quantidadeEstoque, unidadeEstoqueId, fatorConversaoAplicado, origemConversao e referências.

MovimentoEstoqueCreateDto não declara esses campos adicionais. O serviço usa os campos legados recebidos para decidir e recalcular. Portanto, a análise estática indica que a tela conduz ao ramo de mesma unidade, que grava fator 1 e origem NENHUMA com a quantidade já convertida.

Exemplo conceitual: 2 CX de um produto com 24 UN/CX produzem na tela quantidade = 48 e unidademedidaid = UN. Nesse contrato, o backend não recebe pelo DTO os 2 CX originais e tende a registrar 48 UN como quantidade informada, fator 1 e origem NENHUMA. Isso diverge da preservação do original e do cálculo autoritativo previstos. O achado não estabelece a causa do HTTP 500.

## Testes, garantias e lacunas

Os sete Facts encontrados verificam criação/incremento de saldo, quantidade inválida, lote obrigatório, localização estrutural, um placeholder de atomicidade e um caso nominal de ausência de UL. O último verifica lote nulo, não ausência de persistência de UL. O caso de rollback executa Assert.True(true), sem falha de persistência simulada.

Não foram identificados testes específicos de fator por produto, fallback global, prioridade, vigência, dimensões incompatíveis ou preservação histórica com unidades diferentes. A busca por conversões em App.Domain.Tests encontrou apenas os mocks de preparação desse arquivo. O PASS 7/7 do relatório é histórico e não demonstra cobertura funcional completa do 1D. Testes e builds não foram reexecutados.

O mapping do saldo não define unicidade composta nem token de concorrência; a busca usa o primeiro saldo se houver mais de um. Idempotência do endpoint não identificada. O validator específico existe, mas não é chamado pelo endpoint inspecionado. MovimentoEstoque mantém CRUD, portanto imutabilidade integral não está comprovada. A leitura operacional do mapa usa UL e não incorpora SaldoEstoque.

## Evidências / Referências

- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `BACKEND/PRPA/App.Infra.CrossCutting.IoC/MappingProfile.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/UnidadeMedida.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ConversaoUnidade.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoConversaoUnidade.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ClassificacaoLocalizacaoHelper.cs`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.html`
- `BACKEND/PRPA/App.Domain.Tests/EntradaDiretaSincronizacaoTestScenarios.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1_RESULTADO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904185516_AddConversaoUnidadeAndDimension.cs`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904234311_AddProdutoConversaoUnidade.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`
- `BACKEND/PRPA/PRPA/Controllers/UnidadeMedidaController.cs`
- `BACKEND/PRPA/App.Service/Services/UnidadeMedidaServices.cs`
- `BACKEND/PRPA/App.Service/Validators/EntradaDiretaValidator.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/UnitOfWork.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/MovimentoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/SaldoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/ConversaoUnidadeConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/ProdutoConversaoUnidadeConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260621235659_saldoestoque.cs`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260622000002_movimentoestoque.cs`
- `FRONTEND/src/app/application/operacao/services/movimentoestoque.service.ts`
- `FRONTEND/src/app/application/cadastro/unidademedida/services/unidademedida.service.ts`
