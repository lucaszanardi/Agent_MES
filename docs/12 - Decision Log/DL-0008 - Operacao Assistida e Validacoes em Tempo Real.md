# DL-0008 - Operacao Assistida e Validacoes em Tempo Real

## Codigo

DL-0008

## Status

Aprovado

## Contexto

A AS-0002 definiu que a operacao deve ser guiada para reduzir erros, simplificar treinamento e preservar rastreabilidade.

## Problema

Operacoes livres sem validacao em tempo real aumentam risco de erro de localizacao, produto, lote, quantidade, permissao e compatibilidade.

## Decisao

O sistema conduzira o operador passo a passo, validando cada etapa e apresentando a proxima acao esperada. Na transferencia assistida, o fluxo inclui leitura da origem, validacao da origem, leitura do material, validacao de produto, lote e serial, confirmacao de quantidade, retirada, transito, leitura do destino, validacao de capacidade e compatibilidade, e confirmacao de armazenagem.

Serao suportados modos Assistido, Livre e Automatizado. O modo livre nao elimina auditoria nem validacoes obrigatorias. A arquitetura nao dependera de uma unica tecnologia de leitura.

## Alternativas

- Operacao livre como padrao: rejeitada para operadores e processos criticos.
- Operacao assistida: aprovada para reduzir erros.
- Automacao plena imediata: adiada como evolucao futura.

## Consequencias

- O sistema deve impedir ou alertar antes da confirmacao quando houver inconsistencia.
- Validacoes em tempo real abrangem localizacao, produto, lote, serial, quantidade, ordem de producao, capacidade, compatibilidade, status da localizacao, material bloqueado, reserva, validade, permissao do operador, Role, Equipe Operacional, documento de origem, saldo disponivel, transito e regras parametrizadas.

## Impactos

- Coletores.
- Telas futuras de operacao.
- Alertas operacionais.
- Fluxos de entrada, saida, transferencia, inventario e divergencias.

## Riscos

- Uso excessivo do modo livre.
- Alertas excessivos.
- Telas complexas.
- Indisponibilidade de coletor.
- Perda de conexao durante a operacao.

## Pendencias

- Estrategia offline dos coletores.
- Desenho final das telas.
- Suporte tecnico a QR Code, codigo de barras, RFID e NFC.
- Endpoints, migrations e propriedades definitivas.

## Relacao com AS-0002

Derivado das secoes 3, 11, 12, 13, 14, 15, 22, 23, 24 e 25 da AS-0002.
