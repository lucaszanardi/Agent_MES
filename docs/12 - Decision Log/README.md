# Decision Log

Esta pasta registra decisoes arquiteturais aprovadas para o projeto MES/MOM.

## Regras de Uso

- Registrar apenas decisoes aprovadas por validacao humana.
- Separar decisoes por tema sempre que uma sessao gerar multiplos assuntos.
- Nao usar Decision Log para criar entidades, campos, migrations, endpoints ou telas sem aprovacao especifica.
- Registrar impactos, consequencias e pendencias tecnicas quando a decisao depender de modelagem futura.
- Preservar rastreabilidade para Architecture Sessions relacionadas.

## Decisoes Registradas

| ID | Titulo | Status | Origem |
|---|---|---|---|
| DL-0001 | Estrutura Hierarquica de Enderecamento de Estoque | Aprovado | AS-0001 |
| DL-0002 | Identidade, Unicidade e Historico de Localizacoes | Aprovado | AS-0001 |
| DL-0003 | Capacidade, Ocupacao e Compatibilidade de Armazenagem | Aprovado | AS-0001 |
| DL-0004 | Ciclo de Vida e Alteracoes Estruturais de Localizacoes | Aprovado | AS-0001 |
| DL-0005 | Navegacao, Visualizacao e Recomendacao de Localizacoes | Aprovado | AS-0001 |
| DL-0006 | Movimentacoes de Estoque em Etapas e Material em Transito | Aprovado | AS-0002 |
| DL-0007 | Historico Imutavel, Eventos e Correcoes de Estoque | Aprovado | AS-0002 |
| DL-0008 | Operacao Assistida e Validacoes em Tempo Real | Aprovado | AS-0002 |
| DL-0009 | Jornada do Material e Jornada do Operador | Aprovado | AS-0002 |
| DL-0010 | Arquitetura de Tarefas Operacionais | Aprovado | AS-0002 |
| DL-0011 | Distribuicao de Tarefas por Identity, Role e Equipe Operacional | Aprovado | AS-0002 |
| DL-0012 | Independencia do MES em Relacao a ERPs | Aprovado | AS-0003 |
| DL-0013 | Sistema Externo e Referencias Externas | Aprovado | AS-0003 |
| DL-0014 | Hub de Sincronizacao | Aprovado | AS-0003 |
| DL-0015 | Modelo Canonico e Adaptadores | Aprovado | AS-0003 |
| DL-0016 | Politicas de Sincronizacao e Governanca | Aprovado | AS-0003 |
| DL-0017 | Politicas de Evolucao e Versionamento | Aprovado | AS-0003 |
| DL-0018 | Classificacao das Sincronizacoes | Aprovado | AS-0003 |
| DL-0019 | Arquitetura Orientada a Eventos | Aprovado | AS-0003 |
| DL-0020 | Resiliencia, Idempotencia, Auditoria e Reprocessamento | Aprovado | AS-0003 |
| DL-0021 | Limites Fiscais do Produto | Aprovado | AS-0003 |
| DL-0022 | Estoque como Dominio e Saldo como Projecao | Aprovado | AS-0004 |
| DL-0023 | Unidade Logistica como Agregado Fisico | Aprovado | AS-0004 |
| DL-0024 | Local de Estoque como Aggregate Root | Aprovado | AS-0004 |
| DL-0025 | Movimentacao de Estoque como Processo Operacional | Aprovado | AS-0004 |
| DL-0026 | Reserva de Estoque Disponibilidade e Concorrencia | Aprovado | AS-0004 |
| DL-0027 | Expectativa de Recebimento e Recebimento Operacional | Aprovado | AS-0004 |
| DL-0028 | Politica de Contagem Inventario e Ajuste de Estoque | Aprovado | AS-0004 |
| DL-0029 | Autoridade de Dominio Auditoria e Eventos | Aprovado | AS-0004 |
| DL-0030 | Primeira Vertical Funcional do Dominio de Estoque | Aprovado | AS-0005 |
| DL-0031 | Codigo Legado de Estoque como Insumo de Implementacao | Aprovado | AS-0005 |
| DL-0032 | Definition of Ready para Retomada da Codificacao | Aprovado | AS-0005 |
| DL-0033 | Estrategia de Idempotencia dos Comandos da Primeira Vertical de Estoque | Aprovado | AS-0007 |
| DL-0034 | Concorrencia Otimista e Exclusividade de Movimentacao de Unidade Logistica | Aprovado | AS-0007 |
| DL-0035 | Fronteira Transacional da Confirmacao de Movimentacao de Estoque | Aprovado | AS-0007 |
| DL-0036 | Transactional Outbox para Eventos da Primeira Vertical de Estoque | Aprovado | AS-0007 |
| DL-0037 | Estrategia de Coexistencia com o Legado de Estoque | Aprovado | AS-0007 |
| DL-0038 | Persistencia de Local de Estoque e Relacao com Legado | Aprovado | AS-0007 |
| DL-0039 | Exclusividade Ativa por Reserva Transacional de Unidade Logistica | Aprovado | AS-0007 |
| DL-0040 | Identificadores e Retencao Operacional da Persistencia de Estoque | Aprovado com ressalvas operacionais | AS-0007 |
| DL-0041 | Transicao do Legado de Localizacao para Local de Estoque MES | Aprovado | AS-0007 |

