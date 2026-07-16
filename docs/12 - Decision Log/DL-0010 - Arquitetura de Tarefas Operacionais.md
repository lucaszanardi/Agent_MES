# DL-0010 - Arquitetura de Tarefas Operacionais

## Codigo

DL-0010

## Status

Aprovado

## Contexto

A AS-0002 definiu que o operador deve executar tarefas guiadas em vez de depender principalmente de acoes isoladas de menu.

## Problema

A operacao baseada somente em menus dificulta priorizacao, distribuicao, acompanhamento, auditoria e orientacao passo a passo do operador.

## Decisao

O operador executara tarefas. O fluxo conceitual sera Evento de negocio -> regra de geracao -> tarefa -> etapas guiadas -> eventos operacionais -> movimentacoes de estoque -> Jornada do Material -> Jornada do Operador -> auditoria.

As tarefas poderao ser automaticas, parametrizadas pelo gestor ou manuais para excecoes controladas. Tarefas manuais devem exigir motivo e auditoria.

Estados sugeridos e aprovados: Pendente, Atribuida, Aceita, Em Execucao, Pausada, Aguardando Validacao, Concluida, Cancelada e Com Divergencia. O modelo definitivo podera ser refinado em sessao futura sem alterar o principio aprovado.

## Alternativas

- Operacao por menu como forma principal: rejeitada para o fluxo operacional assistido.
- Arquitetura baseada em tarefas: aprovada.

## Consequencias

- Tarefas poderao ter prioridade, prazo, etapas, instrucoes, validacoes, alertas e escalonamento.
- Eventos de negocio poderao gerar tarefas automaticamente.
- Gestores configuram modelos e regras de geracao, nao necessariamente cada tarefa individual.

## Impactos

- Recebimento, estoque, transferencias, inventario, producao, qualidade e expedicao.
- Paineis, alertas e coletores.
- Auditoria e relatorios.

## Riscos

- Tarefas duplicadas.
- Tarefa aceita por mais de um operador.
- Excesso de parametrizacao sem governanca.
- Regras incompletas de reatribuicao.

## Pendencias

- Modelo fisico definitivo de Tarefa.
- Modelo fisico definitivo de EventoTarefa.
- Tratamento de concorrencia.
- Lock de tarefas.
- SLA e escalonamento.
- Prioridade automatica.
- Regras completas de reatribuicao.
- Integracao com notificacoes.
- Migrations, endpoints e propriedades definitivas.

## Relacao com AS-0002

Derivado das secoes 16, 17, 18, 22, 23, 24 e 25 da AS-0002.
