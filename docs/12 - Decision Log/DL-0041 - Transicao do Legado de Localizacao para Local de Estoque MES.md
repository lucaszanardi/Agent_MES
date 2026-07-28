# DL-0041 - Transicao do Legado de Localizacao para Local de Estoque MES

## Codigo

DL-0041

## Status

Aprovado

## Data

2026-07-27

## Contexto

A primeira vertical funcional de Estoque possui dominio e aplicacao abstrata implementados e decisoes de persistencia aprovadas. A implementacao existente de Locais de Estoque usa `LocalizacaoEstoque` e a tabela `CLOCALIZACAOESTOQUE`, com telas, controllers, services, repositories, mappings e migrations legadas.

DL-0038 decidiu que `LocalDeEstoque` sera persistido em `CLOCALDEESTOQUE`, com correlacao opcional para `CLOCALIZACAOESTOQUE.Id`, sem FK fisica obrigatoria e sem escrita da nova vertical no legado.

## Problema

Era necessario definir a estrategia definitiva de transicao entre o legado de localizacao e o novo dominio MES, incluindo ownership, migracao de dados, fonte da verdade e classificacao de ocupacao Livre/Ocupado.

## Decisao

Adotar a estrategia B: congelamento semantico do legado, nova implementacao paralela, carga inicial/migracao, correlacao controlada e substituicao gradual.

`CLOCALIZACAOESTOQUE` permanece tabela legada e fonte cadastral atual ate a virada. `CLOCALDEESTOQUE` sera a fonte MES futura para comandos da primeira vertical. A nova vertical nao escreve em `CLOCALIZACAOESTOQUE`.

`CLOCALDEESTOQUE` deve ser alimentada inicialmente por carga a partir de `CLOCALIZACAOESTOQUE`, `CAREAESTOQUE`, `CALMOXARIFADO` e `CTIPOLOCALIZACAO`, mantendo correlacao opcional com o identificador legado.

Livre/Ocupado nao deve ser persistido como estado autoritativo do local. Deve ser calculado por projecao a partir de `UnidadeLogistica.LocalAtualId`, eventos confirmados, reserva ativa e reconciliacao quando necessaria.

## Alternativas consideradas

| Alternativa | Resultado |
|---|---|
| A - Substituicao imediata | Rejeitada pelo alto risco operacional e impacto nas telas/fluxos existentes. |
| B - Congelamento do legado + nova implementacao paralela + migracao + substituicao | Aprovada. |
| C - Refatoracao gradual da implementacao existente | Rejeitada como estrategia principal por risco de contaminar o novo dominio com CRUD legado. |
| Persistir Livre/Ocupado no local | Rejeitada por risco de divergencia com ULs, movimentacoes e historico. |
| Calcular Livre/Ocupado por projecao | Aprovada. |

## Consequencias positivas

- Preserva operacao legada durante a transicao.
- Mantem ownership claro entre legado e novo dominio MES.
- Evita promover `LocalizacaoEstoque` a Aggregate Root concorrente.
- Permite migracao/reconciliacao controlada e reversivel.
- Evita divergencia estrutural ao calcular ocupacao a partir de fatos operacionais.

## Consequencias negativas

- Exige carga inicial e mapa de correlacao entre IDs legados e novos.
- Exige convivencia temporaria de duas representacoes de local.
- Exige governanca para impedir evolucao semantica divergente do legado durante a transicao.
- Telas legadas precisam ser adaptadas ou substituidas gradualmente.

## Riscos

- Dados divergentes se o legado continuar recebendo alteracoes sem sincronizacao definida.
- Uso indevido de `CLOCALIZACAOESTOQUE` como fonte de comandos da nova vertical.
- Ocupacao exibida de forma inconsistente se a projecao nao for reconstruivel/reconciliavel.
- Exclusao fisica de local legado pode prejudicar historico/correlacao.

## Referencias cruzadas

- AS-0004
- AS-0005
- AS-0006
- AS-0007
- DL-0031
- DL-0037
- DL-0038
- DL-0039
- DL-0040
- `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Primeira Vertical de Estoque.md`
- `MES-ProjectBook/docs/05 - Estoque/Inventario da Implementacao Legada de Locais de Estoque.md`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/Estoque/LocaisDeEstoque/LocalDeEstoque.cs`

## Relacao com a AS

Esta decisao consolida a transicao do legado de Localizacao de Estoque para o novo `LocalDeEstoque` MES e remove a ultima ambiguidade arquitetural antes da implementacao de infraestrutura da primeira vertical.