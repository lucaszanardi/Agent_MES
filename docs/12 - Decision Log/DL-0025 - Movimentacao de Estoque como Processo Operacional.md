# DL-0025 - Movimentacao de Estoque como Processo Operacional

## Codigo

DL-0025

## Status

Aprovado

## Contexto

A AS-0004 definiu que alteracoes relevantes de estoque devem representar processos fisicos confirmados, rastreaveis e auditaveis.

## Problema

Alterar localizacao, disponibilidade ou saldo de forma instantanea ignora etapas fisicas como retirada, transito, chegada, atraso e divergencia.

## Decisao

MovimentacaoDeEstoque sera Aggregate Root proprio e processo operacional, nao somente evento.

Toda movimentacao devera possuir inicio, transito e fim explicitos. Movimentacoes abertas permanecem visiveis e monitoradas.

A localizacao final da UL somente sera alterada apos confirmacao fisica de chegada.

Estados da MovimentacaoDeEstoque nao devem ser tratados automaticamente como eventos. Alertas derivados podem ser produzidos por monitoramento temporal, projecoes ou servicos operacionais, sem representar necessariamente uma transicao direta do Aggregate Root.

## Alternativas

- Atualizar localizacao instantaneamente: rejeitado.
- Registrar apenas evento de movimentacao concluida: rejeitado.
- Controlar movimentacao como processo operacional completo: aprovado.

## Consequencias

### Consequencias positivas

- Retiradas sem chegada permanecem visiveis.
- Aderencia fisico versus virtual e fortalecida.
- Alertas e escalonamentos podem ser parametrizados.

### Consequencias negativas

- Movimentacoes abertas exigem monitoramento operacional.
- Falhas de confirmacao podem gerar pendencias que precisam de tratamento.

### Riscos e compromissos

- Movimentacoes abertas exigem monitoramento operacional.
- Falhas de confirmacao podem gerar pendencias que precisam de tratamento.
- Nomes de eventos devem permanecer como fatos ocorridos, separados de estados e alertas.

## Impactos

- Unidade Logistica.
- Local de Estoque.
- Disponibilidade.
- Centro de Controle Logistico futuro.
- Auditoria.

## Riscos

- Movimentacoes abertas exigem monitoramento operacional.
- Falhas de confirmacao podem gerar pendencias que precisam de tratamento.

## Pendencias

- Definir modelo fisico e estados definitivos.
- Definir paineis e alertas futuros.
- Nenhuma tela ou endpoint e criado por este DL.

## Relacao com AS-0004

Derivado das secoes 8, 16, 21, 30, 31, 33, 37, 38 e 40 da AS-0004.