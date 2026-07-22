# DL-0026 - Reserva de Estoque Disponibilidade e Concorrencia

## Codigo

DL-0026

## Status

Aprovado

## Contexto

A AS-0004 definiu ReservaDeEstoque como processo de atendimento de demanda e Aggregate Root proprio.

## Problema

Reservas feitas apenas sobre saldo agregado podem permitir dupla alocacao, indisponibilidade falsa ou conflito entre demandas.

## Decisao

ReservaDeEstoque sera Aggregate Root proprio.

A reserva coordena alocacao para demandas e pode envolver multiplas ULs. A Unidade Logistica protege sua quantidade fisica e alocavel.

Devem ser separadas quantidade fisica, disponivel, reservada, bloqueada, em movimentacao e em consumo.

Devera existir controle de concorrencia para evitar dupla alocacao da mesma quantidade.

## Alternativas

- Reservar apenas por saldo total do material: rejeitado.
- Fazer reserva como campo interno da UL sem processo proprio: rejeitado.
- Criar ReservaDeEstoque como agregado coordenador: aprovado.

## Consequencias

### Consequencias positivas

- Reservas podem ser parciais, totais, realocadas, liberadas, canceladas, expiradas ou consumidas.
- Disponibilidade real passa a considerar ULs, bloqueios, transito, consumo e reservas.

### Consequencias negativas

- Reservas pendentes podem afetar disponibilidade operacional.
- O modelo precisa coordenar reserva e UL para evitar conflito de alocacao.

### Riscos e compromissos

- Sem concorrencia adequada, duas demandas podem disputar a mesma quantidade.
- Reservas pendentes podem afetar disponibilidade operacional.

## Impactos

- Ordem de Producao futura.
- Abastecimento.
- Expedicao futura.
- Movimentacao.
- Projecoes de disponibilidade.

## Riscos

- Sem concorrencia adequada, duas demandas podem disputar a mesma quantidade.
- Reservas pendentes podem afetar disponibilidade operacional.

## Pendencias

- Modelo fisico de ReservaDeEstoque e alocacoes por UL.
- Regras de expiracao, realocacao e prioridade.

## Relacao com AS-0004

Derivado das secoes 22, 31, 32, 37, 39 e 40 da AS-0004.