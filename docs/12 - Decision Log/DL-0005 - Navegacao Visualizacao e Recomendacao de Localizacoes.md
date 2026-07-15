# DL-0005 - Navegacao, Visualizacao e Recomendacao de Localizacoes

## Status

Aprovado

## Data

2026-07-15

## Origem

Sessao de arquitetura registrada em `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`.

## Contexto

A operacao de estoque precisa selecionar localizacoes com seguranca e clareza. A primeira versao deve apoiar o operador com regras parametrizadas e opcoes tecnicamente validas, sem assumir recomendacao automatica por IA como requisito imediato.

## Decisoes Aprovadas

1. A navegacao operacional sera por Armazem e depois Area.
2. A estrutura sera apresentada visualmente.
3. A ocupacao sera apresentada visualmente.
4. Na primeira versao, o operador escolhera entre localizacoes tecnicamente validas.
5. A arquitetura ficara preparada para recomendacao futura por IA.
6. Serao registrados dados necessarios para treinamento futuro de modelos de recomendacao.
7. A solucao ficara preparada para recomendacao inteligente futura baseada em capacidade, compatibilidade, estrategia, proximidade, FIFO, FEFO, curva ABC e historico operacional.

## Justificativa

A navegacao por armazem e area acompanha a forma natural de operacao e reduz a complexidade de selecao. A apresentacao visual da estrutura e da ocupacao ajuda o operador a entender onde pode armazenar, retirar ou transferir materiais.

A primeira versao prioriza regras parametrizadas e decisao humana assistida. A preparacao para IA evita fechar a arquitetura, mas nao antecipa automatizacoes ou modelos sem dados historicos suficientes.

## Impacto Tecnico

- A interface futura devera consultar localizacoes por armazem e area.
- A visualizacao devera representar estrutura, estado, ocupacao e validade tecnica da localizacao.
- O registro de dados para treinamento futuro dependera de modelagem de eventos, decisoes operacionais e resultados.
- FIFO, FEFO, curva ABC, proximidade e historico operacional ficam como criterios futuros, sem implementacao definida neste Decision Log.

## Impacto Funcional

- O operador selecionara localizacoes tecnicamente validas na primeira versao.
- A operacao tera suporte visual para entender hierarquia e ocupacao.
- O sistema podera evoluir para recomendacao inteligente sem mudar a decisao operacional inicial.

## Entidades Afetadas

- `Almoxarifado`
- `AreaEstoque`
- `LocalizacaoEstoque`
- `SaldoEstoque`
- `MovimentoEstoque`
- `LoteMaterial`
- `Produto`

Observacao: dados de treinamento, eventos de recomendacao e criterios de IA permanecem como pendencia tecnica e nao definem entidade definitiva.

## Telas Afetadas

- Selecao operacional por armazem.
- Selecao operacional por area.
- Visualizacao de arvore ou mapa de localizacoes.
- Operacoes de entrada, saida, transferencia, reserva, bloqueio, inventario e ajuste.
- Futuras telas de recomendacao ou analise operacional, caso aprovadas.

## Regras de Validacao

- A primeira versao deve apresentar somente localizacoes tecnicamente validas para a operacao.
- A validade tecnica deve considerar regras parametrizadas aprovadas.
- A recomendacao por IA nao substitui a regra parametrizada na primeira versao.
- Dados para treinamento futuro devem ser registrados sem criar decisao automatica obrigatoria.

## Consequencias Positivas

- Operacao mais orientada ao fluxo real de armazem e area.
- Menor risco de escolha de localizacao invalida.
- Base evolutiva para recomendacao inteligente.
- Possibilidade de usar historico operacional para melhoria futura.

## Consequencias Negativas

- Visualizacao e ocupacao exigem desenho de UX mais elaborado.
- Registro de dados para IA pode aumentar volume de eventos e necessidade de governanca.
- Recomendacao futura dependera de qualidade dos dados registrados desde a primeira versao.

## Decisoes Futuras Relacionadas

- Definir quais eventos operacionais serao registrados para treinamento.
- Definir criterios de proximidade, FIFO, FEFO e curva ABC.
- Definir se a recomendacao futura sera sugestiva, obrigatoria ou parametrizavel.
- Definir metricas para avaliar qualidade das recomendacoes.
