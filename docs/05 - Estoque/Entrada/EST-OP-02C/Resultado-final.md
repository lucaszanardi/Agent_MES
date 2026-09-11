# EST-OP-02C — Resultado e pendências

**STATUS: EM IMPLEMENTAÇÃO / BLOQUEADO PARA HOMOLOGAÇÃO RUNTIME DO 1D**

O EST-OP-02C inteiro não está concluído.

## Estado consolidado

| Entrega | Evidência e estado |
|---|---|
| Entrada direta → Movimento + Saldo | Implementada. Validação anterior informada pelo solicitante; o relatório 1-V arquivado registrou runtime indisponível naquela verificação. |
| Unidade de estoque automática | Implementada com Produto.UnidadeMedidaId. |
| Conversão global | Implementada; validação anterior informada pelo solicitante. Esta análise não reproduziu essa validação. |
| Conversão por produto | Implementação localizada, incluindo migration, consulta e prioridade. Homologação runtime pendente. |
| Builds/testes | Relatório 1D informa backend e frontend PASS e 7/7 testes PASS. Não reexecutados nesta tarefa; cobertura efetiva tem limites. |
| Runtime atual | HTTP 500 ao confirmar uma Entrada, conforme relato do solicitante nesta tarefa. Causa raiz não identificada; investigação pendente. |
| UL na Entrada | Planejada, não implementada. |
| Quarentena na Entrada | Planejada, não implementada. |

## Bloqueios e divergências

- O relatório 1D informa “ready” para próximos marcos, mas registra runtime não validado. O HTTP 500 atual impede adotar essa prontidão como homologação.
- Payload convertido e DTO sem campos originais divergem da conversão autoritativa e da preservação do histórico. A análise estática indica fator 1/origem NENHUMA no fluxo da tela. Isso não prova a causa do HTTP 500.
- Sete testes PASS não comprovam conversões por produto: os casos encontrados usam mesma unidade; rollback é placeholder e o teste nominal de UL apenas verifica lote nulo.
- Imutabilidade histórica completa não foi identificada, pois MovimentoEstoque possui atualização/exclusão.
- Mapa continua com leitura de UL no serviço inspecionado, sem SaldoEstoque.
- Unicidade composta, controle de concorrência do saldo e idempotência do endpoint permanecem sem implementação identificada nos pontos auditados.
- DL-0022 define saldo como projeção; a diretriz quantitativa do MVP difere desse registro. Divergência documentada em Arquitetura.md, sem alterar o DL.

## Pendências

1. Investigar o HTTP 500 e identificar a causa raiz com evidência runtime.
2. Estabilizar o contrato de conversão e homologar conversão específica por produto em runtime, incluindo quantidade/unidade originais, fator, origem e saldo persistido.
3. Validar rollback real e cenários não cobertos de conversão; distinguir testes unitários de integração.
4. Tratar a lacuna de leitura do saldo direto no mapa e os limites de consistência identificados, mediante tarefa própria.
5. Após estabilizar o 1D, implementar EST-OP-02C.2 — Unidade Logística opcional na Entrada.
6. Implementar regras de quarentena posteriormente.

Conversão por lote não será implementada agora: permanece extensão futura para densidade, umidade ou propriedades próprias do lote. Produção/BOM está fora do escopo atual.

## Proveniência e limites

O HTTP 500 e as validações anteriores de entrada direta/conversão global são informações fornecidas pelo solicitante em 08/09/2026. Os relatórios arquivados registram verificações anteriores distintas e não substituem esse estado atual. Log/stack trace da ocorrência e sua causa raiz não foram identificados nesta consolidação.

Esta tarefa foi exclusivamente documental: não executou runtime, builds, testes, migrations ou correções de código.

## Evidências / Referências

- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1_RESULTADO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/App.Domain.Tests/EntradaDiretaSincronizacaoTestScenarios.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904185516_AddConversaoUnidadeAndDimension.cs`
- `BACKEND/PRPA/App.Infra.Data/Migrations/20260904234311_AddProdutoConversaoUnidade.cs`
