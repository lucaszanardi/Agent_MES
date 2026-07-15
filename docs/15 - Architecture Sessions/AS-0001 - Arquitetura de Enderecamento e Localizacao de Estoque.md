# AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque

## 1. Identificacao da Sessao

| Campo | Valor |
|---|---|
| Codigo | AS-0001 |
| Titulo | Arquitetura de Enderecamento e Localizacao de Estoque |
| Status | Aprovada |
| Data da sessao | 2026-07-15 |
| Data da ultima revisao | 2026-07-15 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Sessao de arquitetura realizada antes da implementacao das alteracoes. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- ChatGPT, no papel de apoio arquitetural.
- Codex, no papel de documentador tecnico.

Fontes documentais preservadas:

- `AGENTS.md`
- `MES-ProjectBook/docs/00 - IA/AI_CONTEXT.md`
- `MES-ProjectBook/docs/01 - Visao Geral do Projeto/INVENTARIO_FUNCIONAL.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Entidades.md`
- `MES-ProjectBook/docs/03 - Modelo de Dados/Relacionamentos.md`
- `MES-ProjectBook/docs/05 - Estoque/Estoque.md`
- `MES-ProjectBook/docs/12 - Decision Log/README.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/README.md`

## 2. Objetivo da Sessao

A sessao teve como finalidade definir a arquitetura de enderecamento e localizacao de estoque no MES/MOM, com base exclusivamente nas decisoes aprovadas nesta sessao.

O objetivo abrange:

- tipos de localizacao;
- estruturas hierarquicas por area de estoque;
- locais de estoque;
- capacidade;
- ocupacao;
- ciclo de vida;
- identificacao;
- visualizacao;
- politicas de armazenagem;
- preparacao para futura recomendacao inteligente.

Esta Architecture Session nao altera `BACKEND`, nao altera `FRONTEND`, nao cria entidades definitivas, nao define migration e nao aprova alteracoes fisicas de banco. Tudo que depender de modelagem tecnica futura fica registrado como pendencia.

## 3. Contexto

O modulo de estoque do MES/MOM possui cadastros estruturais e operacoes relacionadas a almoxarifado, area, localizacao, lote, saldo, movimento, reserva, bloqueio, inventario, recebimento, transferencia e ajuste.

A documentacao existente registra que:

- `AreaEstoque` pertence a `Almoxarifado` e `TipoAreaEstoque`;
- `LocalizacaoEstoque` pertence a `Almoxarifado`, `AreaEstoque`, `TipoLocalizacao` e pode possuir localizacao pai;
- existem telas de cadastro para almoxarifado, area de estoque, tipo de localizacao e localizacao de estoque;
- operacoes de estoque existem, mas regras transacionais completas de saldo/movimento ainda possuem pendencias de confirmacao;
- a capacidade em localizacao existe como conceito observado, mas sua regra operacional completa nao esta consolidada.

O contexto considerado inclui multiplos armazens, multiplas areas dentro do mesmo armazem, diferentes estruturas fisicas de armazenagem, big bags, paletes, tanques, armazenamento em piso, necessidade de parametrizacao, necessidade de evitar regras rigidas e preservacao de rastreabilidade e historico.

## 4. Problemas Analisados

1. Hierarquia global insuficiente.
2. Diferentes estruturas dentro do mesmo armazem.
3. Definicao dos nos armazenadores.
4. Unicidade de codigos.
5. Caminho completo.
6. QR Code.
7. Capacidade.
8. Mistura de produtos e lotes.
9. Ciclo de vida das localizacoes.
10. Reorganizacao fisica.
11. Visualizacao do espaco disponivel.
12. Preparacao para recomendacao futura.
13. Separacao entre area operacional e localizacao armazenadora.
14. Armazenagem em niveis diferentes sem ambiguidade.
15. Inativacao e alteracoes estruturais de localizacoes.
16. Compatibilidade, mistura e estrategias de armazenagem.
17. Preparacao para recomendacao futura sem dependencia de IA na primeira versao.

