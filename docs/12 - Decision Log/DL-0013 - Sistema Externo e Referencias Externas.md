# DL-0013 - Sistema Externo e Referencias Externas

## Codigo

DL-0013

## Status

Aprovado

## Contexto

A AS-0003 definiu que o MES podera integrar-se com ERPs e tambem com WMS, APS, CRM, RH, SCADA, PIMS, LIMS, marketplaces, APIs, arquivos, aplicacoes moveis e sistemas proprios.

## Problema

Um unico campo de codigo externo nas entidades principais nao representa multiplos sistemas, empresas, estabelecimentos, origens e historicos de sincronizacao.

## Decisao

Identificadores externos serao mantidos fora das entidades principais do dominio, por meio de referencias externas associadas a sistemas externos.

As referencias deverao considerar conceitualmente Sistema Externo, tipo da entidade, identificador externo, identificador interno, empresa ou estabelecimento, status e data da ultima sincronizacao.

## Alternativas

- Adicionar um campo unico `CodigoErp` nas entidades principais: rejeitado.
- Usar o codigo externo como identificador interno unico do MES: rejeitado.
- Associar identificadores externos por referencia desacoplada: aprovado.

## Consequencias

- Um registro interno podera ser associado a multiplos sistemas externos.
- O codigo interno do MES permanece preservado.
- O escopo por empresa e estabelecimento podera ser tratado sem duplicar entidades principais.
- Integracoes futuras poderao evoluir sem alterar o conceito central do dominio.

## Impactos

- Produto, Cliente, Fornecedor, Pedido, Ordem de Producao, Estrutura, Roteiro, Lote, Estoque e demais entidades integraveis.
- Auditoria e rastreabilidade de sincronizacao.
- Politicas de governanca por sistema, empresa e estabelecimento.

## Riscos

- Duplicidade de referencias externas se nao houver regras de unicidade futuras.
- Ambiguidade quando a empresa, estabelecimento ou sistema de origem nao forem informados.

## Pendencias

- Modelo fisico de Sistema Externo.
- Modelo fisico de Referencia Externa.
- Regras de unicidade, status, escopo e auditoria.
- Migrations, endpoints e propriedades definitivas.

## Relacao com AS-0003

Derivado das secoes 6.2, 6.3, 6.10, 6.15, 9 e 11 da AS-0003.
