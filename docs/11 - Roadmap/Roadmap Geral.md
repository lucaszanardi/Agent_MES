# Roadmap Arquitetural do MES

## Objetivo

Este roadmap representa a sequencia arquitetural planejada para evolucao do produto MES/MOM.

O roadmap:

- organiza discussoes arquiteturais em ondas;
- nao representa necessariamente datas;
- pode evoluir conforme novas discussoes;
- nao autoriza implementacao sem Architecture Session e Decision Logs correspondentes;
- nao cria decisoes tecnicas por si so.

## Estado Atual

| Architecture Session | Titulo | Status | Decision Logs |
|---|---|---|---|
| AS-0001 | Arquitetura de Enderecamento e Localizacao de Estoque | Concluida | DL-0001 a DL-0005 |
| AS-0002 | Movimentacoes de Estoque e Operacao Assistida | Concluida | DL-0006 a DL-0011 |
| AS-0003 | Arquitetura de Integracao, Sincronizacao, Governanca e Eventos | Concluida | DL-0012 a DL-0021 |
| AS-0004 | Arquitetura do Dominio de Estoque | Concluida | DL-0022 a DL-0029 |
| AS-0005 | Arquitetura da Primeira Vertical Funcional do Dominio de Estoque | Concluida | DL-0030 a DL-0032 |
| AS-0006 | Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque | Concluida | Nao gera DL |
| AS-0007 | Arquitetura Tecnica da Primeira Vertical Funcional de Estoque | Concluida | DL-0033 a DL-0037 |

Proxima etapa planejada:

Implementar o primeiro incremento de codigo recomendado pela AS-0007: nucleo de dominio de UnidadeLogistica e MovimentacaoDeEstoque com testes unitarios, sem API publica e sem migrations.

## Sequencia Aprovada

1. Finalizar AS-0004 - Estoque.
2. Implementar uma primeira vertical funcional do estoque.
3. Corrigir e evoluir autenticacao e experiencia de login.
4. Iniciar Domain Discovery de Gestao da Producao.
5. Modelar ordens, operacoes, recursos e sequenciamento.
6. Discutir necessidades de materiais e MRP.
7. Modelar Apontamento de Producao.
8. Integrar consumo, producao, perdas e estoque.

A implementacao somente podera iniciar apos:

- revisao da AS-0004;
- aprovacao dos Decision Logs;
- validacao do Project Book;
- definicao da primeira vertical funcional;
- arquitetura tecnica de backend;
- arquitetura tecnica de frontend.

## Onda 1 - Arquitetura Base

### AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque

Status: Concluida.

Principais resultados:

- tipos de localizacao;
- areas de estoque;
- arvores independentes;
- caminhos;
- capacidades;
- compatibilidades;
- estrategias;
- ciclo de vida;
- preservacao historica.

Referencia:

- `../15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`

### AS-0002 - Movimentacoes de Estoque e Operacao Assistida

Status: Concluida.

Principais resultados:

- entradas;
- saidas;
- transferencias;
- material em transito;
- historico imutavel;
- estornos;
- Jornada do Material;
- Jornada do Operador;
- tarefas;
- equipes operacionais;
- operacao guiada.

Referencia:

- `../15 - Architecture Sessions/AS-0002 - Movimentacoes de Estoque e Operacao Assistida.md`

### AS-0003 - Arquitetura de Integracao, Sincronizacao, Governanca e Eventos

Status: Concluida.

Principais resultados:

- independencia de ERP;
- Sistemas Externos;
- Referencias Externas;
- modelo canonico;
- adaptadores;
- Hub de Sincronizacao;
- politicas de sincronizacao;
- politicas de evolucao;
- eventos;
- resiliencia;
- idempotencia;
- auditoria;
- reprocessamento;
- limites fiscais.

Referencia:

- `../15 - Architecture Sessions/AS-0003 - Arquitetura de Integracao Sincronizacao Governanca e Eventos.md`

### AS-0004 - Arquitetura do Dominio de Estoque

Status: Concluida.

Principais resultados:

- Estoque como dominio, nao Aggregate Root;
- Aggregate Roots iniciais do dominio de Estoque;
- Unidade Logistica como principal agregado fisico;
- homogeneidade e hierarquia de UL governadas por politicas configuraveis;
- Local de Estoque como Aggregate Root;
- Movimentacao de Estoque como processo operacional com inicio, transito e fim;
- Reserva de Estoque como Aggregate Root proprio;
- Expectativa de Recebimento independente do Recebimento;
- Recebimento como processo operacional;
- Politica de Contagem e Inventario como agregados distintos;
- separacao entre contagem, reconciliacao e ajuste;
- ajustes formais, autorizados e auditaveis;
- permissao de usuario separada da autoridade de dominio;
- eventos com envelope comum e versionamento;
- saldo como projecao derivada.

Referencia:

- `../15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`

## Historico de evolucao do roadmap

A versao anterior do roadmap previa a AS-0004 com foco em Recebimento de Materiais e previa sessoes posteriores separadas para saldos, inventario e abastecimento.

