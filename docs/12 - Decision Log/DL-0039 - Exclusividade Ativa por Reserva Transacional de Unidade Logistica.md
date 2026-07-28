# DL-0039 - Exclusividade Ativa por Reserva Transacional de Unidade Logistica

## Codigo

DL-0039

## Status

Aprovado

## Data

2026-07-27

## Contexto

DL-0034 definiu que uma `UnidadeLogistica` nao pode possuir mais de uma movimentacao ativa simultanea. A implementacao futura usara MySQL com Pomelo/EF Core, mas a versao efetiva do servidor MySQL nao foi identificada nos arquivos do repositorio.

## Problema

Era necessario escolher o mecanismo fisico de exclusividade ativa em MySQL sem depender de indice filtrado, coluna gerada ou comportamento especifico ainda nao validado no ambiente.

## Decisao

Garantir exclusividade ativa por tabela de reserva transacional `CUNIDADELOGISTICAMOVEMENTRESERVATION`, com constraint unica por `WarehouseId, UnidadeLogisticaId`.

A reserva deve ser criada na mesma transacao da movimentacao solicitada, idempotencia e outbox. Na confirmacao, a reserva deve ser liberada na mesma transacao que confirma a movimentacao e atualiza a posicao da `UnidadeLogistica`.

Violacao da constraint unica deve ser traduzida para erro estavel de aplicacao, como movimentacao ativa existente ou conflito equivalente.

## Alternativas consideradas

| Alternativa | Resultado |
|---|---|
| Indice unico filtrado/parcial em `CMOVIMENTACAODEESTOQUE` | Rejeitado para a primeira implementacao por baixa portabilidade em MySQL. |
| Coluna gerada unica em MySQL | Rejeitada para a primeira implementacao por depender de mapping e versao efetiva do servidor. |
| Apenas status `EmMovimentacao` com versionamento otimista | Rejeitada como mecanismo unico, pois nao e garantia fisica suficiente sob concorrencia. |
| Lock pessimista ou isolamento serializable | Rejeitados como padrao inicial por custo operacional e risco de contencao. |
| Tabela de reserva transacional | Aprovada. |

## Consequencias positivas

- Garante exclusividade com constraint simples e portavel.
- Evita dependencia de recursos especificos de versao MySQL.
- Mantem o checker de disponibilidade como pre-validacao, sem trata-lo como autoridade final.
- Preserva a atomicidade da primeira vertical.

## Consequencias negativas

- Introduz tabela adicional no modelo fisico.
- Exige liberacao rigorosa da reserva na confirmacao e em fluxos futuros de cancelamento/rejeicao.
- Exige traducao explicita de violacao unica para erro de dominio/aplicacao.

## Riscos

- Reserva presa se futuros fluxos de cancelamento/rejeicao nao liberarem a linha.
- Falta de indice composto por escopo operacional pode causar conflito indevido entre warehouses.
- Limpeza manual indevida pode violar a garantia de exclusividade.

## Referencias cruzadas

- AS-0007
- DL-0034
- DL-0035
- `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Primeira Vertical de Estoque.md`

## Relacao com a AS

Esta decisao resolve a pendencia fisica da exclusividade de movimentacao ativa por `UnidadeLogistica` na primeira vertical de Estoque.