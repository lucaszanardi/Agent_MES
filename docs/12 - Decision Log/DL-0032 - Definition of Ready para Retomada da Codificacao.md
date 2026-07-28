# DL-0032 - Definition of Ready para Retomada da Codificacao

## Codigo

DL-0032

## Status

Aprovado

## Contexto

O Roadmap Geral estabelece que a implementacao so deve iniciar apos revisao da AS-0004, aprovacao dos Decision Logs, validacao do Project Book, definicao da primeira vertical funcional e arquitetura tecnica de backend e frontend.

A AS-0005 define a primeira vertical funcional, mas ainda registra pendencias tecnicas que devem ser resolvidas antes da codificacao.

## Problema

Retomar codigo sem criterios objetivos pode gerar migrations, endpoints, telas ou regras que nao respeitem o dominio aprovado, especialmente em movimentacao, saldo projetado, historico e idempotencia.

## Decisao

A retomada da codificacao da primeira vertical funcional de Estoque dependera da Definition of Ready registrada na AS-0005.

A implementacao nao devera iniciar enquanto houver pendencias bloqueadoras abertas sobre escopo, invariantes, comandos, eventos, autorizacao, persistencia, impacto no legado, migracao e testes.

## Alternativas

- Iniciar codigo apenas com base na AS-0004: rejeitado.
- Iniciar por telas ou migrations: rejeitado.
- Iniciar por endpoints legados existentes: rejeitado.
- Exigir Definition of Ready da AS-0005: aprovado.

## Consequencias

### Consequencias positivas

- Reduz retrabalho.
- Protege invariantes de dominio.
- Deixa claro o que falta antes da implementacao.

### Consequencias negativas

- Posterga a codificacao ate resolver bloqueadores.
- Exige revisao tecnica de backend e frontend antes da primeira entrega.

### Riscos e compromissos

- Tratar a Definition of Ready como formalidade sem validacao real enfraquece a decisao.
- Bloqueadores nao classificados podem voltar como retrabalho durante a implementacao.

## Referencias

- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `../11 - Roadmap/Roadmap Geral.md`
- `DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`

## Relacao com AS-0005

Derivado das secoes 29, 30, 31, 32, 33, 34 e 39 da AS-0005.
