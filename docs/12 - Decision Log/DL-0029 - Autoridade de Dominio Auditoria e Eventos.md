# DL-0029 - Autoridade de Dominio Auditoria e Eventos

## Codigo

DL-0029

## Status

Aprovado

## Contexto

A AS-0004 definiu que autorizacao de usuario, autoridade de dominio, auditoria, comandos e eventos devem permanecer separados.

## Problema

Permitir que permissao administrativa altere diretamente estado interno de UL, saldo ou localizacao compromete as invariantes do dominio e a rastreabilidade.

## Decisao

Permissoes de usuario serao parametrizaveis, mas alteracoes de dominio ocorrerao somente por operacoes validas.

Fica proibida edicao direta do estado interno da Unidade Logistica.

Comandos e eventos sao conceitos distintos: comando solicita acao; evento registra fato ocorrido.

Eventos terao envelope comum com versionamento, idempotencia, correlacao e causacao.

Estados de agregados, eventos de dominio e alertas derivados nao devem ser misturados. Alertas derivados podem ser gerados por monitoramento temporal, projecoes ou servicos operacionais, devem possuir correlacao, devem ser idempotentes e podem gerar notificacao, tarefa ou escalonamento conforme politica.

## Alternativas

- Permitir edicao administrativa direta de estado interno: rejeitado.
- Misturar comandos e eventos em uma unica mensagem generica: rejeitado.
- Tratar estados ou alertas como eventos de dominio sem criterio: rejeitado.
- Separar autorizacao, operacao de dominio, auditoria, comando e evento: aprovado.

## Consequencias

### Consequencias positivas

- Administrador configura permissoes, mas nao contorna invariantes.
- Operacoes relevantes deixam trilha de auditoria.
- Eventos podem evoluir por versao sem mudar significado silenciosamente.

### Consequencias negativas

- Politicas de permissao mal configuradas podem liberar solicitacoes indevidas.
- Eventos sem versionamento adequado podem quebrar consumidores.

### Riscos e compromissos

- Politicas de permissao mal configuradas podem liberar solicitacoes indevidas.
- Eventos sem versionamento adequado podem quebrar consumidores.
- O catalogo de eventos precisa manter separacao explicita entre comandos, estados, eventos e alertas derivados.

## Impactos

- Autorizacao.
- Auditoria.
- Eventos de dominio.
- Integracoes futuras.
- Projecoes e rastreabilidade.

## Riscos

- Politicas de permissao mal configuradas podem liberar solicitacoes indevidas.
- Eventos sem versionamento adequado podem quebrar consumidores.

## Pendencias

- Modelo tecnico de permissoes e auditoria.
- Persistencia e publicacao de eventos.
- Definicao fisica de infraestrutura permanece fora do escopo.

## Relacao com AS-0004

Derivado das secoes 26, 27, 28, 29, 30, 31, 37, 38 e 40 da AS-0004.