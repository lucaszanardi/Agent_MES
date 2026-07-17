# DL-0014 - Hub de Sincronizacao

## Codigo

DL-0014

## Status

Aprovado

## Contexto

A AS-0003 definiu que a comunicacao externa devera ser intermediada por um modulo responsavel por validacao tecnica, correlacao, auditoria, idempotencia, tentativas, estados e reprocessamento.

## Problema

Permitir que adaptadores ou integracoes gravem diretamente em tabelas do dominio criaria risco de sobrescrita silenciosa, perda de regras industriais e historico inconsistente.

## Decisao

Toda comunicacao externa devera ser intermediada por um Hub de Sincronizacao modular.

O Hub sera inicialmente modulo interno da solucao e nao fica definido como microservico obrigatorio nesta sessao.

O Hub nao implementa regra de negocio do MES, nao atualiza diretamente tabelas de dominio e nao decide validade industrial de alteracoes.

## Alternativas

- Integracao gravando diretamente nas tabelas de dominio: rejeitada.
- Adaptador contendo regras de negocio do MES: rejeitado.
- Hub modular interno com passagem obrigatoria pelos servicos de aplicacao e dominio: aprovado.

## Consequencias

- Mensagens externas serao tratadas com idempotencia, auditoria e correlacao.
- A aplicacao e o dominio continuam responsaveis por criar, atualizar, rejeitar, gerar pendencia, exigir aprovacao, versionar ou inativar.
- A evolucao para microservico permanece possivel, mas nao obrigatoria.

## Impactos

- Integracoes externas.
- Servicos de aplicacao.
- Auditoria.
- Observabilidade.
- Reprocessamento.
- Eventos internos e externos.

## Riscos

- O Hub pode concentrar complexidade tecnica.
- Responsabilidades podem se misturar se nao houver separacao clara entre integracao e dominio.

## Pendencias

- Desenho tecnico do Hub.
- Modelo fisico de mensagens, estados, tentativas e auditoria.
- Definicao futura de filas, broker ou mecanismo equivalente.
- Migrations, endpoints e servicos definitivos.

## Relacao com AS-0003

Derivado das secoes 6.5, 6.12, 6.13, 7, 9 e 11 da AS-0003.
