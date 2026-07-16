# DL-0006 - Movimentacoes de Estoque em Etapas e Material em Transito

## Codigo

DL-0006

## Status

Aprovado

## Contexto

A AS-0002 definiu que movimentacoes de estoque devem representar entrada, saida, transferencia interna, transferencia entre armazens e material em transito de forma rastreavel e operacionalmente segura.

## Problema

Movimentacoes tratadas como evento unico nao representam adequadamente retirada, transito, recebimento, armazenagem, divergencia e conclusao, especialmente quando cada etapa pode ter responsaveis diferentes.

## Decisao

As movimentacoes de estoque serao tratadas em etapas. Transferencias internas seguirao o fluxo Origem -> retirada confirmada -> em transito -> destino informado -> armazenagem confirmada.

Transferencias entre armazens seguirao o fluxo Armazem de origem -> expedicao interna -> em transito -> recebimento no armazem de destino -> armazenagem definitiva.

Material em transito nao fica disponivel na origem, ainda nao fica disponivel no destino, permanece rastreavel, aparece em painel de pendencias e gera alerta quando ultrapassar o tempo esperado.

## Alternativas

- Movimento unico: rejeitado por nao representar responsabilidades e transito.
- Movimento em etapas: aprovado por preservar rastreabilidade operacional.

## Consequencias

- Cada etapa podera registrar seu proprio responsavel.
- A transferencia somente sera concluida apos confirmacao do destino.
- Materiais retirados e ainda nao armazenados poderao ser identificados.

## Impactos

- Entrada, saida, transferencia interna e transferencia entre armazens.
- Painel futuro de pendencias.
- Validacoes futuras de saldo disponivel e condicao de transito.

## Riscos

- Material esquecido em transito.
- Divergencia entre saldo e eventos.
- Falta de alerta para atraso.

## Pendencias

- Modelo fisico definitivo de movimentacao em transito.
- Regras de concorrencia e bloqueio operacional.
- Painel de pendencias e SLA.
- Endpoints, migrations e propriedades definitivas.

## Relacao com AS-0002

Derivado das secoes 4, 5, 13, 15, 22, 23 e 25 da AS-0002.
