# Unidades de medida — Arquitetura

## Conceitos

| Conceito | Significado / implementação |
|---|---|
| Unidade de Estoque | Unidade oficial do saldo do produto: Produto.UnidadeMedidaId no MVP. |
| Unidade Recebida | Unidade da operação/documento. UI: unidadeRecebidaId; histórico: unidadeInformadaId. |
| Quantidade Recebida | Quantidade original da operação. UI: quantidadeRecebida; histórico: quantidadeInformada. |
| Quantidade de Estoque | Quantidade convertida para a unidade oficial. Histórico: quantidadeEstoque; incrementa qtdfisica. |
| Fator aplicado | Multiplicador persistido em fatorConversaoAplicado, com origemConversao e referência da regra utilizada. |

Quando aplicável: **QuantidadeEstoque = QuantidadeRecebida × FatorConversao**.

Mesma unidade usa fator 1. A prioridade implementada no serviço é mesma unidade → específica do produto → global → rejeição. O saldo é mantido na unidade de estoque, independentemente da unidade recebida.

## Cálculo e persistência

O backend deve recalcular; a prévia Angular não deve ser fonte de verdade. O serviço possui esse cálculo, mas o contrato atual da tela envia quantidade/unidade já convertidas nos campos legados e o DTO não contém os campos originais adicionais. A preservação dos dados originais no fluxo completo não está garantida; ver Implementacao.md do 02C.

| Aspecto | Estado encontrado |
|---|---|
| Cálculo backend | decimal em C#; multiplicação sem arredondamento explícito por CasasDecimais no serviço de entrada. |
| Quantidades no banco | decimal(18,4) nos mappings de movimento e saldo. |
| Fatores no banco | decimal(18,6) nas conversões e no fator histórico do movimento. |
| CasasDecimais | UnidadeMedida possui padrão 3; validator aceita 0 a 10. |
| Exibição | Template usa toFixed(unidadeEstoqueSelecionada.casasDecimais) na prévia. |
| UI numérica | number em TypeScript; mínimo do formulário 0,000001 e step HTML 0,0001. |

CasasDecimais não muda a escala física do banco. Não afirmar que uma configuração de 10 casas garante persistência de 10 casas. A especificação histórica 1A propõe fatores com decimal(18,10), mas o mapping implementado utiliza decimal(18,6). Política completa de arredondamento por unidade não identificada.

## Conversão versus produção

TON → KG é conversão de unidade: representa a mesma quantidade física em outra unidade.

Um pallet produzido consumindo m³ de madeira representa transformação/consumo de BOM ou receita. Não é conversão de unidade; envolve produtos e processo produtivo. Produção está fora do escopo atual.

Embalagens com conteúdo específico devem usar conversão por produto, sem transformar sua composição em regra universal.

## Extensões e estado

Conversão por lote não será implementada agora. É extensão futura para densidade, umidade ou propriedades específicas do lote. A presença de uma opção futura em modelo conceitual não significa algoritmo implementado.

UL opcional e quarentena permanecem planejadas na Entrada. O 1D está bloqueado para homologação runtime devido ao HTTP 500 relatado pelo solicitante, cuja causa raiz é não identificada; builds PASS históricos não eliminam esse bloqueio.

## Evidências / Referências

- `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/UnidadeMedida.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.html`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1A_ESPECIFICACAO_UNIDADES_MEDIDA_ESTOQUE.md`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904185516_AddConversaoUnidadeAndDimension.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Service/Validators/UnidadeMedidaValidator.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/MovimentoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/SaldoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/ConversaoUnidadeConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/ProdutoConversaoUnidadeConfig.cs`
