# DL-0015 - Modelo Canonico e Adaptadores

## Codigo

DL-0015

## Status

Aprovado

## Contexto

A AS-0003 definiu que sistemas externos possuem formatos, nomenclaturas, autenticacoes, payloads e regras tecnicas diferentes.

## Problema

Permitir que servicos de aplicacao recebam diretamente payloads especificos de fornecedores externos levaria detalhes tecnicos de integracao para dentro do MES.

## Decisao

Sistemas externos serao traduzidos por adaptadores para contratos canonicos estaveis antes de alcancar os servicos de aplicacao do MES.

O fluxo conceitual de entrada sera Sistema Externo -> Adaptador -> Contrato Canonico -> Hub de Sincronizacao -> Servico de Aplicacao -> Dominio MES.

O fluxo de saida sera Dominio MES -> Evento ou mensagem de saida -> Hub de Sincronizacao -> Adaptador -> Sistema Externo.

## Alternativas

- Expor servicos de aplicacao diretamente a payloads externos: rejeitado.
- Criar adaptadores com regra de negocio do MES: rejeitado.
- Traduzir sistemas externos para contratos canonicos estaveis: aprovado.

## Consequencias

- O dominio fica isolado de formatos externos.
- Adaptadores podem existir para APIs, arquivos, mensageria ou outros meios.
- A evolucao de um fornecedor externo nao deve exigir mudancas diretas no dominio.

## Impactos

- Contratos de entrada e saida.
- Hub de Sincronizacao.
- Adaptadores futuros.
- Auditoria de mensagem original e mensagem canonica.

## Riscos

- Contratos canonicos mal definidos podem gerar acoplamento indireto.
- Excesso de transformacoes pode dificultar diagnostico se a auditoria for incompleta.

## Pendencias

- Definir contratos canonicos fisicos.
- Definir padroes de versao de contrato.
- Implementar adaptadores somente em etapa futura aprovada.
- Nenhum adaptador especifico e criado nesta fase.

## Relacao com AS-0003

Derivado das secoes 6.4, 6.13, 7, 9 e 11 da AS-0003.