## 5. Cenarios Industriais Considerados

### 5.1 Area de Big Bags

```text
Rua > Bloco
```

### 5.2 Area Paletizada

```text
Rua > Coluna > Nivel > Posicao
```

### 5.3 Area de Liquidos

```text
Tanque
```

### 5.4 Area de Piso

```text
Setor > Posicao
```

### 5.5 Reorganizacao Fisica

Repintura, remarcacao de piso, substituicao ou desativacao de enderecos.

### 5.6 Operacao por Leitura

Leitura da origem e do destino por QR Code ou codigo de barras.

## 6. Alternativas Avaliadas

### 6.1 Hierarquia Global

Descricao: definir uma estrutura fixa e comum para todos os armazens e areas.

Motivo de rejeicao: nao atende cenarios industriais com estruturas fisicas diferentes e forca niveis desnecessarios em areas simples.

Decisao resultante: cada `AreaEstoque` podera definir sua propria estrutura hierarquica.

### 6.2 Hierarquia por Armazem

Descricao: definir uma estrutura comum para todas as areas de um mesmo armazem.

Motivo de rejeicao: um mesmo armazem pode conter areas com estruturas fisicas diferentes.

Decisao resultante: a estrutura sera configuravel por area de estoque.

### 6.3 Catalogo Global com Configuracao por Armazem

Descricao: manter `TipoLocalizacao` global, mas configurar a hierarquia no nivel do armazem.

Motivo de rejeicao: a variacao ocorre dentro do armazem, por area.

Decisao resultante: `TipoLocalizacao` sera global e a estrutura sera definida por `AreaEstoque`.

### 6.4 Catalogo Global com Estrutura por Area de Estoque

Descricao: manter `TipoLocalizacao` como catalogo global reutilizavel e permitir que cada `AreaEstoque` defina sua estrutura.

Motivo de aceitacao: preserva reuso do catalogo e permite flexibilidade por area.

Decisao resultante: alternativa aprovada.

### 6.5 Arvore Completa

Descricao: representar localizacoes em arvore, com nos pais e filhos.

Motivo de aceitacao: permite representar estruturas industriais com profundidades variaveis.

Decisao resultante: serao mantidos `localizacaopaiid` e `caminhocompleto`; localizacoes armazenadoras devem ser nos terminais configurados.

### 6.6 Navegacao por Area

Descricao: navegar operacionalmente por armazem e depois por area.

Motivo de aceitacao: acompanha a organizacao fisica e operacional do estoque.

Decisao resultante: a navegacao operacional sera por Armazem e depois Area.

### 6.7 Caminho Calculado

Descricao: calcular o caminho completo a partir da hierarquia.

Motivo de aceitacao parcial: o calculo e necessario quando houver alteracoes estruturais.

Decisao resultante: alteracoes estruturais recalcularao caminhos atuais.

### 6.8 Caminho Persistido

Descricao: manter o caminho completo persistido para exibicao, busca e rastreabilidade.

Motivo de aceitacao: facilita identificacao operacional e preservacao do caminho historico em movimentacoes.

Decisao resultante: sera mantido `caminhocompleto`, e movimentacoes historicas deverao preservar o caminho utilizado na epoca.

### 6.9 Exclusao Fisica

Descricao: permitir exclusao fisica de localizacoes.

Motivo de rejeicao: prejudica rastreabilidade e historico operacional.

Decisao resultante: localizacoes nunca serao excluidas fisicamente.

### 6.10 Inativacao Controlada

Descricao: controlar disponibilidade de localizacoes por ciclo de vida.

Motivo de aceitacao: permite bloquear, desativar e esvaziar localizacoes sem apagar historico.

Decisao resultante: o ciclo de vida sera Em Projeto, Ativa, Bloqueada, Em Desativacao e Inativa.

