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

Proxima discussao planejada:

AS-0004 - Recebimento de Materiais.

Nao existe arquivo formal de AS-0004 neste momento.

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

## Onda 2 - Execucao Logistica

### AS-0004 - Recebimento de Materiais

Status: Planejada para discussao.

Escopo preliminar, sem decisao arquitetural:

- expectativa de recebimento;
- recebimento previsto e nao previsto;
- conferencia documental operacional;
- conferencia fisica;
- divergencia;
- lote;
- validade;
- serial;
- embalagem;
- unidade logistica;
- staging;
- quarentena;
- inspecao de recebimento;
- liberacao;
- rejeicao;
- devolucao;
- geracao de tarefas de armazenagem;
- integracao com pedido de compra;
- referencia de nota fiscal;
- jornada do material recebido.

O escopo acima e apenas preliminar e devera ser validado durante a discussao da AS-0004.

### AS-0005 - Saldos, Disponibilidade, Reservas e Bloqueios

Status: Planejada.

Escopo preliminar:

- saldo fisico;
- saldo logico;
- saldo disponivel;
- saldo reservado;
- saldo bloqueado;
- saldo em transito;
- disponibilidade por lote;
- disponibilidade por localizacao;
- reservas;
- compromissos;
- bloqueios;
- conciliacao.

### AS-0006 - Inventario e Ajustes

Status: Planejada.

Escopo preliminar:

- inventario geral;
- inventario rotativo;
- contagem cega;
- reconferencia;
- divergencias;
- ajustes;
- aprovacao;
- trilha de auditoria;
- bloqueio durante inventario.

### AS-0007 - Abastecimento, Consumo e Retorno da Producao

Status: Planejada.

Escopo preliminar:

- solicitacao de material;
- separacao;
- abastecimento;
- entrega na linha;
- consumo;
- consumo automatico e manual;
- retorno;
- sobra;
- perda;
- material em processo;
- devolucao ao estoque.

## Onda 3 - Engenharia e Planejamento Industrial

Temas futuros sem numeracao definitiva:

- engenharia de produto;
- estruturas e revisoes;
- roteiros;
- recursos;
- centros de trabalho;
- calendarios;
- capacidade;
- MRP;
- necessidades de materiais;
- planejamento;
- sequenciamento.

## Onda 4 - Execucao da Producao

Temas futuros sem numeracao definitiva:

- ordens de producao;
- demandas;
- alocacoes;
- campanhas de producao;
- operacoes;
- execucoes;
- apontamentos;
- turnos;
- equipes;
- passagem de turno;
- perdas;
- refugos;
- retrabalho;
- lote produzido;
- sublotes;
- rateios.

## Onda 5 - Qualidade

Temas futuros sem numeracao definitiva:

- inspecao de recebimento;
- inspecao em processo;
- inspecao final;
- plano de inspecao;
- amostragem;
- nao conformidade;
- bloqueio;
- liberacao;
- desvio;
- retrabalho;
- descarte;
- rastreabilidade da qualidade.

## Onda 6 - Manutencao e Desempenho

Temas futuros sem numeracao definitiva:

- eventos de maquina;
- paradas;
- motivos de parada;
- manutencao corretiva;
- manutencao preventiva;
- manutencao preditiva;
- disponibilidade;
- performance;
- qualidade;
- OEE;
- MTBF;
- MTTR.

## Onda 7 - Analytics e Inteligencia Industrial

Temas futuros sem numeracao definitiva:

- dashboards;
- indicadores;
- series temporais;
- data lake;
- analytics;
- previsao;
- deteccao de anomalias;
- otimizacao;
- visao computacional;
- IA;
- agentes industriais.

Esses itens dependerao da maturidade e qualidade dos dados operacionais anteriores.

## Dependencias Principais

```text
Enderecamento
-> Movimentacoes
-> Recebimento
-> Saldos e disponibilidade
-> Inventario
-> Abastecimento da producao
```

```text
Integracao e sincronizacao
-> aplicavel transversalmente a todas as ondas
```

```text
Engenharia e roteiros
-> planejamento
-> ordens
-> execucao
-> apontamentos
-> OEE e analytics
```

```text
Qualidade
-> transversal a recebimento, estoque, producao e expedicao
```

## Limites

- Este roadmap nao define cronograma com datas.
- Este roadmap nao estima esforco.
- Este roadmap nao reserva numeracao definitiva para temas posteriores a AS-0007.
- Este roadmap nao cria novas decisoes arquiteturais.
- Este roadmap nao autoriza implementacao.
