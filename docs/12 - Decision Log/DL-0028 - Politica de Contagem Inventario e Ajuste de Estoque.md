# DL-0028 - Politica de Contagem Inventario e Ajuste de Estoque

## Codigo

DL-0028

## Status

Aprovado

## Contexto

A AS-0004 separou politica de contagem, inventario, contagem fisica, reconciliacao e ajuste de estoque.

## Problema

Misturar regras de frequencia, execucao de contagem, comparacao e ajuste pode permitir alteracoes sem governanca ou auditoria.

## Decisao

PoliticaDeContagem e Inventario serao Aggregate Roots distintos.

PoliticaDeContagem possui identidade, versao, vigencia, estado ativo ou inativo, criterios de selecao, frequencia, escopo, tolerancias, regras de recontagem, regras de aprovacao e ciclo de vida proprio.

Somente versoes ativas e vigentes geram Inventarios. Alteracoes relevantes geram nova versao ou preservam historico equivalente, nao modificam retroativamente execucoes ja iniciadas e nao apagam Inventarios ja gerados.

TarefaDeContagem permanecera inicialmente como entidade interna do agregado Inventario.

TarefaDeContagem pode ser apresentada operacionalmente como tarefa ao usuario e se relaciona ao conceito geral de TarefaOperacional ja utilizado no Project Book, mas nao sera inicialmente Aggregate Root independente.

Contagem fisica, reconciliacao e ajuste de estoque sao conceitos diferentes.

Ajustes de estoque serao operacoes formais, autorizadas e auditaveis.

## Alternativas

- Embutir frequencias fixas no codigo: rejeitado.
- Tratar contagem como ajuste automatico: rejeitado.
- Promover TarefaDeContagem a Aggregate Root nesta etapa: rejeitado.
- Separar politica, inventario, reconciliacao e ajuste formal: aprovado.

## Consequencias

### Consequencias positivas

- Politicas podem gerar inventarios ou tarefas conforme configuracao.
- Contagem registra o encontrado.
- Reconciliacao compara fisico e virtual.
- Ajuste exige autorizacao, justificativa e auditoria.

### Consequencias negativas

- Muitos inventarios ou tarefas podem exigir revisao futura da fronteira de TarefaDeContagem.
- Ajustes sem aprovacao adequada podem comprometer confiabilidade do saldo.

### Riscos e compromissos

- Muitos inventarios ou tarefas podem exigir revisao futura da fronteira de TarefaDeContagem.
- Ajustes sem aprovacao adequada podem comprometer confiabilidade do saldo.
- Criterios, frequencia e tolerancias precisam respeitar parametros validos e produzir escopo interpretavel.

## Impactos

- Inventario geral e rotativo.
- Tarefas de contagem.
- Auditoria.
- Acuracidade.
- Projecoes de saldo.

## Riscos

- Muitos inventarios ou tarefas podem exigir revisao futura da fronteira de TarefaDeContagem.
- Ajustes sem aprovacao adequada podem comprometer confiabilidade do saldo.

## Pendencias

- Politicas detalhadas de contagem.
- Modelo fisico de inventario, tarefas, recontagens e ajustes.
- Possivel revisao futura de TarefaDeContagem como agregado separado.

## Relacao com AS-0004

Derivado das secoes 23, 24, 25, 27, 31, 32, 37, 38 e 40 da AS-0004.