# EST-OP-02C — Arquitetura da Entrada de Estoque

## Diretriz consolidada para o MVP

A arquitetura abaixo é a diretriz expressamente confirmada na tarefa de consolidação. A documentação anterior do ProjectBook fornece contexto, mas suas aprovações, cronogramas e funcionalidades projetadas não são convertidos em fatos implementados.

| Conceito | Responsabilidade |
|---|---|
| SaldoEstoque | Fonte de verdade quantitativa do MVP: físico, reservado, bloqueado e disponível, na unidade oficial do produto. |
| MovimentoEstoque | Histórico/fato da movimentação e registro da conversão aplicada. Não confundir com a entidade MovimentacaoDeEstoque da vertical de UL. |
| UnidadeLogistica | Identidade logística opcional para material fisicamente identificado. Não é uma segunda fonte independente a somar ao saldo. |
| LocalizacaoEstoque | Posição física de destino. A operação exige armazenagem efetiva. |
| Produto.UnidadeMedidaId | Unidade de Estoque no MVP; não foi criado campo separado UnidadeEstoqueId no produto. |

**Nem todo estoque possui Unidade Logística.** Estoque direto pode existir como Produto + Lote, quando aplicável + Localização + Unidade de Estoque + Quantidade, sem UnidadeLogistica.

A chave lógica implementada acrescenta versão e almoxarifado: produtoid + versaoprodutoid + lotematerialid + almoxarifadoid + localizacaoestoqueid + unidademedidaid.

## Fluxos

Atual: Entrada → MovimentoEstoque → SaldoEstoque.

O controller chama EntradaDiretaSincronizacaoServices dentro de IUnitOfWork.ExecuteAsync. O serviço valida, calcula a conversão, adiciona o movimento, localiza/cria saldo, incrementa qtdfisica e calcula qtddisponivel = qtdfisica − qtdreservada − qtdbloqueada.

Futuro, quando solicitado: Entrada → MovimentoEstoque → SaldoEstoque → associação opcional com UL.

UL não é criada nem associada pela Entrada atual. A existência de UnidadeLogistica em outra vertical não representa implementação de EST-OP-02C.2. É vedado totalizar SaldoEstoque + UnidadeLogistica como duas quantidades independentes.

## Limites da implementação

- A chave lógica é usada na busca; o mapping de SaldoEstoque inspecionado não define índice único composto ou token de concorrência. Não declarar duplicidade ou concorrência resolvidas.
- A infraestrutura possui transação, mas o teste de rollback encontrado é placeholder; atomicidade runtime não foi comprovada nesta tarefa.
- MovimentoEstoque tem campos de histórico, porém possui setters públicos e endpoints de atualização/exclusão. Imutabilidade integral não está demonstrada.
- O mapa operacional inspecionado consulta UL, sem incorporar SaldoEstoque.
- O contrato tela/DTO diverge da regra de recalcular a conversão a partir da quantidade original; ver Implementacao.md.

## Divergências documentais

O DL-0022 está marcado Aprovado e define saldo como projeção derivada, rejeitando saldo editável como fonte primária. A diretriz do MVP solicitada aqui e o serviço atual usam SaldoEstoque como referência quantitativa persistida. A divergência permanece explícita; este documento não revoga nem altera o DL-0022.

EST-OP-02C-ARCH_DECISAO_FINAL.md ainda contém status de aprovação pendente, enquanto a especificação histórica usa linguagem de arquitetura aprovada. Não foram identificadas nesta análise evidências que permitam ratificar as assinaturas ou todos os compromissos desses relatórios. A consolidação registra a diretriz confirmada pelo solicitante, sem promover propostas antigas a decisões adicionais.

## Evolução delimitada

Após estabilizar e homologar o 1D, o próximo marco pretendido é EST-OP-02C.2 — Unidade Logística opcional na Entrada. Quarentena permanece planejada para etapa posterior.

Conversão por lote não será implementada agora. Permanece extensão futura apenas para relações que dependam de densidade, umidade ou propriedades específicas do material/lote. Não há autorização de implementação nesta tarefa.

## Evidências / Referências

- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C-ARCH_DECISAO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C-SPEC_ESPECIFICACAO_FINAL.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ClassificacaoLocalizacaoHelper.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`
- `BACKEND/PRPA/App.Domain.Tests/EntradaDiretaSincronizacaoTestScenarios.cs`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `BACKEND/PRPA/App.Domain/Entities/Estoque/UnidadesLogisticas/UnidadeLogistica.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/SaldoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/UnitOfWork.cs`
