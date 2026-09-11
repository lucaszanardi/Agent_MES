# EST-OP-02C.1D — Conversões por produto

**IMPLEMENTAÇÃO: concluída conforme código do sub-marco, com divergência de integração documentada.**  
**TESTES AUTOMATIZADOS: 7/7 PASS reportados anteriormente; cobertura de conversão por produto não identificada nos cenários inspecionados.**  
**RUNTIME: PENDENTE / HTTP 500 relatado, com investigação da causa raiz ainda pendente.**

“Implementação concluída” designa a presença dos componentes do 1D; não significa homologação funcional nem encerramento do EST-OP-02C.

## Modelo real

A entidade é ProdutoConversaoUnidade, com ProdutoId, UnidadeOrigemId, UnidadeDestinoId, Fator decimal, Ativo (padrão true), VigenciaInicio e VigenciaFim opcionais, navegações e campos herdados de BaseEntity.

ProdutoConversaoUnidadeConfig mapeia CPRODUTOCONVERSAOUNIDADE, fator decimal(18,6), relações com produto/unidades e índice ProdutoId + UnidadeOrigemId + UnidadeDestinoId + Ativo, sem unicidade. A migration 20260904234311_AddProdutoConversaoUnidade cria essa tabela e adiciona produtoConversaoUnidadeId ao movimento. Aplicação no banco atual não identificada nesta análise.

## Regra implementada no serviço

1. Mesma unidade → fator 1 e origem NENHUMA.
2. Conversão específica do produto, ativa e vigente → origem PRODUTO.
3. Fallback para conversão global, ativa, vigente e dimensionalmente compatível → origem GLOBAL.
4. Sem conversão → rejeitar.

A conversão específica tem prioridade sobre a global. O serviço compara a vigência com DateTime.Now, usando limites inclusivos. O ramo específico não exige igualdade de Dimension, permitindo relações próprias de embalagem/conteúdo. Não há conversão automática inversa, encadeamento ou desempate explícito entre múltiplas regras elegíveis. Validação explícita de fator positivo nesse serviço não foi identificada.

Exemplos conceituais, não dados persistidos:

| Produto | Relação específica |
|---|---|
| Produto A | 1 CX = 24 UN |
| Produto B | 1 FARDO = 12 KG |
| Produto C | 1 ROLO = 50 M |

## Histórico que o movimento deve preservar

| Informação | Campo real |
|---|---|
| Quantidade original | quantidadeInformada |
| Unidade original | unidadeInformadaId |
| Quantidade de estoque | quantidadeEstoque |
| Unidade de estoque | unidadeEstoqueId |
| Fator aplicado | fatorConversaoAplicado |
| Origem | origemConversao |
| Regra específica usada | produtoConversaoUnidadeId |
| Regra global usada no fallback | conversaoUnidadeId |

No ramo PRODUTO, o serviço preenche o snapshot e limpa conversaoUnidadeId; no ramo GLOBAL faz o inverso. Alterar o fator cadastral não recalcula automaticamente esses campos históricos pelo fluxo de entrada inspecionado. Isso não equivale a imutabilidade integral: o controller ainda oferece atualização/exclusão de movimento.

## Integração e divergência encontrada

A tela usa UnidadeMedidaService.getUnidadesRecebiveisPorProduto e o endpoint GET api/UnidadeMedida/unidades-recebiveis-produto/{produtoId}; recebe a unidade oficial, unidades recebíveis e as duas listas de conversão. A prévia usa prioridade produto > global.

Entretanto, prepararPayload envia quantidade/unidademedidaid já convertidos e os originais em campos adicionais ausentes do MovimentoEstoqueCreateDto. O serviço decide a partir dos campos legados: a análise estática indica ramo de mesma unidade, fator 1 e perda da quantidade/unidade originais no fluxo da tela. O backend contém o algoritmo de conversão, mas a integração não comprova o recálculo autoritativo desejado. Essa divergência não estabelece a causa do HTTP 500.

## Testes e runtime

EST-OP-02C.1D_RESULTADO_FINAL.md reporta backend/frontend build PASS, 7/7 testes PASS e runtime não validado por API indisponível. O estado atual informado pelo solicitante é HTTP 500 na confirmação; não está homologado.

EntradaDiretaSincronizacaoTestScenarios contém sete Facts sobre saldo, quantidade inválida, lote, localização, placeholder de rollback e caso nominal sem UL. Os testes usam mesma unidade e mock de conversão específica retornando null. Não foram identificados casos de prioridade, fator por produto, fallback ou histórico convertido. O teste de rollback usa Assert.True(true), sem provar atomicidade.

Nenhum build, teste ou runtime foi executado nesta tarefa documental. Causa raiz do HTTP 500: não identificada.

## Próximos limites

Investigar o erro, estabilizar o contrato e homologar a conversão por produto em runtime antes de avançar. Próximo marco pretendido: EST-OP-02C.2 — UL opcional na Entrada; quarentena permanece planejada para depois.

Conversão por lote não será implementada agora. Permanece extensão futura para densidade, umidade ou propriedades específicas do lote. Produção/BOM não integra o escopo de conversão.

## Evidências / Referências

- `BACKEND/PRPA/App.Domain/Entities/PRPA/ProdutoConversaoUnidade.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `BACKEND/PRPA/App.Domain.Tests/EntradaDiretaSincronizacaoTestScenarios.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904234311_AddProdutoConversaoUnidade.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/ProdutoConversaoUnidadeConfig.cs`
- `BACKEND/PRPA/PRPA/Controllers/UnidadeMedidaController.cs`
- `BACKEND/PRPA/App.Service/Services/UnidadeMedidaServices.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `FRONTEND/src/app/application/cadastro/unidademedida/services/unidademedida.service.ts`
