# DL-0018 - Classificacao das Sincronizacoes

## Codigo

DL-0018

## Status

Aprovado

## Contexto

A AS-0003 classificou informacoes integraveis em cadastros mestres, documentos operacionais e eventos operacionais.

## Problema

Tratar todos os dados com a mesma latencia e estrategia ignora diferencas entre cadastros relativamente estaveis, documentos com ciclo de vida e eventos de chao de fabrica.

## Decisao

As sincronizacoes serao classificadas como mestre, documental e operacional.

Sincronizacao Mestre atende dados relativamente estaveis, como produtos, clientes, fornecedores, colaboradores, unidades, recursos e linhas.

Sincronizacao Documental atende documentos com ciclo de vida, como pedidos, demandas, ordens de producao, planos, estruturas liberadas e roteiros liberados.

Sincronizacao Operacional atende eventos que devem chegar quase em tempo real, como sequenciamento alterado, OP liberada, prioridade alterada, lote bloqueado, material liberado, tarefa atribuida e recurso indisponivel.

## Alternativas

- Classificacao unica para todas as sincronizacoes: rejeitada.
- Separar sincronizacoes por comportamento e criticidade operacional: aprovada.

## Consequencias

- A latencia esperada podera variar por categoria.
- Processamentos agendados podem ser adequados para dados mestres em alguns cenarios.
- Eventos operacionais exigem tratamento mais rapido e observavel.

## Impactos

- Politicas de sincronizacao.
- Hub de Sincronizacao.
- Eventos.
- Paineis operacionais.
- Central futura de Sincronizacao.

## Riscos

- Classificacao incorreta pode causar atraso operacional.
- Eventos operacionais tratados como cadastros podem comprometer execucao no chao de fabrica.

## Pendencias

- Definir SLAs e latencias por implantacao.
- Definir mecanismos fisicos de processamento por categoria.
- Definir telas e indicadores de acompanhamento.

## Relacao com AS-0003

Derivado das secoes 6.8, 6.9, 6.13 e 11 da AS-0003.
