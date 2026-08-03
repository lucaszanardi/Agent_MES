
## Planta e recursos produtivos

Referencias: AS-0009 e DL-0043.

Linhas de producao pertencem funcionalmente a Planta. Elas podem estar fisicamente localizadas dentro de um Armazem, Galpao ou Area Mista.

Buffers, supermercados de linha e estoques intermediarios pertencem ao escopo da Planta e podem ser representados por enderecos de estoque.

O vinculo tecnico de `CentroTrabalho`, linha, celula, maquina, ordem ou roteiro com Planta ainda nao esta implementado e devera ser detalhado em decisoes futuras do modulo de Producao.

## Impacto da identidade unica de LocalizacaoEstoque

AS-0010 e DL-0044 definem que buffers, supermercados de linha e estoques intermediarios deverao usar `LocalizacaoEstoque` elegivel como endereco operacional quando forem representados por estoque.

O modulo de Producao ainda devera detalhar o vinculo entre linhas, recursos, buffers e localizacoes. Nenhuma implementacao de Producao foi realizada nesta decisao documental.