### 6.11 Enderecamento Fixo

Descricao: associar determinados materiais a localizacoes fixas.

Motivo de aceitacao: atende operacoes que precisam de previsibilidade de armazenagem.

Decisao resultante: sera suportado enderecamento fixo.

### 6.12 Enderecamento Dinamico

Descricao: permitir que materiais sejam alocados em localizacoes tecnicamente validas conforme disponibilidade e regras.

Motivo de aceitacao: atende operacoes com uso flexivel de espaco.

Decisao resultante: sera suportado enderecamento dinamico.

### 6.13 Enderecamento Preferencial

Descricao: permitir preferencia de localizacao sem exigir exclusividade absoluta.

Motivo de aceitacao: equilibra padronizacao operacional e flexibilidade.

Decisao resultante: sera suportado enderecamento preferencial.

## 7. Decisoes Aprovadas

### 7.1 Catalogo Global de Tipos de Localizacao

`TipoLocalizacao` sera um catalogo global reutilizavel.

### 7.2 Estrutura Hierarquica Configuravel por Area de Estoque

Cada `AreaEstoque` podera definir sua propria estrutura hierarquica.

### 7.3 Separacao entre AreaEstoque e LocalizacaoEstoque

`AreaEstoque` e `LocalizacaoEstoque` permanecem entidades separadas.

### 7.4 Navegacao Operacional por Armazem e Area

A navegacao operacional sera por Armazem e depois Area.

### 7.5 Armazenagem em Nos Terminais Configurados

Localizacoes poderao armazenar em niveis diferentes, desde que sejam nos terminais configurados. Localizacao armazenadora nao pode possuir filhos ativos. Localizacao com estoque nao pode receber filhos.

### 7.6 Codigo Unico entre Irmaos

`codlocalizacao` sera unico entre irmaos.

### 7.7 Caminho Completo Unico

O caminho completo sera unico.

### 7.8 Persistencia de localizacaopaiid e caminhocompleto

Serao mantidos `localizacaopaiid` e `caminhocompleto`.

### 7.9 Preservacao do Caminho Historico das Movimentacoes

Alteracoes estruturais recalcularao caminhos atuais. Movimentacoes historicas deverao preservar o caminho utilizado na epoca.

### 7.10 Identificacao Estavel por QR Code

QR Code devera identificar a identidade estavel da localizacao.

### 7.11 Capacidade Parametrizavel

A capacidade sera parametrizavel por peso, volume, quantidade ou unidade logistica.

### 7.12 Multiplos Criterios de Capacidade

Uma localizacao podera possuir multiplas restricoes de capacidade.

### 7.13 Conversoes entre Unidades, Embalagens e Unidades Logisticas

O sistema devera considerar conversoes entre unidades, embalagens e unidades logisticas.

### 7.14 Visualizacao de Ocupacao

A estrutura sera apresentada visualmente. A ocupacao sera apresentada visualmente.

### 7.15 Escolha Assistida pelo Operador na Primeira Versao

Na primeira versao, o operador escolhera entre localizacoes tecnicamente validas.

### 7.16 Preparacao para Recomendacao Futura por IA

A arquitetura ficara preparada para recomendacao futura por IA.

### 7.17 Registro de Dados para Aprendizado Futuro

Serao registrados dados necessarios para treinamento futuro de modelos de recomendacao.

### 7.18 Ciclo de Vida da Localizacao

O ciclo de vida sera:

- Em Projeto
- Ativa
- Bloqueada
- Em Desativacao
- Inativa

Localizacoes nunca serao excluidas fisicamente.

### 7.19 Regras de Inativacao

Localizacao com estoque nao pode ser inativada diretamente. Em Desativacao nao permite novas entradas, mas permite saida para esvaziamento. Inativacao definitiva exige saldo, reservas, bloqueios, transito e tarefas zerados.

