# EST-OP-02C — Especificação funcional da Entrada

## IMPLEMENTADO

Entrada direta com produto, destino, quantidade e unidade, registrando movimento e atualizando saldo pelo serviço específico. Implementado significa localizado no código; não implica homologação runtime do 1D.

| Campo | Implementação / regra |
|---|---|
| Produto | produtoid obrigatório na tela; serviço exige existência e Ativo. |
| Versão do Produto | versaoprodutoid opcional, filtrado pelo produto na UI e integrante da chave lógica do saldo. Validação de pertencimento da versão no serviço de entrada: não identificada. |
| Lote | lotematerialid opcional no formulário/DTO. Backend exige se Produto.ControlaLote; quando informado, valida existência, produto e ativo. |
| Almoxarifado destino | almoxarifadodestinoid obrigatório; tela filtra permiteEntrada; serviço valida existência. |
| Localização destino | localizacaodestinoid obrigatório. Tela filtra pelo almoxarifado; serviço exige não bloqueada, sem filhos e finalidade Armazenagem. Validação explícita de pertencimento ao almoxarifado nesse serviço: não identificada. |
| Quantidade recebida | quantidadeRecebida na UI, positiva; formulário usa mínimo 0,000001 e HTML step 0,0001. Serviço exige quantidade > 0. Há divergência de transporte descrita abaixo. |
| Unidade recebida | unidadeRecebidaId na UI; lista de unidades recebíveis por produto; padrão igual à unidade de estoque. |
| Unidade de estoque | Obtida automaticamente de Produto.UnidadeMedidaId, exibida sem edição. UI bloqueia produto sem unidade localizável. |
| Conversão | Mesma unidade: fator 1; específica do produto; global; ausência: rejeição no serviço. UI mostra prévia. |
| Quantidade de estoque | quantidadeEstoqueCalculada na tela; quantidadeEstoque na entidade. Saldo recebe quantidade na unidade de estoque. |
| Motivo | motivomovimentoid obrigatório na tela e nullable no DTO. |
| Tipo de documento | tipodocumentoid obrigatório na tela e nullable no DTO. |
| Número do documento | numdocumento opcional. |
| Data do movimento | datamovimento obrigatória na tela, convertida para ISO no payload. Vigência de conversão é avaliada com DateTime.Now, não com essa data. |
| Observação | observacao opcional. |

## Regras autoritativas e divergência do contrato

Saldo é persistido na unidade de estoque. A regra funcional é o backend recalcular a conversão: o frontend não é fonte de verdade da conversão.

O serviço implementa esse cálculo a partir de movimento.quantidade e movimento.unidademedidaid. Entretanto, prepararPayload envia nesses campos o resultado já convertido e a unidade de estoque; os campos originais são enviados separadamente, mas não existem no MovimentoEstoqueCreateDto. Assim, a integração atual não garante essa regra de ponta a ponta: a análise estática indica caminho de mesma unidade e perda dos valores originais. O comportamento deve ser homologado após investigação; isso não é diagnóstico da causa do HTTP 500.

EntradaDiretaValidator existe, mas sua invocação no endpoint específico não foi identificada. As regras efetivamente chamadas são as de EntradaDiretaSincronizacaoServices. Não atribuir automaticamente ao endpoint todas as validações declaradas no validator.

## PLANEJADO

- EST-OP-02C.2: UL opcional, após estabilizar e homologar o 1D; criação/associação ausente na Entrada atual.
- Quarentena e regras de inspeção/liberação em etapa posterior; não implementadas no fluxo atual. Regras detalhadas permanecem dependentes de especificação/validação humana.

## FORA DO ESCOPO ATUAL

Conversão por lote, consumo de BOM/receita e produção. Conversão por lote permanece extensão futura para densidade, umidade e características próprias do lote.

Criação de lote durante a entrada não está implementada na tela inspecionada; a sugestão histórica não comprova aprovação funcional. Esta tarefa não cria campos, entidades, endpoints, telas ou migrations.

## Situação de homologação

EM IMPLEMENTAÇÃO / BLOQUEADO PARA HOMOLOGAÇÃO RUNTIME DO 1D. O solicitante informa HTTP 500 ao confirmar entrada; causa raiz não identificada. Builds/testes PASS históricos não encerram a validação runtime.

## Evidências / Referências

- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C-SPEC_ESPECIFICACAO_FINAL.md`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.html`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ClassificacaoLocalizacaoHelper.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Service/Validators/EntradaDiretaValidator.cs`
