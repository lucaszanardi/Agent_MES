# DL-0023 - Unidade Logistica como Agregado Fisico

## Codigo

DL-0023

## Status

Aprovado

## Contexto

A AS-0004 definiu Unidade Logistica como o principal agregado fisico do dominio de Estoque.

## Problema

Materiais, lotes, saldos, locais e documentos nao representam adequadamente o objeto fisico manipulavel na fabrica.

## Decisao

UnidadeLogistica sera Aggregate Root e principal agregado fisico do dominio de Estoque.

A UL possui identidade propria e dimensoes independentes: estado existencial, condicao operacional, localizacao, custodia, estrutura logistica e conteudo.

Homogeneidade sera governada por politica configuravel, incluindo atributos configuraveis como material, lote, validade, serie, condicao de qualidade ou caracteristicas especificas.

Hierarquia de UL sera opcional e configuravel. Genealogia devera ser preservada em agrupamentos, desagrupamentos, divisoes e consolidacoes.

O backend opera sempre sobre UL. O frontend pode ocultar a UL em operacoes simples por meio de UL explicita, implicita ou virtual, mas ocultar a UL na interface nao elimina sua existencia no dominio. Operacoes criticas devem preservar identificacao e rastreabilidade por codigo de barras, QR Code, RFID ou identificador configuravel, sem transferir toda a complexidade do dominio ao operador.

## Alternativas

- Tratar material ou lote como objeto fisico movimentado: rejeitado.
- Codificar regra universal de homogeneidade: rejeitado.
- Permitir UL explicita, implicita ou virtual conforme politica e experiencia operacional: aprovado.

## Consequencias

### Consequencias positivas

- O backend opera sempre sobre UL.
- O frontend pode ocultar a complexidade em operacoes simples.
- Uma UL historica nunca deve ser apagada.

### Consequencias negativas

- A experiencia operacional precisa distinguir quando a UL deve aparecer explicitamente.
- Politicas de homogeneidade mal configuradas podem gerar mistura indevida.

### Riscos e compromissos

- Expor toda a complexidade da UL ao operador pode prejudicar usabilidade.
- Politicas de homogeneidade mal configuradas podem gerar mistura indevida.

## Impactos

- Recebimento.
- Movimentacao.
- Reserva.
- Inventario.
- Rastreabilidade.
- Experiencia operacional.

## Riscos

- Expor toda a complexidade da UL ao operador pode prejudicar usabilidade.
- Politicas de homogeneidade mal configuradas podem gerar mistura indevida.

## Pendencias

- Modelo fisico futuro de UL, Tipo de UL, conteudo, hierarquia e genealogia.
- Desenho de UX para UL explicita, implicita e virtual.

## Relacao com AS-0004

Derivado das secoes 8, 17, 31, 33, 37, 38 e 40 da AS-0004.