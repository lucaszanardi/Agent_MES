# DL-0040 - Identificadores e Retencao Operacional da Persistencia de Estoque

## Codigo

DL-0040

## Status

Aprovado com ressalvas operacionais

## Data

2026-07-27

## Contexto

A primeira vertical de Estoque ja possui Value Objects de identificacao baseados em `int` para ARs e `Guid` para rastreabilidade de comandos/eventos. O contrato de persistencia tambem precisa suportar idempotencia persistida e transactional outbox.

## Problema

Era necessario decidir os tipos fisicos dos identificadores e se a politica final de retencao/TTL de idempotencia e outbox bloqueia a implementacao de infraestrutura.

## Decisao

Os identificadores fisicos dos Aggregate Roots da primeira vertical permanecem `int`: `UnidadeLogisticaId`, `LocalDeEstoqueId` e `MovimentacaoDeEstoqueId`. `ActorId` tambem permanece `int`.

`CorrelationId`, `CausationId` e `EventId` devem ser persistidos como GUID em `char(36)`.

`MovimentacaoDeEstoqueId` deve estar disponivel antes da criacao do agregado e dos eventos de dominio. A infraestrutura/aplicacao deve usar alocacao previa de identificador ou estrategia equivalente sem alterar os Value Objects atuais.

A politica final de retencao de idempotencia e outbox nao bloqueia a implementacao de mappings, migrations, repositories e Unit of Work, desde que as tabelas sejam criadas com colunas e indices que permitam limpeza futura.

Antes do go-live, a retencao operacional deve ser definida e implementada.

## Alternativas consideradas

| Alternativa | Resultado |
|---|---|
| Trocar IDs de AR para GUID | Rejeitada, pois conflita com Value Objects atuais e padrao legado predominante. |
| Usar auto-incremento pos-insert para `MovimentacaoDeEstoqueId` | Rejeitada para criacao de eventos, pois o evento precisa do ID do agregado antes do commit. |
| Persistir GUIDs como `binary(16)` | Nao adotada nesta etapa; `char(36)` foi preferido por legibilidade e interoperabilidade documental. |
| Bloquear implementacao ate definir retencao final | Rejeitada. |
| Exigir estrutura fisica preparada para retencao e bloquear somente go-live sem politica operacional | Aprovada. |

## Consequencias positivas

- Mantem compatibilidade com Value Objects e padroes existentes.
- Permite criar eventos com identificador de agregado ja conhecido.
- Evita bloquear a implementacao por uma politica operacional que pode ser configurada depois.
- Prepara idempotencia e outbox para limpeza, monitoramento e recuperacao futura.

## Consequencias negativas

- Exige mecanismo de alocacao previa de ID para agregados criados por comando.
- `char(36)` ocupa mais espaco que `binary(16)` para GUIDs.
- Go-live continua dependente de politica operacional de retencao e jobs de manutencao.

## Riscos

- Implementacao usar auto-incremento sem alocacao previa e criar eventos sem ID definitivo.
- Crescimento indefinido de outbox/idempotencia se a etapa de go-live ignorar limpeza.
- Politica de retencao curta demais pode prejudicar replays idempotentes e auditoria tecnica.

## Referencias cruzadas

- AS-0007
- DL-0033
- DL-0035
- DL-0036
- `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Primeira Vertical de Estoque.md`
- `BACKEND/PRPA/App.Domain/Entities/Estoque/Shared/ValueObjects.cs`

## Relacao com a AS

Esta decisao resolve as pendencias de identificadores fisicos e classifica retencao como pendencia operacional de go-live, nao como bloqueio para iniciar a implementacao da persistencia.