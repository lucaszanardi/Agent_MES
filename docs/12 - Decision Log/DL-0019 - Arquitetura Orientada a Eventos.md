# DL-0019 - Arquitetura Orientada a Eventos

## Codigo

DL-0019

## Status

Aprovado

## Contexto

A AS-0003 definiu que mudancas operacionais relevantes devem chegar rapidamente a telas, dispositivos, modulos internos e sistemas externos quando aplicavel.

## Problema

Processos de polling ou sincronizacao lenta podem atrasar informacoes criticas de PCP, execucao, estoque, qualidade e tarefas operacionais.

## Decisao

Mudancas relevantes serao propagadas por eventos internos e externos.

Exemplos conceituais:

- SequenciamentoAlterado;
- OrdemLiberada;
- OrdemIniciada;
- LoteBloqueado;
- MaterialDisponibilizado;
- TarefaAtribuida.

Nao fica definida tecnologia fisica de eventos nesta decisao.

## Alternativas

- Atualizacao apenas por consultas periodicas: rejeitada para mudancas operacionais relevantes.
- Eventos internos e externos para propagacao de mudancas relevantes: aprovado.

## Consequencias

- PCP podera alterar sequenciamento e a mudanca devera ser propagada rapidamente.
- Modulos internos poderao reagir a eventos sem acoplamento direto.
- Sistemas externos poderao receber mensagens de saida quando a politica permitir.

## Impactos

- Sequenciamento.
- Ordens de producao.
- Estoque e lotes.
- Tarefas.
- Apontamentos.
- Paineis e dispositivos.
- Integracoes externas.

## Riscos

- Eventos duplicados ou fora de ordem precisam de idempotencia e versionamento.
- Sem observabilidade, falhas de propagacao podem ficar ocultas.

## Pendencias

- Definir tecnologia fisica de eventos.
- Definir padrao de publicacao, consumo e reprocessamento.
- Definir contratos e versionamento de eventos.
- SignalR, WebSocket ou alternativa permanecem fora do escopo desta sessao.

## Relacao com AS-0003

Derivado das secoes 6.9, 6.12, 7 e 11 da AS-0003.
