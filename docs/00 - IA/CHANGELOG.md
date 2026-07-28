# Changelog do Project Book

## 2026-07-28

- Implementada a API publica da primeira vertical funcional de Estoque para `CriarMovimentacaoDeEstoque`, `ConfirmarMovimentacaoDeEstoque` e consulta por ID em `GET/POST /api/estoque/movimentacoes`, com autorizacao obrigatoria, `Idempotency-Key`, correlation ID, tratamento estavel de erros e 75 cenarios do harness aprovados; sem frontend, sem nova migration e sem `database update` nesta etapa.

- Revisado o pre-deploy da migration `20260727170707_CreateFirstEstoqueVertical` apos reorganizacao do backend: toolchain local alinhada para `dotnet-ef` 8.0.0, builds e harness de dominio/aplicacao aprovados com 65 cenarios, script SQL gerado apenas para inspecao em diretorio ignorado e nenhuma migration aplicada.
- Inspecao remota do banco de demonstracao classificada como bloqueada nesta execucao por ausencia das variaveis `MES_DEMO_DB_*` e do gate `MES_DEMO_CONFIRMATION=DEMO_DATABASE_CONFIRMED`; nenhum segredo foi registrado e nenhum DDL foi executado.

## 2026-07-27

- Resolvidas as decisoes bloqueadoras de persistencia da primeira vertical de Estoque: persistencia propria de LocalDeEstoque com correlacao ao legado, exclusividade ativa por tabela de reserva transacional, identificadores fisicos e classificacao da retencao como pendencia operacional de go-live.
- Criado inventario da implementacao legada de Locais de Estoque e registrado o DL-0041, consolidando transicao paralela com carga inicial/correlacao, fonte da verdade e ocupacao calculada.
- Implementada a infraestrutura de persistencia da primeira vertical de Estoque no backend: mapeamentos EF, repositories concretos, reserva transacional, idempotencia persistida, transactional outbox e Unit of Work, sem migrations, sem scripts SQL, sem API, sem frontend e sem workers/publicadores.
- Revisada tecnicamente a infraestrutura EF Core da primeira vertical de Estoque; corrigidas a preservacao de erros estaveis do Unit of Work e a identificacao de duplicidade da reserva transacional, com 65 cenarios aprovados e build da solucao concluido.
- Gerada e revisada a primeira migration EF Core da vertical de Estoque (`20260727170707_CreateFirstEstoqueVertical`), criando somente as seis tabelas aprovadas, com SQL de inspecao temporario e sem executar `database update`.
- Tentada a preparacao da validacao fisica da migration `20260727170707_CreateFirstEstoqueVertical` em MySQL descartavel; teste classificado como `TESTE BLOQUEADO` por ausencia de Docker, cliente/servico MySQL local ou porta MySQL descartavel comprovavel, sem executar `database update`.


## 2026-07-24

- Criada a especificacao tecnica `Contrato de Persistencia da Primeira Vertical de Estoque.md`, cobrindo modelo relacional, concorrencia, exclusividade ativa, idempotencia persistida, transactional outbox, fronteira transacional e compatibilidade com legado, sem implementar infraestrutura.

- Criada a AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque.
- Registrada classificacao final `NOT READY` para retomada da codificacao funcional da primeira vertical de Estoque.
- Atualizados indices e roadmap com a AS-0006.
- Criada a AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.
- Registrada classificacao documental `READY WITH CONDITIONS` para iniciar a primeira slice apos validacao humana.
- Atualizados indices e roadmap com a AS-0007.
- Criados os Decision Logs DL-0033 a DL-0037 para consolidar idempotencia, concorrencia, fronteira transacional, outbox e coexistencia com legado.
- Atualizada a AS-0007 com a classificacao final `READY` para o primeiro incremento de dominio.
- Atualizados indices, roadmap e glossario com os DL-0033 a DL-0037.
- Implementado o primeiro incremento de codigo autorizado pela AS-0007: nucleo de dominio de Estoque em memoria, com `UnidadeLogistica`, `LocalDeEstoque`, `MovimentacaoDeEstoque`, eventos de dominio, erros, Value Objects e testes unitarios auto-contidos, sem migrations, endpoints, persistencia EF, frontend ou integracao com legado.
- Revisado e padronizado o nucleo de dominio de Estoque: movido de `App.Domain/Estoque` para `App.Domain/Entities/Estoque`, namespaces atualizados para `App.Domain.Entities.Estoque`, cancelamento/rejeicao adiados para incremento futuro e harness marcado como provisorio.
- Revisado tecnicamente o nucleo de dominio de Estoque: corrigido envelope comum de eventos com `AggregateVersion` e `WarehouseId`, adicionadas validacoes temporais de criacao/solicitacao/confirmacao e ampliado o harness provisorio para 32 cenarios.
- Alinhada a AS-0007 ao recorte efetivamente aprovado de `CriarMovimentacaoDeEstoque` e `ConfirmarMovimentacaoDeEstoque`; implementada camada de aplicacao abstrata em `App.Service` com commands, handlers, interfaces de repository/unit of work/idempotencia/eventos/exclusividade e harness ampliado para 53 cenarios, sem infraestrutura concreta.
- Reorganizada a camada de aplicacao de Estoque de `App.Service/Estoque` para `App.Service/Services/Estoque`, com namespaces atualizados para `App.Service.Services.Estoque`, sem alteracao funcional, de contratos ou de comportamento.
- Criada a AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.
- Criados os Decision Logs DL-0030, DL-0031 e DL-0032 derivados da AS-0005.
- Atualizados indices, roadmap e glossario com referencias da AS-0005.
- Nenhuma alteracao de backend ou frontend foi realizada.