### 7.20 Regras de Alteracao Estrutural

Alteracao de pai exige no e descendentes sem estoque e sem pendencias. Estruturas com historico devem preferencialmente ser inativadas e recriadas. Ordenacao visual pode mudar sem alterar o endereco.

### 7.21 Regras Parametrizaveis de Mistura

Regras de mistura serao parametrizaveis:

- multiplos produtos;
- multiplos lotes;
- multiplas validades;
- multiplos proprietarios;
- limite de SKUs.

### 7.22 Enderecamento Fixo, Dinamico e Preferencial

Serao suportados enderecamentos fixo, dinamico e preferencial.

### 7.23 Regras de Compatibilidade

Areas e localizacoes poderao possuir regras de compatibilidade.

### 7.24 Estrategias de Armazenagem Configuraveis

Estrategias de armazenagem serao configuraveis pelo usuario.

### 7.25 Principio de Parametrizacao Industrial

A primeira versao utilizara regras parametrizadas.

O sistema deve privilegiar parametrizacao e configuracao em detrimento de regras rigidas e customizacoes especificas por cliente.

## 8. Principios Arquiteturais

Os principios abaixo derivam das decisoes aprovadas e nao criam decisoes adicionais:

- parametrizacao antes de customizacao;
- flexibilidade por segmento industrial;
- separacao entre catalogo, configuracao e instancia fisica;
- preservacao de historico;
- rastreabilidade;
- operacao assistida;
- preparacao para IA sem dependencia de IA na primeira versao;
- identidade estavel das localizacoes;
- nao exclusao fisica de registros operacionais;
- seguranca operacional acima da conveniencia de edicao.

## 9. Impactos Funcionais

Esta sessao impacta diretamente os seguintes dominios, fluxos ou interfaces, sem declarar que todos ja estao implementados:

- Cadastro de Tipo de Localizacao;
- Cadastro de Armazem;
- Cadastro de Area de Estoque;
- Cadastro de Localizacao de Estoque;
- mapa visual do estoque;
- entrada;
- saida;
- transferencia;
- inventario;
- reserva;
- bloqueio;
- recebimento;
- expedicao;
- producao;
- rastreabilidade;
- etiquetas;
- coletores de dados;
- futura recomendacao de armazenagem.

Impactos funcionais preservados do registro original:

- O usuario navegara primeiro por armazem e depois por area.
- A estrutura e a ocupacao deverao ser exibidas visualmente.
- O operador escolhera entre localizacoes tecnicamente validas na primeira versao.
- Localizacoes com estoque ou pendencias terao restricoes de mudanca estrutural e inativacao.
- A operacao tera melhor suporte para cenarios industriais diversos.
- Relatorios e consultas historicas deverao refletir o caminho usado no momento da movimentacao.

## 10. Impactos Tecnicos

Impactos tecnicos identificados ou derivados das decisoes aprovadas:

- necessidade futura de configuracao de estrutura por area;
- necessidade de suportar caminhos hierarquicos;
- necessidade de preservar historico;
- necessidade de indices de unicidade;
- necessidade de ciclo de vida mais expressivo que um booleano;
- necessidade futura de multiplos criterios de capacidade;
- necessidade futura de regras de compatibilidade;
- necessidade futura de QR Code;
- necessidade de validacoes no backend;
- necessidade de representacao visual no frontend.

Este documento nao define entidades, tabelas, campos, migrations, endpoints ou telas definitivas.

## 11. Riscos

- criacao de localizacoes incompativeis com a estrutura da area;
- duplicidade entre irmaos;
- alteracao de estrutura com estoque;
- perda de rastreabilidade;
- uso de QR Code baseado em texto mutavel;
- inativacao indevida;
- mistura incompativel de materiais;
- ultrapassagem de capacidade;
- divergencia entre capacidade fisica e unidade do produto;
- regras excessivamente rigidas;
- complexidade de parametrizacao;
- dados insuficientes para recomendacao futura;
- tratar CRUD de localizacao como regra operacional completa;
- antecipar recomendacao por IA sem dados historicos suficientes;
- misturar ordenacao visual com endereco operacional.

