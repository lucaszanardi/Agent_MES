# DL-0038 - Persistencia de Local de Estoque e Relacao com Legado

## Codigo

DL-0038

## Status

Aprovado

## Data

2026-07-27

## Contexto

A primeira vertical funcional de Estoque precisa persistir `LocalDeEstoque` para validar origem e destino de movimentacoes. O sistema legado ja possui `CLOCALIZACAOESTOQUE`, usada por cadastros e operacoes existentes, com hierarquia, almoxarifado, area, tipo, permissoes e bloqueio.

## Problema

Era necessario decidir se o novo `LocalDeEstoque` seria persistido em tabela propria, reutilizaria diretamente `CLOCALIZACAOESTOQUE` ou manteria uma tabela nova com correlacao controlada para o legado.

## Decisao

Persistir `LocalDeEstoque` em tabela nova `CLOCALDEESTOQUE`, pertencente ao novo dominio de Estoque.

A tabela pode manter correlacao opcional com `CLOCALIZACAOESTOQUE.Id`, mas sem FK fisica obrigatoria e sem escrita da nova vertical em `CLOCALIZACAOESTOQUE`.

`CLOCALIZACAOESTOQUE` permanece como tabela legada e pode ser usada somente como insumo por camada anticorrupcao, sincronizacao ou reconciliacao futura.

## Alternativas consideradas

| Alternativa | Resultado |
|---|---|
| Reutilizar diretamente `CLOCALIZACAOESTOQUE` como tabela do novo AR | Rejeitada, pois mistura ownership legado e novo, alem de acoplar invariantes da nova vertical a cadastros existentes. |
| Criar somente `CLOCALDEESTOQUE` sem correlacao com legado | Rejeitada como opcao inicial, pois dificulta coexistencia, migracao incremental e reconciliacao operacional. |
| Criar `CLOCALDEESTOQUE` com correlacao opcional para legado | Aprovada. |

## Consequencias positivas

- Preserva a fronteira do novo dominio de Estoque.
- Evita escrita concorrente da nova vertical em tabela legada.
- Permite migracao incremental e reconciliacao com dados existentes.
- Reduz risco de transformar cadastro legado em Aggregate Root concorrente sem justificativa.

## Consequencias negativas

- Exige sincronizacao, carga inicial ou camada anticorrupcao para popular a nova tabela.
- Pode haver divergencia temporaria entre local legado e local novo se a governanca de sincronizacao nao for definida.
- Aumenta o numero de tabelas de Estoque na transicao.

## Riscos

- Divergencia entre `CLOCALIZACAOESTOQUE` e `CLOCALDEESTOQUE` sem processo de reconciliacao.
- Uso indevido da correlacao como FK operacional forte.
- Escritas acidentais no legado pela nova vertical.

## Referencias cruzadas

- AS-0004
- AS-0005
- AS-0007
- DL-0024
- DL-0037
- `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Primeira Vertical de Estoque.md`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs`
- `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LocalizacaoEstoqueConfig.cs`

## Relacao com a AS

Esta decisao resolve a pendencia de persistencia fisica de `LocalDeEstoque` para a primeira vertical funcional de Estoque.