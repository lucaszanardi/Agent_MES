# DL-0036 - Transactional Outbox para Eventos da Primeira Vertical de Estoque

## Codigo

DL-0036

## Status

Aprovado

## Contexto

A AS-0003 aprovou arquitetura orientada a eventos e a AS-0007 definiu eventos de dominio para a primeira vertical de Estoque. A publicacao direta de eventos durante a transacao de dominio poderia perder mensagens ou publicar fatos que nao foram persistidos.

## Problema

Eventos da vertical precisam ser persistidos de forma atomica com a alteracao do dominio e publicados posteriormente com rastreabilidade, retries, deduplicacao e idempotencia de consumidores.

## Decisao

A primeira vertical de Estoque usara Transactional Outbox para eventos de dominio.

A outbox sera gravada na mesma transacao local que altera `MovimentacaoDeEstoque`, `UnidadeLogistica`, historico e idempotencia.

Estrutura conceitual minima:

- `EventId`;
- `EventName`;
- `SchemaVersion`;
- `AggregateType`;
- `AggregateId`;
- `AggregateVersion`;
- `Payload`;
- `OccurredAt`;
- `CorrelationId`;
- `CausationId`;
- `ActorId`;
- `PlantId`;
- `WarehouseId`;
- `PublishStatus`;
- `AttemptCount`;
- `NextAttemptAt`;
- `PublishedAt`;
- `LastError`.

Estados minimos de publicacao:

- `Pending`;
- `Publishing`;
- `Published`;
- `FailedRetryable`;
- `FailedFinal`;
- `DeadLetter`.

Regras:

- entrega assumida: `at least once`;
- nao assumir `exactly once`;
- consumidores devem ser idempotentes por `EventId`;
- publicacao duplicada deve ser tolerada por consumidores;
- retry deve usar tentativa limitada e backoff;
- falha permanente deve mover evento para dead letter logica;
- ordenacao deve ser preservada por `AggregateType`, `AggregateId` e `AggregateVersion` quando houver eventos do mesmo agregado;
- schema de evento deve possuir versao;
- payload deve preservar correlation ID e causation ID;
- retencao da outbox deve ser definida por politica futura, sem apagar antes de auditoria e reprocessamento seguros.

## Alternativas

- Publicar evento diretamente dentro do handler sem outbox: rejeitado.
- Usar polling de tabelas de dominio como evento: rejeitado.
- Exigir broker antes de iniciar a primeira slice: rejeitado, pois nenhum broker foi identificado no projeto.
- Persistir outbox local e publicar posteriormente: aprovado.

## Consequencias

### Consequencias positivas

- Evita perda de evento apos commit.
- Permite retry e reprocessamento.
- Preserva rastreabilidade por correlation ID.
- E compativel com a stack atual sem instalar dependencias.

### Consequencias negativas

- Exige tabela/estrutura de outbox.
- Exige processo publicador futuro.
- Consumidores precisam tratar duplicidade.

### Riscos e compromissos

- Sem monitoramento, eventos pendentes podem acumular.
- Publicador concorrente pode publicar fora de ordem se nao respeitar agregado/versao.
- Payload sensivel precisa ser protegido conforme politica futura.

## Compatibilidade tecnica

- EF Core/MySQL suportam gravacao da outbox na mesma transacao.
- Nao foi identificado broker, MassTransit, RabbitMQ, Kafka, Hangfire, Quartz ou `BackgroundService` especifico no projeto.
- A primeira etapa pode persistir outbox e publicar em processo posterior a ser implementado com infraestrutura propria da aplicacao.
- O projeto possui logs/middleware basicos, mas monitoramento de outbox ainda precisa ser implementado.

## Referencias

- `../15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `DL-0019 - Arquitetura Orientada a Eventos.md`
- `DL-0020 - Resiliencia Idempotencia Auditoria e Reprocessamento.md`
- `DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `DL-0035 - Fronteira Transacional da Confirmacao de Movimentacao de Estoque.md`

## Relacao com AS-0007

Consolida a decisao pendente de Transactional Outbox registrada nas secoes 13, 14, 18, 21, 22, 24 e 25 da AS-0007.
