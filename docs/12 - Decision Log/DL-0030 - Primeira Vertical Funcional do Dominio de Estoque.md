# DL-0030 - Primeira Vertical Funcional do Dominio de Estoque

## Codigo

DL-0030

## Status

Aprovado

## Contexto

A AS-0004 consolidou o Dominio de Estoque, mas o Roadmap Geral condiciona a retomada de implementacao a definicao da primeira vertical funcional.

O inventario funcional registra que existem entidades, controllers e telas de estoque, mas tambem registra lacunas transacionais, especialmente em saldo, movimentacao, transferencia, reserva, bloqueio, ajuste e inventario.

## Problema

Iniciar implementacao por todo o Dominio de Estoque ou por telas isoladas aumenta o risco de transformar CRUDs parciais em processo operacional e de violar invariantes aprovadas na AS-0004.

## Decisao

A primeira vertical funcional do Dominio de Estoque sera limitada ao fluxo:

```text
Local de Estoque
-> Unidade Logistica
-> Movimentacao
-> Confirmacao
-> Consulta
-> Historico
```

A vertical deve validar LocalDeEstoque, UnidadeLogistica e MovimentacaoDeEstoque como recorte minimo demonstravel antes de expandir para recebimento, reserva, inventario, ajuste, MRP ou producao.

## Alternativas

- Iniciar por Recebimento completo: rejeitado para esta primeira vertical.
- Iniciar por Reserva ou Inventario: rejeitado para esta primeira vertical.
- Iniciar por autenticacao/login: rejeitado como proxima etapa imediata, pois o roadmap coloca autenticacao apos a primeira vertical de estoque.
- Iniciar por Producao/MRP/Apontamento: rejeitado por depender de disponibilidade e movimentacao de estoque.
- Definir recorte Local -> UL -> Movimentacao -> Confirmacao -> Consulta -> Historico: aprovado.

## Consequencias

### Consequencias positivas

- Valida o nucleo operacional do Dominio de Estoque.
- Reduz risco antes de fluxos maiores.
- Cria base para reserva, recebimento, inventario, abastecimento e producao.

### Consequencias negativas

- Nao entrega reserva, recebimento, inventario ou ajuste completos.
- Exige disciplina para nao expandir o escopo durante a implementacao.

### Riscos e compromissos

- A vertical pode crescer indevidamente se incluir fluxos fora do recorte.
- O legado pode sugerir atalhos que nao respeitam a arquitetura aprovada.

## Referencias

- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `../15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `DL-0023 - Unidade Logistica como Agregado Fisico.md`
- `DL-0024 - Local de Estoque como Aggregate Root.md`
- `DL-0025 - Movimentacao de Estoque como Processo Operacional.md`

## Relacao com AS-0005

Derivado das secoes 2, 3, 29, 32, 33, 34 e 35 da AS-0005.
