# DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao

## Codigo

DL-0031

## Status

Aprovado

## Contexto

A documentacao funcional registra que o codigo atual possui entidades, controllers, services e telas relacionados a estoque, incluindo LocalizacaoEstoque, SaldoEstoque, MovimentoEstoque, TransferenciaEstoque, ReservaEstoque, BloqueioEstoque, InventarioEstoque, RecebimentoEstoque e AjusteEstoque.

A mesma documentacao registra lacunas e duvidas sobre regras transacionais, atualizacao automatica de saldo, geracao de movimentos, endpoints e telas incompletas.

## Problema

Se o codigo legado for tratado como autoridade de dominio, a implementacao futura pode consolidar comportamentos parciais ou divergentes das decisoes aprovadas na AS-0004 e AS-0005.

## Decisao

O codigo legado de estoque sera tratado como insumo de implementacao e evidencia de estado atual, mas nao como autoridade arquitetural do dominio.

Antes da codificacao da vertical, o legado devera ser classificado como reutilizar, adaptar, substituir, descontinuar ou investigar, conforme aderencia a AS-0004 e AS-0005.

## Alternativas

- Adaptar cegamente o legado: rejeitado.
- Descartar todo o legado sem avaliacao: rejeitado.
- Reimplementar sem mapear impacto: rejeitado.
- Usar o legado como insumo avaliado contra a arquitetura aprovada: aprovado.

## Consequencias

### Consequencias positivas

- Preserva valor de implementacao existente.
- Evita transformar CRUD parcial em processo operacional aprovado.
- Reduz risco de acoplamento indevido ao modelo antigo.

### Consequencias negativas

- Exige diagnostico tecnico antes de codificar.
- Pode revelar necessidade de adaptar ou substituir partes existentes.

### Riscos e compromissos

- Subestimar divergencias entre legado e arquitetura pode gerar retrabalho.
- Reutilizacao sem teste de aderencia pode violar invariantes.

## Referencias

- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `../01 - Visão Geral do Projeto/INVENTARIO_FUNCIONAL.md`
- `../05 - Estoque/Estoque.md`
- `DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `DL-0025 - Movimentacao de Estoque como Processo Operacional.md`

## Relacao com AS-0005

Derivado das secoes 2, 5, 28, 29, 31, 32 e 33 da AS-0005.
