# DL-0011 - Distribuicao de Tarefas por Identity, Role e Equipe Operacional

## Codigo

DL-0011

## Status

Aprovado

## Contexto

A AS-0002 definiu que tarefas operacionais podem ser distribuidas por usuario especifico, Role, Equipe Operacional ou Equipe Operacional com Role obrigatoria.

## Problema

Permissao de acesso e distribuicao de trabalho sao conceitos diferentes. Role define se o usuario pode executar; Equipe Operacional define para qual grupo de trabalho a tarefa deve ser enviada.

## Decisao

ASP.NET Identity continuara responsavel por autenticacao, usuarios, Role unica, autorizacao, permissoes, menus dinamicos e acesso as acoes. Nao sera criada outra solucao de autenticacao ou autorizacao.

Equipes Operacionais serao conceito complementar ao Identity. Um usuario podera pertencer a uma ou mais equipes; a equipe nao substitui a Role; tarefas poderao exigir equipe e Role; equipe inativa nao recebe novas tarefas; remocao do usuario da equipe nao apaga historico; tarefa ja aceita permanece vinculada ao operador; menus continuam controlados por Role; equipes filtram o contexto operacional.

## Alternativas

- Usar apenas Role para distribuicao de trabalho: rejeitado por misturar permissao e contexto operacional.
- Criar nova autenticacao/autorizacao: rejeitado.
- Complementar Identity com Equipe Operacional: aprovado.

## Consequencias

- Menus continuam controlados por Role.
- Tarefas podem ser roteadas por equipe, usuario e Role obrigatoria.
- Historico operacional deve preservar vinculos mesmo apos mudancas de equipe.

## Impactos

- Usuarios.
- Roles.
- Equipes.
- Tarefas.
- Auditoria.
- Paineis operacionais.

## Riscos

- Equipe ou Role incorreta.
- Tarefa enviada para grupo inadequado.
- Historico perdido por remocao de usuario de equipe.
- Politicas incompletas de autorizacao.

## Pendencias

- Modelo definitivo de EquipeOperacional.
- Modelo de associacao usuario-equipe.
- Politicas completas de autorizacao.
- Regras de equipe inativa e historico.
- Migrations, endpoints e propriedades definitivas.

## Relacao com AS-0002

Derivado das secoes 19, 20, 21, 22, 23, 24 e 25 da AS-0002.
