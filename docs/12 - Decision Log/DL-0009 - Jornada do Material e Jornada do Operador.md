# DL-0009 - Jornada do Material e Jornada do Operador

## Codigo

DL-0009

## Status

Aprovado

## Contexto

A AS-0002 definiu Jornada do Material e Jornada do Operador como conceitos funcionais centrais para rastreabilidade e gestao operacional.

## Problema

Sem uma linha do tempo consolidada, fica dificil reconstruir a vida do material e a execucao operacional de cada operador.

## Decisao

A Jornada do Material devera apresentar linha do tempo de eventos relevantes, como recebido, conferido, inspecionado, aprovado ou rejeitado, armazenado, reservado, bloqueado, transferido, em transito, consumido, transformado, produzido, reclassificado, expedido, devolvido, ajustado e inventariado.

A Jornada do Operador devera registrar tarefas recebidas, aceitas, executadas, pausadas, reatribuidas, divergencias, tempos, produtividade e conclusao.

## Alternativas

- Consultas isoladas por modulo: rejeitadas como visao insuficiente para auditoria completa.
- Linha do tempo integrada: aprovada como conceito funcional central.

## Consequencias

- A Jornada do Material conecta estoque, qualidade, producao, rastreabilidade, expedicao, documentos, operadores, locais, lotes e ordens de producao.
- O operador passa a ter historico de tarefas e execucoes relacionado ao seu contexto operacional.

## Impactos

- Rastreabilidade.
- Auditoria.
- Relatorios.
- Painel futuro de tarefas e produtividade.

## Riscos

- Perda de rastreabilidade.
- Divergencia entre saldo e eventos.
- Excesso de dados sem governanca.

## Pendencias

- Entidade fisica definitiva da Jornada do Material.
- Modelo fisico definitivo de Jornada do Operador.
- Forma de preservar snapshots historicos.
- Relatorios e interfaces futuras.

## Relacao com AS-0002

Derivado das secoes 3, 9, 10, 16, 22, 23 e 25 da AS-0002.
