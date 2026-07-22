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

Proxima etapa planejada:

Revisao humana da AS-0004 e dos Decision Logs derivados antes de iniciar qualquer implementacao.

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

Status: Planejada apos revisao humana da AS-0004 e dos DLs derivados.

Escopo devera ser definido em etapa propria, antes da implementacao.

Temas candidatos, sem decisao de implementacao nesta documentacao:

- Unidade Logistica;
- Local de Estoque;
- recebimento operacional;
- movimentacao com inicio, transito e fim;
- projecoes de saldo;
- reservas;
- inventario e ajuste formal;
- auditoria e rastreabilidade.

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
