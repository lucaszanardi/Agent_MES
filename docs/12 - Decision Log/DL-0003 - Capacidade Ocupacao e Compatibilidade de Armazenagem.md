# DL-0003 - Capacidade, Ocupacao e Compatibilidade de Armazenagem

## Status

Aprovado

## Data

2026-07-15

## Origem

Sessao de arquitetura registrada em `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`.

## Contexto

Enderecos de estoque precisam validar se uma localizacao suporta determinado produto, lote, embalagem, unidade logistica ou volume operacional. A documentacao existente identifica capacidade em `LocalizacaoEstoque`, mas nao confirma regra operacional completa.

## Decisoes Aprovadas

1. A capacidade sera parametrizavel por peso, volume, quantidade ou unidade logistica.
2. Uma localizacao podera possuir multiplas restricoes de capacidade.
3. O sistema devera considerar conversoes entre unidades, embalagens e unidades logisticas.
4. A ocupacao sera apresentada visualmente.
5. Regras de mistura serao parametrizaveis: multiplos produtos, lotes, validades, proprietarios e limite de SKUs.
6. Serao suportados enderecamentos fixo, dinamico e preferencial.
7. Areas e localizacoes poderao possuir regras de compatibilidade.
8. Estrategias de armazenagem serao configuraveis pelo usuario.
9. A primeira versao utilizara regras parametrizadas.

## Justificativa

Ambientes industriais podem limitar enderecos por peso, volume, quantidade fisica ou unidade logistica. Tambem podem restringir mistura de produtos, lotes, validades e proprietarios por requisitos de seguranca, qualidade, rastreabilidade ou eficiencia operacional.

O uso de regras parametrizadas na primeira versao permite entregar controle operacional sem antecipar recomendacao inteligente ou automacoes mais complexas.

## Impacto Tecnico

- Sera necessario modelar regras de capacidade, ocupacao, conversao, mistura e compatibilidade em desenho tecnico futuro.
- Conversoes entre unidades, embalagens e unidades logisticas exigem fonte confiavel de fatores de conversao.
- O calculo de ocupacao devera considerar saldo, reservas, bloqueios, transito e regras aplicaveis quando esses conceitos forem modelados.
- Este Decision Log nao define tabelas, colunas, algoritmos finais, indices ou endpoints.

## Impacto Funcional

- Usuarios poderao restringir onde determinados materiais podem ser armazenados.
- A interface devera indicar ocupacao de forma visual.
- O operador devera ver apenas opcoes tecnicamente validas ou receber bloqueio quando a localizacao nao cumprir as regras.
- Estrategias fixas, dinamicas e preferenciais permitirao adaptar a operacao por area, produto ou politica operacional.

## Entidades Afetadas

- `AreaEstoque`
- `LocalizacaoEstoque`
- `Produto`
- `LoteMaterial`
- `SaldoEstoque`
- `UnidadeMedida`
- `MovimentoEstoque`
- `ReservaEstoque`
- `BloqueioEstoque`

Observacao: entidades especificas para capacidade, compatibilidade, embalagem, unidade logistica e estrategia permanecem como pendencia tecnica.

## Telas Afetadas

- Cadastro de area de estoque.
- Cadastro de localizacao de estoque.
- Cadastros de produto, unidade de medida e lote quando houver impacto de compatibilidade.
- Operacoes de entrada, transferencia, reserva, bloqueio, inventario e ajuste.
- Visualizacao operacional de ocupacao.

## Regras de Validacao

- Uma localizacao pode ter mais de uma restricao de capacidade.
- A capacidade deve poder ser avaliada por peso, volume, quantidade ou unidade logistica.
- A validacao deve considerar conversoes aplicaveis entre unidades, embalagens e unidades logisticas.
- Regras de mistura devem ser parametrizaveis para multiplos produtos, lotes, validades, proprietarios e limite de SKUs.
- Areas e localizacoes podem aplicar regras de compatibilidade.
- A primeira versao deve usar regras parametrizadas.

## Consequencias Positivas

- Maior controle sobre ocupacao e seguranca de armazenagem.
- Base funcional para operacao com restricoes industriais reais.
- Preparacao para recomendacao futura sem depender dela na primeira versao.
- Flexibilidade para diferentes politicas de armazenagem.

## Consequencias Negativas

- A configuracao pode ficar complexa para usuarios sem boa interface.
- Conversoes incorretas podem gerar decisao operacional errada.
- Regras conflitantes precisarao de prioridade e diagnostico claro.

## Decisoes Futuras Relacionadas

- Definir entidades ou estruturas para capacidade, regra de mistura, compatibilidade e estrategia.
- Definir como proprietario sera representado, caso faca parte do escopo.
- Definir modelo de embalagem e unidade logistica.
- Definir calculo oficial de ocupacao e fonte dos saldos considerados.
- Definir prioridade entre regra de area, regra de localizacao, regra de produto e estrategia de armazenagem.
