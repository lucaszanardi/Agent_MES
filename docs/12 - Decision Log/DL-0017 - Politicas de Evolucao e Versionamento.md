# DL-0017 - Politicas de Evolucao e Versionamento

## Codigo

DL-0017

## Status

Aprovado

## Contexto

A AS-0003 diferenciou politica de sincronizacao de politica de evolucao. Dados criticos podem exigir aprovacao, versao, revisao, bloqueio ou inativacao.

## Problema

Aplicar mudancas criticas por simples sobrescrita pode corromper ordens, estoque, rastreabilidade, estrutura de produto, roteiro ou historico operacional.

## Decisao

Mudancas em dados criticos nao serao aplicadas por simples sobrescrita quando houver impacto operacional.

A politica de evolucao devera tratar versao e revisao, estruturas, roteiros, alteracoes criticas de produto, preservacao de versoes utilizadas por ordens existentes, aprovacao e conflito.

## Alternativas

- Sobrescrever sempre o cadastro atual com a versao externa mais recente: rejeitado.
- Bloquear toda alteracao em dados criticos: rejeitado.
- Diferenciar alteracao simples, critica e incompativel por politica de evolucao: aprovado.

## Consequencias

- Estruturas e roteiros poderao exigir nova versao ou revisao.
- Ordens existentes deverao preservar a versao usada no momento definido por arquitetura futura.
- Alteracoes externas poderao gerar pendencia, aprovacao ou conflito.

## Impactos

- Produtos.
- Unidades de medida.
- Controle de lote.
- Estruturas de produto.
- Roteiros.
- Ordens de producao.
- Estoque e rastreabilidade.

## Riscos

- Regras de evolucao incompletas podem bloquear operacao ou permitir alteracao indevida.
- Versionamento pode aumentar complexidade de consulta e auditoria.

## Pendencias

- Architecture Session futura de Engenharia de Produto.
- Architecture Session futura de Roteiros e Recursos.
- Architecture Session futura de Ordens e Campanhas de Producao.
- Modelo fisico de versoes, revisoes e aprovacoes.

## Relacao com AS-0003

Derivado das secoes 6.7, 6.10, 6.12, 6.17 e 11 da AS-0003.