Durante o Domain Discovery, foi identificada forte dependencia e coesao entre recebimento, saldos, unidades logisticas, locais, movimentacoes, reservas, inventario, ajustes e rastreabilidade. Por isso, esses conceitos foram consolidados na AS-0004 - Arquitetura do Dominio de Estoque.

Essa consolidacao substituiu o planejamento anterior. O roadmap vigente representa a sequencia aprovada apos essa revisao arquitetural e preserva a rastreabilidade da evolucao do desenho do dominio, sem reintroduzir os itens obsoletos como atividades futuras ativas.

## Onda 2 - Primeira Vertical Funcional de Estoque

Status: Definida pela AS-0005, avaliada pela AS-0006 como `NOT READY`, detalhada pela AS-0007 como `READY WITH CONDITIONS` e consolidada pelos DL-0033 a DL-0037 como `READY` para iniciar o primeiro incremento de dominio.

Escopo definido pela AS-0005 como Local de Estoque -> Unidade Logistica -> Movimentacao -> Confirmacao -> Consulta -> Historico.

Bloqueadores tecnicos registrados pela AS-0006:

- ausencia de `UnidadeLogistica` no codigo atual;
- ausencia de `MovimentacaoDeEstoque` como Aggregate Root tecnico;
- persistencia, idempotencia, eventos, autorizacao e testes ainda nao prontos;
- necessidade de classificar o codigo legado conforme DL-0031 antes de iniciar implementacao.

Arquitetura tecnica definida pela AS-0007:

- primeira slice: movimentar uma unica Unidade Logistica de um Local de Estoque para outro;
- comandos: criar, confirmar e cancelar movimentacao;
- confirmacao com idempotencia, concorrencia otimista, historico, outbox e projecoes;
- coexistencia incremental com `LocalizacaoEstoque`, `MovimentoEstoque`, `TransferenciaEstoque` e `SaldoEstoque`;
- DL-0033 a DL-0037 consolidam idempotencia, concorrencia, fronteira transacional, outbox e coexistencia com legado;
- primeiro incremento recomendado: nucleo de dominio de UnidadeLogistica e MovimentacaoDeEstoque com testes unitarios, sem API publica e sem migrations.

Temas candidatos remanescentes, sem decisao de implementacao nesta documentacao:

- recebimento operacional;
- reservas;
- inventario e ajuste formal;
- abastecimento da producao;
- projecoes de saldo alem do recorte minimo;
- auditoria e rastreabilidade alem do recorte minimo.

## Onda 3 - Autenticacao e Experiencia de Login

Status: Planejada.

Temas futuros:

- revisar autenticacao existente;
- melhorar experiencia de login;
- preservar autorizacao por perfis e permissoes;
- alinhar permissoes operacionais com autoridade de dominio.

## Onda 4 - Gestao da Producao

Status: Planejada para Domain Discovery.

Temas futuros:

- ordens de producao;
- operacoes;
- recursos;
- centros de trabalho;
- sequenciamento;
- demandas;
- alocacoes;
- campanhas de producao;
- turnos;
- equipes;
- passagem de turno.

## Onda 5 - Necessidades de Materiais e MRP

Status: Planejada.

Temas futuros:

- necessidades de materiais;
- planejamento;
- MRP;
- reservas para producao;
- disponibilidade real;
- consumo previsto;
- integracao com estoque e producao.

## Onda 6 - Apontamento de Producao

Status: Planejada.

Temas futuros:

- execucao de operacao;
- apontamentos;
- consumo;
- quantidade boa;
- perdas;
- refugos;
- paradas;
- retrabalho;
- lote produzido;
- sublotes;
- integracao com estoque.

## Onda 7 - Qualidade, Desempenho e Inteligencia Industrial

Status: Planejada sem numeracao definitiva.

Temas futuros:

- inspecao de recebimento;
- inspecao em processo;
- inspecao final;
- nao conformidade;
- bloqueio;
- liberacao;
- OEE;
- indicadores;
- dashboards;
- analytics;
- IA;
- agentes industriais.

Esses itens dependerao da maturidade e qualidade dos dados operacionais anteriores.

## Dependencias Principais

```text
Enderecamento e Local de Estoque
-> Unidade Logistica
-> Movimentacao
-> Saldo projetado
-> Disponibilidade
-> Reserva
-> Abastecimento da producao
```

```text
Recebimento
-> Unidade Logistica
-> Custodia
-> Disponibilidade
-> Consumo
```

```text
Inventario
-> Contagem
-> Reconciliacao
-> Ajuste formal
-> Projecoes de saldo
```

```text
Integracao e sincronizacao
-> aplicavel transversalmente a todas as ondas
```

```text
Gestao da Producao
-> ordens
-> operacoes
-> recursos
-> sequenciamento
-> apontamentos
-> consumo, producao, perdas e estoque
```

## Limites

- Este roadmap nao define cronograma com datas.
- Este roadmap nao estima esforco.
- Este roadmap nao cria novas decisoes arquiteturais.
- Este roadmap nao autoriza implementacao.
- Este roadmap nao substitui Architecture Sessions ou Decision Logs.


