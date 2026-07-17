# DL-0016 - Politicas de Sincronizacao e Governanca

## Codigo

DL-0016

## Status

Aprovado

## Contexto

A AS-0003 definiu que diferentes tipos de informacao podem ter origens oficiais, edicoes permitidas, destinos e periodicidades diferentes conforme cliente, empresa, estabelecimento e modo de operacao.

## Problema

Uma regra unica de sincronizacao para todos os dados nao atende cenarios standalone, integrados e hibridos, alem de aumentar risco de conflito e sobrescrita indevida.

## Decisao

Cada tipo de informacao tera politica configuravel de propriedade, edicao, direcao, periodicidade, estrategia e conflito.

Modos conceituais aprovados:

- importacao;
- exportacao;
- bidirecional controlado;
- evento;
- agendamento;
- manual;
- sem sincronizacao.

A matriz de propriedade da AS-0003 e arquitetura inicial parametrizavel, nao regra fixa para todos os clientes.

## Alternativas

- Sincronizacao bidirecional irrestrita: rejeitada.
- Regra unica de propriedade para todos os clientes: rejeitada.
- Politicas configuraveis por tipo de informacao e contexto: aprovada.

## Consequencias

- O sistema oficial podera variar conforme implantacao.
- A edicao no MES podera ser controlada conforme origem e politica.
- Conflitos poderao ser tratados sem sobrescrita silenciosa.

## Impactos

- Cadastros mestres.
- Documentos operacionais.
- Eventos operacionais.
- Governanca por organizacao, empresa, estabelecimento e planta.
- Central futura de Sincronizacao.

## Riscos

- Parametrizacao incorreta pode permitir edicao indevida.
- Politicas conflitantes entre sistemas podem gerar pendencias operacionais.

## Pendencias

- Modelo fisico de politicas.
- Matriz configuravel por cliente e contexto.
- Regras de permissao e edicao por politica.
- Telas e APIs futuras.

## Relacao com AS-0003

Derivado das secoes 6.6, 6.12, 6.15 e 11 da AS-0003.
