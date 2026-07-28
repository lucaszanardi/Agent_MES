# DL-0033 - Estrategia de Idempotencia dos Comandos da Primeira Vertical de Estoque

## Codigo

DL-0033

## Status

Aprovado

## Contexto

A primeira vertical funcional de Estoque executara comandos mutaveis para criar, confirmar e cancelar movimentacoes de uma unica Unidade Logistica.

A AS-0006 identificou ausencia de idempotencia como bloqueador. A AS-0007 definiu uma estrategia tecnica recomendada, ainda pendente de Decision Log.

## Problema

Repeticoes por clique duplo, timeout, retry de frontend, retry de integracao futura ou reprocessamento nao podem duplicar movimentacoes, confirmacoes, historico, eventos ou alteracoes de posicao da Unidade Logistica.

## Decisao

Todos os comandos mutaveis da primeira vertical de Estoque exigirao `IdempotencyKey`.

Comandos cobertos:

- `CriarMovimentacaoDeEstoque`;
- `ConfirmarMovimentacaoDeEstoque`;
- `CancelarMovimentacaoDeEstoque`.

A chave idempotente tera o escopo logico:

```text
CommandType + ActorOrClientId + IdempotencyKey
```

O sistema devera persistir um registro de idempotencia, conceitualmente denominado `IdempotencyRecord` ou `CommandInbox`, com:

- id;
- command type;
- actor ou client;
- idempotency key;
- payload hash;
- processing status;
- result reference;
- response summary;
- correlation ID;
- created at;
- completed at;
- expiration date.

Estados do registro:

- `Processing`;
- `Completed`;
- `FailedRetryable`;
- `FailedFinal`.

Regras:

- payload repetido com mesma chave e mesmo hash retorna a resposta gravada ou a referencia do resultado;
- payload divergente com mesma chave retorna erro explicito de conflito de idempotencia;
- comando ainda `Processing` retorna resposta de operacao em processamento, sem executar novamente;
- comando `Completed` retorna resultado anterior, sem produzir novos eventos;
- falha `FailedRetryable` permite nova tentativa com a mesma chave;
- falha `FailedFinal` nao permite reprocessamento automatico com a mesma chave;
- correlation ID deve ser registrado e preservado no historico e na outbox;
- comandos humanos e comandos de integracoes futuras seguem a mesma regra de idempotencia;
- consumidores de eventos tambem devem ser idempotentes por `eventId`.

## Alternativas

- Nao exigir idempotency key e confiar apenas no frontend: rejeitado.
- Usar somente `correlationId` como chave idempotente: rejeitado, pois correlacao pode agrupar varios comandos.
- Usar somente chave por Unidade Logistica: rejeitado, pois comandos diferentes poderiam colidir.
- Usar `CommandType + ActorOrClientId + IdempotencyKey`: aprovado.

## Consequencias

### Consequencias positivas

- Evita duplicidade de movimentacao e confirmacao.
- Permite replay seguro apos timeout.
- Facilita auditoria e diagnostico por correlation ID.
- E compativel com EF Core, MySQL e transacoes locais existentes.

### Consequencias negativas

- Exige nova estrutura persistente para idempotencia.
- Exige calculo canonico de hash de payload.
- Exige politica futura de retencao/expurgo.

### Riscos e compromissos

- Hash calculado de forma instavel pode gerar conflitos falsos.
- Retencao curta demais pode permitir duplicidade tardia.
- Retencao longa demais pode aumentar volume operacional.

## Compatibilidade tecnica

- .NET 8 e EF Core suportam persistencia do registro de idempotencia.
- MySQL suporta constraint unica composta para `CommandType + ActorOrClientId + IdempotencyKey`.
- O `UnitOfWork` existente pode envolver o registro de idempotencia na mesma transacao do comando.
- Nao foi identificado worker ou broker obrigatorio para esta decisao.
- Nao ha projeto de testes backend identificado; a implementacao deve criar testes para repeticao, payload divergente e estados do registro.

## Referencias

- `../15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `../15 - Architecture Sessions/AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque.md`
- `DL-0020 - Resiliencia Idempotencia Auditoria e Reprocessamento.md`
- `DL-0032 - Definition of Ready para Retomada da Codificacao.md`

## Relacao com AS-0007

Consolida a decisao pendente de idempotencia registrada nas secoes 11, 14, 15, 18, 21, 22, 24 e 25 da AS-0007.
