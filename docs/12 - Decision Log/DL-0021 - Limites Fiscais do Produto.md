# DL-0021 - Limites Fiscais do Produto

## Codigo

DL-0021

## Status

Aprovado

## Contexto

A AS-0003 definiu que o MES/MOM e um sistema de execucao, rastreabilidade, qualidade, estoque operacional, apontamentos e acompanhamento industrial, nao um ERP fiscal.

## Problema

Confundir referencia operacional de documento fiscal com responsabilidade fiscal do produto poderia criar escopo indevido, risco legal e dependencia de regras tributarias fora do dominio MES.

## Decisao

O MES nao sera responsavel por obrigacoes fiscais ou comunicacao com SEFAZ.

Documentos fiscais poderao ser utilizados somente como referencia operacional para recebimento, conferencia, rastreabilidade e associacao documental.

## Alternativas

- Implementar emissao fiscal dentro do MES: rejeitado.
- Tratar nota fiscal como documento operacional de referencia quando necessario: aprovado.
- Substituir ERP fiscal: rejeitado.

## Consequencias

- O MES nao calcula impostos.
- O MES nao emite documento fiscal.
- O MES nao assina documento fiscal.
- O MES nao transmite documento a SEFAZ.
- O MES preserva a nota fiscal apenas como referencia operacional quando aplicavel.

## Impactos

- Recebimento.
- Conferencia.
- Origem do material.
- Rastreabilidade documental.
- Integracoes com ERP ou sistemas fiscais externos.

## Riscos

- Usuarios podem esperar funcionalidades fiscais por existir referencia a nota fiscal.
- Integracoes com ERP fiscal precisam deixar claro o limite de responsabilidade.

## Pendencias

- Definir como referencias fiscais operacionais serao representadas futuramente.
- Definir integracoes de consulta ou recebimento de dados fiscais somente se aprovadas em sessao futura.
- Nenhuma comunicacao com SEFAZ e criada nesta decisao.

## Relacao com AS-0003

Derivado das secoes 6.16, 7, 8 e 11 da AS-0003.
