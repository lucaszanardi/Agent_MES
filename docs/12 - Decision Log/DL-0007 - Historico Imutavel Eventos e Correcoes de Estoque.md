# DL-0007 - Historico Imutavel, Eventos e Correcoes de Estoque

## Codigo

DL-0007

## Status

Aprovado

## Contexto

A AS-0002 definiu que movimentacoes concluidas precisam preservar auditoria e permitir reconstrucao do ocorrido.

## Problema

Editar ou excluir movimentacoes concluidas compromete auditoria, rastreabilidade e confianca operacional.

## Decisao

Movimentacoes concluidas nao podem ser alteradas ou excluidas. Correcoes ocorrerao por estorno, reversao, novos eventos ou movimentacoes relacionadas. Cancelamentos geram movimentacao corretiva e devem manter vinculo com a movimentacao original.

Eventos da movimentacao formarao linha do tempo, podendo incluir Solicitada, Atribuida, Aceita, Separacao iniciada, Retirada confirmada, Em transito, Destino informado, Armazenagem confirmada, Concluida, Cancelada, Com divergencia e Corrigida.

## Alternativas

- Editar movimentacao concluida: rejeitado por perda de auditoria.
- Excluir movimentacao concluida: rejeitado por perda de historico.
- Corrigir por eventos e movimentos relacionados: aprovado.

## Consequencias

- O historico deve permitir reconstruir o ocorrido.
- Motivo, usuario e data/hora sao obrigatorios em cancelamentos e correcoes.
- Cada etapa podera ter operadores diferentes.

## Impactos

- Auditoria operacional.
- Relatorios historicos.
- Estornos, reversoes e correcoes.
- Registro de dispositivo quando disponivel.

## Riscos

- Correcao sem vinculo.
- Cancelamento incorreto.
- Movimentacao sem documento.
- Divergencia entre saldo e eventos.

## Pendencias

- Modelo fisico definitivo de EventoMovimentacao.
- Politicas completas de autorizacao para cancelamento e correcao.
- Tratamento de concorrencia.
- Migrations, endpoints e propriedades definitivas.

## Relacao com AS-0002

Derivado das secoes 5, 6, 7, 8, 23, 24 e 25 da AS-0002.