## Arquivos

- `DL-0001 - Arquitetura de Endereçamento de Estoque.md`
- `DL-0002 - Identidade Unicidade e Historico de Localizacoes.md`
- `DL-0003 - Capacidade Ocupacao e Compatibilidade de Armazenagem.md`
- `DL-0004 - Ciclo de Vida e Alteracoes Estruturais de Localizacoes.md`
- `DL-0005 - Navegacao Visualizacao e Recomendacao de Localizacoes.md`
- `DL-0006 - Movimentacoes de Estoque em Etapas e Material em Transito.md`
- `DL-0007 - Historico Imutavel Eventos e Correcoes de Estoque.md`
- `DL-0008 - Operacao Assistida e Validacoes em Tempo Real.md`
- `DL-0009 - Jornada do Material e Jornada do Operador.md`
- `DL-0010 - Arquitetura de Tarefas Operacionais.md`
- `DL-0011 - Distribuicao de Tarefas por Identity Role e Equipe Operacional.md`
- `DL-0012 - Independencia do MES em Relacao a ERPs.md`
- `DL-0013 - Sistema Externo e Referencias Externas.md`
- `DL-0014 - Hub de Sincronizacao.md`
- `DL-0015 - Modelo Canonico e Adaptadores.md`
- `DL-0016 - Politicas de Sincronizacao e Governanca.md`
- `DL-0017 - Politicas de Evolucao e Versionamento.md`
- `DL-0018 - Classificacao das Sincronizacoes.md`
- `DL-0019 - Arquitetura Orientada a Eventos.md`
- `DL-0020 - Resiliencia Idempotencia Auditoria e Reprocessamento.md`
- `DL-0021 - Limites Fiscais do Produto.md`
- `DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `DL-0023 - Unidade Logistica como Agregado Fisico.md`
- `DL-0024 - Local de Estoque como Aggregate Root.md`
- `DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `DL-0026 - Reserva de Estoque Disponibilidade e Concorrencia.md`
- `DL-0027 - Expectativa de Recebimento e Recebimento Operacional.md`
- `DL-0028 - Politica de Contagem Inventario e Ajuste de Estoque.md`
- `DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `DL-0030 - Primeira Vertical Funcional do Dominio de Estoque.md`
- `DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao.md`
- `DL-0032 - Definition of Ready para Retomada da Codificacao.md`
- `DL-0033 - Estrategia de Idempotencia dos Comandos da Primeira Vertical de Estoque.md`
- `DL-0034 - Concorrencia Otimista e Exclusividade de Movimentacao de Unidade Logistica.md`
- `DL-0035 - Fronteira Transacional da Confirmacao de Movimentacao de Estoque.md`
- `DL-0036 - Transactional Outbox para Eventos da Primeira Vertical de Estoque.md`
- `DL-0037 - Estrategia de Coexistencia com o Legado de Estoque.md`
- `DL-0038 - Persistencia de Local de Estoque e Relacao com Legado.md`
- `DL-0039 - Exclusividade Ativa por Reserva Transacional de Unidade Logistica.md`
- `DL-0040 - Identificadores e Retencao Operacional da Persistencia de Estoque.md`
- `DL-0041 - Transicao do Legado de Localizacao para Local de Estoque MES.md`

