# Contrato de Transicao para Identidade Unica de Localizacao de Estoque

## Status

Documental. Implementacao pendente.

## Referencias

- AS-0010 - Unificacao da Identidade Fisica e Operacional dos Locais de Estoque.
- DL-0044 - LocalizacaoEstoque como Identidade Unica Fisica e Operacional.
- DL-0043 - Planta como Escopo Superior de Producao, Armazens e Estoque.

## Estado atual

| Tabela | Estado informado |
|---|---|
| `CLOCALIZACAOESTOQUE` | 18 registros. |
| `CLOCALDEESTOQUE` | 0 registros. |
| `CUNIDADELOGISTICA` | 0 registros. |
| `CMOVIMENTACAODEESTOQUE` | 0 registros. |

Nao ha dados operacionais para migrar.

## Estado alvo

- `LocalizacaoEstoque` como identidade unica fisica e operacional.
- `CLOCALIZACAOESTOQUE` como fonte de verdade de enderecos.
- Unidade Logistica referenciando `LocalizacaoEstoque`.
- Movimentacao usando origem/destino em `LocalizacaoEstoque`.
- `PlantId` derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`.
- `WarehouseId` equivalente a `AlmoxarifadoId`.
- `CLOCALDEESTOQUE` removida ao final da transicao, somente apos validacao humana.

## Etapas de transicao

| Etapa | Acao | Criterio de parada | Rollback conceitual |
|---|---|---|---|
| 1 | Refatorar dominio sem remover tabela | Dominio compila e testes cobrem elegibilidade | Reverter refatoracao antes de migration |
| 2 | Ajustar repositories e handlers | Consultas e comandos usam `LocalizacaoEstoque` | Restaurar referencias anteriores antes de deploy |
| 3 | Ajustar contratos HTTP | APIs documentadas sem `LocalDeEstoque` como identidade | Manter contrato anterior enquanto nao publicado |
| 4 | Ajustar testes | Casos `ARMAZENA`, `ESTRUTURAL`, `BLOQUEADO` cobertos | Reabrir AS se regra conflitar com dominio |
| 5 | Gerar migration incremental | Migration revisada sem DDL destrutivo prematuro | Descartar migration antes de aplicar |
| 6 | Revisar SQL | Script revisado por humano | Nao aplicar script |
| 7 | Ajustar frontend | Telas usam cadastro unico de localizacao | Manter tela antiga ate backend estabilizado |
| 8 | Validar banco vazio operacional | Confirmar `CLOCALDEESTOQUE`, UL e movimentacoes vazias | Pausar remocao se houver dados |
| 9 | Remover `CLOCALDEESTOQUE` | Refatoracao completa, testes e validacao humana | Criar migration de rollback somente se necessario |
| 10 | Atualizar documentacao factual | Documentos refletem estado implementado | Corrigir documentacao se implementacao divergir |

## Regras de elegibilidade

Apenas `LocalizacaoEstoque` com classificacao efetiva `ARMAZENA` pode receber Unidade Logistica e ser usada como origem/destino.

`ESTRUTURAL` e `BLOQUEADO` nao recebem estoque e nao podem ser origem/destino.

## Dependencias operacionais para alteracao hierarquica

Antes de adicionar filho abaixo de localizacao `ARMAZENA`, a implementacao devera validar ausencia de:

- Unidade Logistica posicionada diretamente;
- saldo diretamente associado;
- movimentacao ativa;
- reserva;
- inventario em andamento;
- bloqueio operacional incompativel.

## Itens fora deste contrato

- Nomes fisicos definitivos de colunas e FKs.
- Nulabilidade de `LocalizacaoEstoqueId` em estados transitorios de UL.
- Detalhamento da coluna/token de concorrencia.
- DDL final de remocao de `CLOCALDEESTOQUE`.
- Implementacao em backend/frontend.

## Restricoes

Este contrato nao cria migration, nao altera banco, nao altera backend, nao altera frontend e nao declara `CLOCALDEESTOQUE` removida.