# DL-0001 - Estrutura Hierarquica de Enderecamento de Estoque

## Status

Aprovado

## Data

2026-07-15

## Origem

Sessao de arquitetura registrada em `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`.

## Contexto

O modulo de estoque do MES/MOM precisa representar estruturas fisicas diferentes dentro de um mesmo armazem. A documentacao existente confirma a presenca das entidades `Almoxarifado`, `AreaEstoque`, `TipoLocalizacao` e `LocalizacaoEstoque`, alem de relacionamentos entre localizacao, area, almoxarifado, tipo e localizacao pai.

Evidencias documentais:

- `MES-ProjectBook/docs/05 - Estoque/Estoque.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Entidades.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Relacionamentos.md`

## Decisoes Aprovadas

1. `TipoLocalizacao` sera um catalogo global reutilizavel.
2. Cada `AreaEstoque` podera definir sua propria estrutura hierarquica.
3. `AreaEstoque` e `LocalizacaoEstoque` permanecem entidades separadas.
4. Localizacoes poderao armazenar em niveis diferentes, desde que sejam nos terminais configurados.
5. Localizacao armazenadora nao pode possuir filhos ativos.
6. Localizacao com estoque nao pode receber filhos.

## Justificativa

A estrutura por area permite representar cenarios industriais distintos sem impor uma hierarquia unica para todo o armazem. A separacao entre `AreaEstoque` e `LocalizacaoEstoque` preserva o limite funcional entre a area operacional e os enderecos internos utilizados para armazenagem, movimentacao e rastreabilidade.

Permitir armazenagem em niveis diferentes evita obrigar todas as areas a seguirem a mesma profundidade de arvore. A restricao de que apenas nos terminais configurados sejam armazenadores evita ambiguidade operacional entre agrupadores e enderecos fisicos de saldo.

## Impacto Tecnico

- A modelagem futura devera suportar hierarquia por `AreaEstoque`.
- A regra de no terminal armazenador devera ser validada nas operacoes de criacao, alteracao estrutural e movimentacao.
- Qualquer necessidade de novos campos, tabelas, indices, constraints, DTOs ou endpoints permanece como pendencia tecnica de modelagem futura.

## Impacto Funcional

- O operador podera trabalhar com estruturas fisicas diferentes conforme o tipo de area.
- A mesma lista global de tipos de localizacao podera ser reaproveitada em diferentes areas.
- A estrutura visual devera diferenciar nos agrupadores de nos armazenadores.

## Entidades Afetadas

- `Almoxarifado`
- `AreaEstoque`
- `TipoLocalizacao`
- `LocalizacaoEstoque`
- `SaldoEstoque`
- `MovimentoEstoque`
- `ReservaEstoque`
- `BloqueioEstoque`
- `TransferenciaEstoque`
- `TransferenciaEstoqueItem`
- `AjusteEstoque`
- `AjusteEstoqueItem`

Observacao: esta lista registra entidades ja identificadas na documentacao existente. Nao define novas entidades nem altera banco de dados.

## Telas Afetadas

- Cadastro de almoxarifado: `listalmoxarifado`
- Cadastro de area de estoque: `listareaestoque`
- Cadastro de tipo de localizacao: `listtipolocalizacao`
- Cadastro de localizacao de estoque: `listlocalizacaoestoque`
- Operacoes de estoque que selecionam origem ou destino de localizacao.

## Regras de Validacao

- Uma localizacao definida como armazenadora deve ser no terminal configurado.
- Uma localizacao armazenadora nao pode possuir filhos ativos.
- Uma localizacao com estoque nao pode receber filhos.
- A criacao de filhos deve validar se o pai pode atuar como agrupador.
- As validacoes detalhadas de saldo, pendencias, reservas e bloqueios dependem de modelagem tecnica futura.

## Consequencias Positivas

- Maior aderencia a diferentes layouts industriais.
- Reuso controlado do catalogo global de tipos de localizacao.
- Menor acoplamento entre area operacional e estrutura interna de enderecos.
- Base adequada para representacao visual em arvore.

## Consequencias Negativas

- A validacao de estrutura torna-se mais complexa.
- A profundidade variavel exige regras claras para distinguir agrupamento e armazenagem.
- Consultas operacionais podem precisar considerar arvore, caminho e estado da localizacao.

## Decisoes Futuras Relacionadas

- Definir a modelagem fisica de nos terminais, armazenadores e agrupadores.
- Definir como a estrutura hierarquica por area sera configurada na interface.
- Definir validacoes transacionais entre estoque, saldo, reservas, bloqueios, transito e tarefas.
- Definir indices, constraints e migracoes somente apos aprovacao tecnica especifica.
