# DL-0022 - Estoque como Dominio e Saldo como Projecao

## Codigo

DL-0022

## Status

Aprovado

## Contexto

A AS-0004 consolidou o Domain Discovery do dominio de Estoque e logistica interna.

## Problema

Tratar estoque como um unico Aggregate Root ou como saldo editavel mistura processos fisicos, disponibilidade, movimentacoes, reservas, inventarios e consultas em uma estrutura ampla demais.

## Decisao

Estoque sera tratado como dominio funcional, nao como Aggregate Root.

O saldo de estoque sera uma projecao derivada de fatos operacionais confirmados, como recebimentos, movimentacoes, reservas, consumos, bloqueios, inventarios e ajustes formais.

Aggregate Roots iniciais do dominio: ExpectativaDeRecebimento, Recebimento, UnidadeLogistica, LocalDeEstoque, MovimentacaoDeEstoque, ReservaDeEstoque, PoliticaDeContagem e Inventario.

## Alternativas

- Criar um Aggregate Root `Estoque`: rejeitado.
- Editar saldo diretamente como fonte primaria: rejeitado.
- Modelar estoque como dominio com agregados especializados e saldo projetado: aprovado.

## Consequencias

### Consequencias positivas

- Processos operacionais preservam rastreabilidade.
- Projecoes apoiam consulta, disponibilidade e relatorios.
- Alteracoes relevantes devem passar por operacoes de dominio.

### Consequencias negativas

- A leitura de saldo passa a depender de projecoes corretamente mantidas.
- A fronteira entre agregados exige disciplina na implementacao futura.

### Riscos e compromissos

- Projecoes podem ser confundidas com fonte primaria de verdade.
- A fronteira entre agregados exige disciplina na implementacao futura.

## Impactos

- Estoque fisico e virtual.
- Consultas de saldo.
- Movimentacoes, reservas, inventarios e ajustes.
- Modelagem futura de backend e frontend.

## Riscos

- Projecoes podem ser confundidas com fonte primaria de verdade.
- A fronteira entre agregados exige disciplina na implementacao futura.

## Pendencias

- Modelagem fisica futura dos agregados.
- Estrategia tecnica de projecoes.
- Nenhuma migration, endpoint ou classe e criada por este DL.

## Relacao com AS-0004

Derivado das secoes 8, 10, 11, 12, 31, 32, 37 e 40 da AS-0004.