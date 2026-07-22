# DL-0027 - Expectativa de Recebimento e Recebimento Operacional

## Codigo

DL-0027

## Status

Aprovado

## Contexto

A AS-0004 definiu recebimento como processo operacional e expectativa como previsao independente de origem tecnologica.

## Problema

Acoplar recebimento a Pedido de Compra ou ERP limita cenarios standalone, transferencias, devolucoes, industrializacao, ajustes, recebimentos avulsos e integracoes diversas.

## Decisao

ExpectativaDeRecebimento sera Aggregate Root independente.

Recebimento sera Aggregate Root e processo operacional, nao documento e nao pertencente ao ERP.

Um Recebimento pode existir sem Expectativa. Uma Expectativa pode existir antes de qualquer Recebimento.

Origem Operacional substitui acoplamento direto a Pedido de Compra e pode representar Compra, Transferencia, Industrializacao, Devolucao, Ajuste, Avulso ou Outro Sistema.

## Alternativas

- Tratar recebimento como documento de ERP: rejeitado.
- Exigir expectativa para todo recebimento: rejeitado.
- Separar Expectativa e Recebimento com Origem Operacional: aprovado.

## Consequencias

### Consequencias positivas

- O MES opera em modo standalone, integrado ou hibrido.
- Recebimentos imprevistos podem existir.
- Expectativas podem ser parcialmente atendidas ou atendidas por multiplos recebimentos.

### Consequencias negativas

- Ambiguidade entre expectativa e recebimento se a UX nao for clara.
- Origem operacional exige governanca de configuracao.

### Riscos e compromissos

- Ambiguidade entre expectativa e recebimento se a UX nao for clara.
- Origem operacional exige governanca de configuracao.

## Impactos

- Entrada fisica.
- Conferencia.
- Custodia inicial.
- Divergencias.
- Unidade Logistica.
- Integracoes futuras.

## Riscos

- Ambiguidade entre expectativa e recebimento se a UX nao for clara.
- Origem operacional exige governanca de configuracao.

## Pendencias

- Modelagem fisica futura dos agregados.
- Politicas de nascimento de UL durante recebimento.
- Fluxos detalhados de divergencia e destinacao.

## Relacao com AS-0004

Derivado das secoes 19, 20, 31, 33, 37, 38 e 40 da AS-0004.