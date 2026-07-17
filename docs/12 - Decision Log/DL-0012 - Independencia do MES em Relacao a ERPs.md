# DL-0012 - Independencia do MES em Relacao a ERPs

## Codigo

DL-0012

## Status

Aprovado

## Contexto

A AS-0003 definiu que o MES/MOM deve operar em cenarios standalone, integrados e hibridos, sem depender de um ERP especifico.

## Problema

Campos, servicos ou regras especificas de fornecedores externos no dominio criariam acoplamento indevido, dificultariam comercializacao para diferentes clientes e enfraqueceriam a consistencia industrial do MES.

## Decisao

O nucleo e o dominio do MES nao conhecerao fornecedores, campos, estruturas ou servicos especificos de ERP.

O dominio deve conhecer somente conceitos industriais e empresariais proprios, como Produto, Cliente, Fornecedor, Pedido, Demanda, Ordem de Producao, Lote, Estoque, Roteiro, Estrutura de Produto, Apontamento, Evento e Tarefa.

## Alternativas

- Incluir campos especificos de ERP diretamente nas entidades do dominio: rejeitado.
- Criar servicos de ERP dentro do dominio: rejeitado.
- Manter o dominio independente e tratar fornecedores em camadas de integracao: aprovado.

## Consequencias Positivas

- Maior independencia comercial do produto.
- Menor acoplamento a fornecedores externos.
- Melhor capacidade de operar sem ERP.
- Base mais adequada para multiplas integracoes futuras.

## Consequencias Negativas

- A integracao exige camada propria de mapeamento e governanca.
- O diagnostico de divergencias entre sistemas passa a depender de referencias externas e auditoria.

## Restricoes

- Nao criar campos como `CodigoProtheus`, `CodigoSAP`, `FilialProtheus` ou `CentroSAP` no dominio.
- Nao criar interfaces como `ISapService` ou `IProtheusService` no dominio.
- Nao incorporar regras especificas de ERP como regra industrial do MES.

## Impactos

- Cadastros mestres.
- Pedidos e demandas.
- Ordens de producao.
- Estruturas e roteiros.
- Estoque, lotes, movimentacoes e apontamentos.
- Integracoes futuras.

## Riscos

- Necessidade de disciplina arquitetural para impedir acoplamento indireto.
- Tentacao de resolver demandas de cliente com campos especificos no dominio.

## Pendencias

- Definir modelos fisicos futuros de integracao sem contaminar o dominio.
- Definir validacoes tecnicas em implementacoes futuras.
- Migrations, endpoints, propriedades e servicos permanecem fora desta decisao documental.

## Relacao com AS-0003

Derivado das secoes 5, 6.1, 6.17, 7, 9 e 11 da AS-0003.
