# DL-0004 - Ciclo de Vida e Alteracoes Estruturais de Localizacoes

## Status

Aprovado

## Data

2026-07-15

## Origem

Sessao de arquitetura registrada em `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`.

## Contexto

Localizacoes de estoque possuem historico operacional, saldos e possiveis pendencias. Por isso, exclusao fisica e alteracoes estruturais livres podem comprometer rastreabilidade, auditoria e consistencia de operacoes em andamento.

## Decisoes Aprovadas

1. Localizacoes nunca serao excluidas fisicamente.
2. O ciclo de vida sera: Em Projeto, Ativa, Bloqueada, Em Desativacao e Inativa.
3. Localizacao com estoque nao pode ser inativada diretamente.
4. Em Desativacao nao permite novas entradas, mas permite saida para esvaziamento.
5. Inativacao definitiva exige saldo, reservas, bloqueios, transito e tarefas zerados.
6. Alteracao de pai exige no e descendentes sem estoque e sem pendencias.
7. Estruturas com historico devem preferencialmente ser inativadas e recriadas.
8. Ordenacao visual pode mudar sem alterar o endereco.

## Justificativa

A exclusao fisica de localizacoes com uso historico prejudica rastreabilidade. O ciclo de vida permite controlar disponibilidade operacional sem apagar registros. O estado Em Desativacao cria um caminho seguro para esvaziar enderecos antes da inativacao definitiva.

Alteracao de pai muda o caminho e o contexto operacional da localizacao; por isso, exige ausencia de estoque e pendencias no no e em seus descendentes. Quando ja houver historico relevante, a estrategia preferencial sera inativar e recriar para preservar clareza operacional.

## Impacto Tecnico

- A solucao futura devera representar estado de ciclo de vida da localizacao.
- Operacoes de entrada, saida, transferencia, reserva, bloqueio e tarefas deverao validar o estado da localizacao.
- A inativacao definitiva dependera de verificacoes integradas de saldo, reservas, bloqueios, transito e tarefas.
- A definicao tecnica de transito e tarefas permanece pendente.
- Este Decision Log nao define migration, tabela, coluna ou endpoint.

## Impacto Funcional

- Usuarios nao poderao apagar fisicamente enderecos historicos.
- Localizacoes poderao ser bloqueadas ou colocadas em desativacao sem interromper a saida para esvaziamento.
- Mudancas de estrutura serao controladas para evitar perda de rastreabilidade.
- A ordenacao visual podera ser ajustada sem alterar o endereco operacional.

## Entidades Afetadas

- `LocalizacaoEstoque`
- `SaldoEstoque`
- `MovimentoEstoque`
- `ReservaEstoque`
- `BloqueioEstoque`
- `TransferenciaEstoque`
- `TransferenciaEstoqueItem`
- `AjusteEstoque`
- `AjusteEstoqueItem`

Observacao: entidades ou estruturas para tarefas e transito nao estao definidas e devem ser tratadas como pendencia tecnica.

## Telas Afetadas

- Cadastro de localizacao de estoque.
- Visualizacao hierarquica de localizacoes.
- Operacoes de entrada, saida, transferencia, reserva, bloqueio, inventario e ajuste.
- Futuras telas de tarefas, transito e manutencao estrutural, caso aprovadas.

## Regras de Validacao

- Localizacao nao deve ser excluida fisicamente.
- Localizacao com estoque nao pode ser inativada diretamente.
- Localizacao em desativacao nao pode receber novas entradas.
- Localizacao em desativacao pode permitir saidas para esvaziamento.
- Inativacao definitiva exige saldo, reservas, bloqueios, transito e tarefas zerados.
- Alteracao de pai exige no e descendentes sem estoque e sem pendencias.
- Ordenacao visual nao deve alterar o endereco.

## Consequencias Positivas

- Preserva historico e rastreabilidade.
- Reduz risco de inconsistencia operacional.
- Permite desativacao controlada de estruturas fisicas.
- Separa organizacao visual de identidade/endereco.

## Consequencias Negativas

- Reestruturacoes ficam mais restritas.
- O processo de desativacao pode exigir etapas operacionais adicionais.
- Dependencias com tarefas e transito precisarao ser desenhadas antes da implementacao completa.

## Decisoes Futuras Relacionadas

- Definir a representacao tecnica do ciclo de vida.
- Definir o conceito de transito aplicavel a localizacoes.
- Definir o conceito de tarefas pendentes e sua integracao com estoque.
- Definir fluxo de aprovacao para mudancas estruturais com historico.
