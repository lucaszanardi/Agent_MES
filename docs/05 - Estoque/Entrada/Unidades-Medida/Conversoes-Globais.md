# EST-OP-02C.1C — Conversões globais

## Modelo implementado

ConversaoUnidade contém UnidadeOrigemId, UnidadeDestinoId, Fator (decimal), Ativo (padrão true), VigenciaInicio e VigenciaFim opcionais, além das navegações de unidade e campos herdados de BaseEntity. ConversaoUnidadeConfig mapeia CCONVERSAOUNIDADE, fator decimal(18,6) e índice de origem/destino/ativo sem unicidade.

UnidadeMedidaDimension está declarado em UnidadeMedida.cs. A propriedade Dimension classifica a unidade em QUANTIDADE, MASSA, COMPRIMENTO, VOLUME ou AREA. O serviço verifica igualdade de dimensão no ramo global quando ambas as unidades são localizadas.

A migration 20260904185516_AddConversaoUnidadeAndDimension cria a tabela, acrescenta Dimension e os campos de conversão do movimento. A migration usa default 0 para Dimension, enquanto a entidade usa QUANTIDADE = 1. Classificação das unidades existentes no banco atual: não identificada; não presumir que todas foram corrigidas ou classificadas. A migration não foi executada nesta tarefa.

## Resolução e prioridade

1. Mesma unidade de recebimento e estoque: fator 1, origem NENHUMA.
2. Conversão específica ativa/vigente do produto: prioridade sobre global.
3. Conversão global ativa/vigente da origem para a unidade de estoque: fallback.
4. Nenhuma regra: serviço rejeita.

Vigência usa DateTime.Now, com limites inclusivos; não a data do movimento. A busca é direta e orientada origem → destino. Conversão inversa automática e encadeamento de várias regras não foram identificados.

Não há campo Prioridade na entidade: prioridade é a ordem do algoritmo. Havendo múltiplas regras elegíveis, o serviço usa FirstByAsync sem desempate explícito; o índice encontrado não impede duplicidade. Validação explícita de Fator > 0 nesse serviço não foi identificada.

## Relações adequadas

Exemplos conceituais, sem comprovação de registros cadastrados:

| Origem → destino | Fator |
|---|---:|
| TON → KG | 1000 |
| KG → G | 1000 |
| M → CM | 100 |
| L → ML | 1000 |

Conversão global serve para relações dimensionais universais. Não deve representar uma embalagem específica de um produto, como caixa com conteúdo variável entre produtos.

## Consulta e histórico

UnidadeMedidaController expõe GET api/UnidadeMedida/conversoes, unidades-recebiveis/{unidadeEstoqueId} e unidades-recebiveis-produto/{produtoId}. UnidadeMedidaServices monta as unidades recebíveis; a consulta por produto reúne regras globais e específicas. As consultas recebíveis filtram ativo/vigência, mas não filtram compatibilidade dimensional; essa validação aparece no serviço de confirmação. A listagem geral de conversões filtra apenas Ativo.

Quando o ramo global é executado, o serviço registra quantidadeInformada, unidadeInformadaId, quantidadeEstoque, unidadeEstoqueId, fatorConversaoAplicado, origemConversao = GLOBAL e conversaoUnidadeId; atualiza os campos legados para a unidade de estoque. Guardar o fator aplicado evita depender do fator atual do catálogo para interpretar o movimento antigo.

A rastreabilidade não está garantida de ponta a ponta pela tela atual: o DTO não recebe os campos originais adicionais e a tela envia os campos legados já convertidos. Imutabilidade integral também não está comprovada, pois há CRUD de movimento.

## Evidência de validação e pendências

Implementação do 1C localizada no código; validação anterior informada pelo solicitante. Não foi identificada evidência autônoma de execução runtime do 1C nos relatórios inspecionados. O relatório 1D informa builds/testes PASS, mas os testes encontrados não exercitam conversão global com fator diferente de 1.

O fluxo atual apresenta HTTP 500 relatado pelo solicitante e permanece pendente de investigação/homologação; não atribuir esse erro à migration, dimensão ou conversão sem evidência runtime.

## Evidências / Referências

- `BACKEND/PRPA/App.Domain/Entities/PRPA/ConversaoUnidade.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/UnidadeMedida.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904185516_AddConversaoUnidadeAndDimension.cs`
- `BACKEND/PRPA/App.Domain.Tests/EntradaDiretaSincronizacaoTestScenarios.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Infra.Data/Mapping/ConversaoUnidadeConfig.cs`
- `BACKEND/PRPA/PRPA/Controllers/UnidadeMedidaController.cs`
- `BACKEND/PRPA/App.Service/Services/UnidadeMedidaServices.cs`
