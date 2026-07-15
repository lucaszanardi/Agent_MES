# DL-0002 - Identidade, Unicidade e Historico de Localizacoes

## Status

Aprovado

## Data

2026-07-15

## Origem

Sessao de arquitetura registrada em `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`.

## Contexto

Localizacoes de estoque precisam ser identificadas de forma inequivoca em operacoes, consultas, movimentacoes historicas e leitura por QR Code. A estrutura hierarquica pode mudar ao longo do tempo, mas os historicos operacionais devem preservar a informacao usada na epoca.

## Decisoes Aprovadas

1. `codlocalizacao` sera unico entre irmaos.
2. Nos raiz terao codigo unico dentro da area.
3. O caminho completo sera unico.
4. Serao mantidos `localizacaopaiid` e `caminhocompleto`.
5. Alteracoes estruturais recalcularao caminhos atuais.
6. Movimentacoes historicas deverao preservar o caminho utilizado na epoca.
7. QR Code devera identificar a identidade estavel da localizacao.

## Justificativa

A unicidade por irmaos permite codigos curtos e legiveis dentro de cada nivel, enquanto o caminho completo garante identificacao operacional inequivoca. Manter `localizacaopaiid` preserva a navegacao hierarquica, e manter `caminhocompleto` facilita leitura, exibicao, busca e auditoria.

O QR Code deve apontar para a identidade estavel da localizacao, nao apenas para o texto do caminho, porque o caminho pode mudar em alteracoes estruturais aprovadas. Historicos de movimentacao devem preservar o caminho usado na epoca para rastreabilidade e auditoria.

## Impacto Tecnico

- A solucao futura devera distinguir identidade estavel da localizacao e caminho exibivel.
- Alteracoes estruturais deverao recalcular caminhos atuais sem reescrever historicos operacionais.
- A persistencia do caminho historico em movimentacoes depende de desenho tecnico futuro.
- Indices, constraints, colunas e eventos de recalculo nao estao definidos neste Decision Log.

## Impacto Funcional

- O usuario podera reconhecer localizacoes por caminho completo.
- QR Codes continuarao validos mesmo que a apresentacao hierarquica mude, desde que a identidade da localizacao seja preservada.
- Relatorios historicos deverao mostrar o endereco utilizado na epoca da movimentacao.

## Entidades Afetadas

- `AreaEstoque`
- `LocalizacaoEstoque`
- `MovimentoEstoque`
- `SaldoEstoque`
- `ReservaEstoque`
- `BloqueioEstoque`
- `TransferenciaEstoqueItem`
- `AjusteEstoqueItem`

Observacao: a lista usa entidades ja identificadas na documentacao existente e nao cria entidade definitiva para historico.

## Telas Afetadas

- Cadastro de localizacao de estoque.
- Visualizacao em arvore por area.
- Operacoes de entrada, saida, transferencia, reserva, bloqueio, inventario e ajuste.
- Consultas e relatorios historicos de movimentacao.

## Regras de Validacao

- `codlocalizacao` deve ser unico entre localizacoes com o mesmo pai.
- Localizacoes raiz devem ter codigo unico dentro da mesma `AreaEstoque`.
- `caminhocompleto` deve ser unico.
- Recalculo de caminhos atuais deve ocorrer em alteracoes estruturais aprovadas.
- Historicos de movimentacao nao devem perder o caminho utilizado na epoca.
- QR Code deve referenciar a identidade estavel da localizacao.

## Consequencias Positivas

- Enderecos legiveis e unicos.
- Melhor rastreabilidade historica.
- Menor risco de QR Code quebrar por mudanca de nome ou caminho.
- Separacao clara entre identidade e apresentacao do endereco.

## Consequencias Negativas

- Recalculo de caminhos exige cuidado transacional.
- Historico de caminho adiciona complexidade ao modelo de movimentacao.
- Mudancas estruturais exigem validacoes fortes para evitar inconsistencia.

## Decisoes Futuras Relacionadas

- Definir o formato oficial do `caminhocompleto`.
- Definir separadores, normalizacao de codigo e tratamento de caracteres.
- Definir onde o caminho historico sera armazenado em movimentacoes.
- Definir estrategia de QR Code, payload, rota de resolucao e permissao de leitura.
