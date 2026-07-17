# DL-0020 - Resiliencia Idempotencia Auditoria e Reprocessamento

## Codigo

DL-0020

## Status

Aprovado

## Contexto

A AS-0003 definiu que integracoes podem falhar, ficar indisponiveis, enviar mensagens duplicadas, atrasadas, fora de ordem ou precisar de reprocessamento.

## Problema

Falhas de integracao nao podem interromper a operacao industrial do MES, nem gerar efeitos duplicados, perda de payload, sobrescrita silenciosa ou ausencia de rastreabilidade.

## Decisao

A integracao suportara indisponibilidade, mensagens duplicadas, mensagens fora de ordem, falhas, retentativas, reprocessamento e rastreabilidade completa.

A arquitetura devera considerar idempotencia, correlacao, versionamento, estados da sincronizacao, fila logica de erro, preservacao do payload e operacao do MES independente da disponibilidade externa.

## Alternativas

- Interromper operacao do MES quando ERP ou sistema externo estiver indisponivel: rejeitado.
- Reprocessar mensagens sem preservar payload e historico de tentativas: rejeitado.
- Controlar resiliencia, auditoria, idempotencia e reprocessamento no fluxo de sincronizacao: aprovado.

## Consequencias

- Mensagens de saida poderao permanecer pendentes para envio posterior.
- Mensagens duplicadas nao devem produzir efeitos duplicados.
- Mensagens atrasadas ou fora de ordem devem ser avaliadas por versao, timestamp, sequencia, revisao ou estado atual.
- Toda tentativa devera ser auditavel.

## Impactos

- Hub de Sincronizacao.
- Auditoria.
- Observabilidade.
- Central futura de Sincronizacao.
- Operacao de estoque, producao, qualidade, rastreabilidade e tarefas.

## Riscos

- Reprocessamento manual sem autorizacao pode gerar inconsistencia.
- Payload sensivel precisa ser protegido.
- Falta de limites de tentativas pode gerar acumulacao operacional.

## Pendencias

- Modelo fisico de mensagens, estados, tentativas e erros.
- Definir politica de retentativas e reprocessamento.
- Definir seguranca e mascaramento de payloads sensiveis.
- Definir indicadores e telas futuras.

## Relacao com AS-0003

Derivado das secoes 6.12, 6.13, 6.14, 6.17 e 11 da AS-0003.