## 12. Regras de Validacao

- nao permitir codigo duplicado entre irmaos;
- nao permitir duplicidade de nos raiz na mesma area;
- nao permitir filhos em localizacao armazenadora com estoque;
- nao permitir armazenagem em no com filhos ativos;
- nao permitir inativacao direta com saldo ou pendencias;
- nao permitir novas entradas em localizacao Em Desativacao;
- permitir somente saida para esvaziamento em Em Desativacao;
- nao permitir alteracao de pai com estoque ou pendencias;
- nao permitir exclusao fisica;
- preservar historico;
- validar capacidade;
- validar compatibilidade;
- validar regras de mistura;
- validar estrategia configurada;
- recalcular caminhos atuais em alteracoes estruturais aprovadas;
- preservar o caminho utilizado na epoca em movimentacoes historicas;
- validar que QR Code referencie a identidade estavel da localizacao.

## 13. Decisoes Deliberadamente Adiadas

As decisoes abaixo permanecem como pendencias futuras e nao estao decididas nesta sessao:

- modelo definitivo da entidade de configuracao da hierarquia por area;
- modelo definitivo das capacidades multiplas;
- modelo definitivo das regras de compatibilidade;
- formato tecnico do identificador de leitura;
- geracao e armazenamento do QR Code;
- calculo ou persistencia detalhada da ocupacao;
- algoritmo de sugestao automatica;
- modelo de IA;
- regras completas de FIFO e FEFO;
- tratamento definitivo das movimentacoes em transito;
- estrategia de versionamento das estruturas;
- forma de preservar snapshots historicos;
- desenho final das telas;
- migrations e alteracoes de banco.

Pendencias tecnicas preservadas do registro original:

- definir modelagem tecnica de nos terminais, armazenadores e agrupadores;
- definir formato oficial de `caminhocompleto`;
- definir regras de normalizacao de `codlocalizacao`;
- definir persistencia do caminho historico em movimentacoes;
- definir payload e resolucao de QR Code;
- definir entidades ou estruturas para capacidade, restricoes, compatibilidade, mistura e estrategias;
- definir conversoes entre unidades, embalagens e unidades logisticas;
- definir ciclo de vida em nivel tecnico, incluindo estados e transicoes;
- definir conceitos de transito e tarefas pendentes;
- definir eventos ou registros para treinamento futuro de recomendacao;
- definir UX da visualizacao estrutural e de ocupacao;
- definir prioridades entre regras de area, localizacao, produto e estrategia.

## 14. Decision Logs Derivados

- DL-0001 - Estrutura Hierarquica de Enderecamento de Estoque
- DL-0002 - Identidade, Unicidade e Historico de Localizacoes
- DL-0003 - Capacidade, Ocupacao e Compatibilidade de Armazenagem
- DL-0004 - Ciclo de Vida e Alteracoes Estruturais de Localizacoes
- DL-0005 - Navegacao, Visualizacao e Recomendacao de Localizacoes

Os arquivos de Decision Log nao foram alterados nesta tarefa.

## 15. Criterios de Encerramento

A sessao foi considerada concluida porque foram aprovadas decisoes para:

- estrutura;
- identidade;
- unicidade;
- armazenagem;
- capacidade;
- ocupacao;
- compatibilidade;
- ciclo de vida;
- visualizacao;
- parametrizacao;
- preparacao para IA.

## 16. Proximos Passos

1. Revisar a AS-0001.
2. Revisar os Decision Logs.
3. Aprovar o commit documental.
4. Iniciar futuramente a AS-0002 - Movimentacoes de Estoque.
5. Nao implementar alteracoes estruturais antes da aprovacao especifica do escopo tecnico.
