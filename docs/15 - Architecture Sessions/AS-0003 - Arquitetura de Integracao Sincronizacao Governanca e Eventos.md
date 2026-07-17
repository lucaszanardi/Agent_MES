# AS-0003 - Arquitetura de Integracao Sincronizacao Governanca e Eventos

## 1. Identificacao da Sessao

| Campo | Valor |
|---|---|
| Codigo | AS-0003 |
| Titulo | Arquitetura de Integracao, Sincronizacao, Governanca e Eventos |
| Status | Aprovada |
| Data da sessao | 2026-07-17 |
| Data da ultima revisao | 2026-07-17 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Sessao de arquitetura realizada antes da implementacao das alteracoes. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- ChatGPT, no papel de apoio arquitetural.
- Codex, no papel de documentador tecnico.

## 2. Objetivo

A sessao teve como objetivo definir a arquitetura logica de integracao, sincronizacao, governanca e eventos do MES/MOM, preservando a independencia do dominio industrial em relacao a ERPs e outros sistemas externos.

Esta Architecture Session nao implementa adaptadores, nao escolhe tecnologia fisica de mensageria, nao cria entidades, nao cria tabelas, nao cria migrations, nao cria endpoints e nao altera `BACKEND` ou `FRONTEND`.

## 3. Contexto

O sistema esta sendo desenvolvido como um MES/MOM industrial comercializavel para pequenas, medias e futuramente grandes industrias.

O produto deve funcionar em diferentes cenarios:

- industria sem ERP;
- industria com ERP;
- industria com integracao parcial;
- industria cujo ERP ja possui produtos, clientes, fornecedores, colaboradores, estruturas de produto, roteiros, linhas, recursos, pedidos ou ordens de producao;
- industria que deseja utilizar o MES apenas para execucao, apontamentos, rastreabilidade, qualidade, consumo, paradas e acompanhamento da producao.

O nucleo do MES nao podera depender de um ERP especifico. A arquitetura deve permitir integracao futura com ERPs, WMS, APS, CRM, RH, SCADA, PIMS, LIMS, sistemas proprios, aplicacoes moveis, marketplaces, APIs externas e arquivos estruturados, sem declarar que esses adaptadores estejam implementados.

## 4. Principios Arquiteturais Preservados

- operacao guiada;
- historico imutavel;
- parametrizacao antes de customizacao;
- Jornada do Material;
- Jornada do Operador;
- arquitetura baseada em tarefas;
- evolucao gradual;
- dificil de errar;
- facil de operar;
- completo para auditar;
- independencia do dominio industrial;
- consistencia operacional acima da conveniencia de sincronizacao.

## 5. Modos de Operacao do Produto

### 5.1 Modo Standalone

O MES e responsavel pelos cadastros e operacoes necessarias para funcionamento sem ERP.

### 5.2 Modo Integrado

Sistemas externos sao proprietarios de determinados dados, e o MES recebe replicas operacionais para execucao industrial.

### 5.3 Modo Hibrido

Parte dos dados nasce no ERP ou em outro sistema externo, e parte nasce no MES.

Exemplos:

- produtos vindos do ERP;
- pedidos vindos do ERP e tambem cadastrados no MES;
- ordens de producao geradas no MES;
- apontamentos gerados no MES;
- resultados enviados ao ERP.

## 6. Decisoes Aprovadas

### 6.1 MES Independente de ERP

O nucleo e o dominio do MES nao conhecerao fornecedores, campos, estruturas, servicos ou regras especificas de ERP.

Exemplos proibidos no dominio:

- `CodigoProtheus`;
- `CodigoSAP`;
- `FilialProtheus`;
- `CentroSAP`;
- `ISapService`;
- `IProtheusService`;
- regras especificas de Sankhya, Senior ou outro ERP.

O dominio deve conhecer conceitos industriais e empresariais proprios, como Produto, Cliente, Fornecedor, Pedido, Demanda, Ordem de Producao, Lote, Estoque, Roteiro, Estrutura de Produto, Apontamento, Evento e Tarefa.

### 6.2 Sistema Externo

Sistema Externo representa qualquer sistema integrado, e nao apenas ERP.

Tipos conceituais possiveis:

- ERP;
- WMS;
- APS;
- CRM;
- RH;
- SCADA;
- PIMS;
- LIMS;
- Marketplace;
- API;
- Arquivo;
- Aplicacao movel;
- Sistema proprio.

O conceito devera permitir identificar futuramente nome, tipo, organizacao, empresa, estabelecimento ou filial, status, modo de comunicacao, configuracao protegida e capacidades de integracao.

Nao fica criada entidade fisica nesta sessao.

### 6.3 Referencia Externa

As entidades do MES continuarao utilizando seus identificadores internos. Identificadores externos serao associados por referencias desacopladas.

Composicao conceitual:

- Sistema Externo;
- tipo da entidade;
- identificador externo;
- identificador interno;
- empresa ou estabelecimento;
- status;
- data da ultima sincronizacao.

Exemplo conceitual:

```text
Produto interno 157
-> Sistema Externo: Protheus
-> Empresa: 01
-> Filial: 03
-> Codigo externo: PA00045
```

Nao deve ser adicionado um unico campo `CodigoErp` diretamente em `Produto`. Um produto podera possuir referencias para multiplos sistemas externos.

### 6.4 Modelo Canonico e Adaptadores

Dados externos deverao ser transformados para contratos canonicos estaveis antes de alcancar os servicos de aplicacao do MES.

Fluxo conceitual de entrada:

```text
Sistema Externo
-> Adaptador
-> Contrato Canonico
-> Hub de Sincronizacao
-> Servico de Aplicacao
-> Dominio MES
```

Fluxo conceitual de saida:

```text
Dominio MES
-> Evento ou mensagem de saida
-> Hub de Sincronizacao
-> Adaptador
-> Sistema Externo
```

Cada sistema externo podera possuir adaptador especifico. O adaptador conecta, autentica, interpreta payloads, mapeia campos, transforma nomenclaturas e converte dados entre formato externo e modelo canonico. O adaptador nao implementa regras de negocio do MES.

Nao ficam definidas classes fisicas nem adaptadores reais nesta sessao.

### 6.5 Hub de Sincronizacao

O Hub de Sincronizacao sera um modulo inicialmente interno da solucao. Nao fica definido como microservico obrigatorio nesta sessao.

Responsabilidades tecnicas:

- receber mensagens;
- validar formato e contrato;
- transformar mensagens;
- verificar idempotencia;
- registrar auditoria;
- manter correlacao;
- controlar tentativas;
- colocar mensagens em fila logica;
- permitir reprocessamento;
- publicar comandos e eventos;
- preparar mensagens de saida;
- registrar estado da sincronizacao;
- apoiar observabilidade.

O Hub nao deve atualizar diretamente tabelas de dominio, decidir regras de negocio, decidir validade industrial de alteracoes ou sobrescrever entidades sem passar pelos servicos de aplicacao e dominio.

### 6.6 Politicas de Sincronizacao e Governanca

Cada tipo de informacao podera possuir politica de sincronizacao configuravel, definindo sistema de origem, sistema oficial, destinos, permissao de edicao, direcao, periodicidade, estrategia, comportamento em conflito e comportamento quando sistema externo estiver indisponivel.

Modos conceituais:

- somente importacao;
- somente exportacao;
- bidirecional controlado;
- orientado a evento;
- agendado;
- manual;
- sem sincronizacao.

Sincronizacao bidirecional irrestrita deve ser evitada.

A matriz abaixo representa arquitetura inicial parametrizavel, nao regra fixa para todos os clientes.

| Entidade | Fonte possivel | Sistema oficial | Edicao no MES | Integracao |
|---|---|---|---|---|
| Produto | ERP ou MES | Configuravel | Conforme origem e politica | Entrada ou bidirecional controlado |
| Cliente | ERP ou MES | Configuravel | Conforme origem | Entrada |
| Fornecedor | ERP ou MES | Configuravel | Conforme origem | Entrada |
| Pedido de venda | ERP ou MES | Sistema de origem ou configuravel | Controlada | Entrada e eventual saida |
| Ordem de producao | ERP ou MES | Configuravel | Conforme cenario | Bidirecional controlado |
| Estrutura de produto | ERP ou MES | Configuravel | Conforme origem | Entrada |
| Roteiro | ERP ou MES | Configuravel | Conforme origem | Entrada |
| Estoque fisico operacional | MES | MES | Operacional | Saida ou conciliacao |
| Localizacoes de estoque | MES | MES | MES | Eventual exportacao |
| Movimentacoes | MES | MES | Imutavel, correcao por estorno | Saida |
| Lotes operacionais | MES | MES | Controlada | Saida |
| Apontamentos | MES | MES | Operacional | Saida |
| Qualidade de processo | MES | MES | Operacional | Saida ou consulta |
| Nota fiscal | ERP | ERP | Somente referencia | Entrada |

### 6.7 Politica de Evolucao e Versionamento

Dados criticos devem possuir regras de evolucao alem da politica de sincronizacao.

A politica deve diferenciar alteracao simples, alteracao critica, alteracao incompativel, necessidade de aprovacao, geracao de nova versao, inativacao, obsolescencia e bloqueio.

Estruturas de produto e roteiros devem gerar nova versao ou revisao quando houver alteracao relevante. Ordens existentes deverao preservar a versao de estrutura e roteiro utilizada no momento definido pela futura arquitetura de producao.

### 6.8 Classificacao dos Tipos de Informacao e Sincronizacao

Categorias de informacao:

- Cadastro Mestre: Produto, Cliente, Fornecedor, Colaborador, Unidade de Medida, Recurso, Linha e Centro de Trabalho.
- Documento Operacional: Pedido, Demanda, Ordem de Producao, Ordem de Compra como referencia, Plano Mestre e Programacao.
- Evento Operacional: Apontamento, Movimentacao, Parada, Liberacao, Bloqueio, Mudanca de Prioridade, Mudanca de Sequenciamento, Resultado de Qualidade e Material Disponibilizado.

Tipos de sincronizacao:

- Sincronizacao Mestre: dados relativamente estaveis, podendo tolerar processamento agendado conforme implantacao.
- Sincronizacao Documental: documentos com ciclo de vida.
- Sincronizacao Operacional: eventos que devem chegar quase em tempo real.

### 6.9 Arquitetura Orientada a Eventos

Mudancas operacionais relevantes devem ser propagadas como eventos. A infraestrutura de eventos podera ser utilizada para comunicacao externa e interna entre modulos.

Exemplos conceituais:

- SequenciamentoAlterado;
- OrdemProducaoLiberada;
- OrdemProducaoIniciada;
- OrdemProducaoPausada;
- OrdemProducaoConcluida;
- MaterialDisponibilizado;
- LoteBloqueado;
- LoteLiberado;
- TarefaAtribuida;
- PedidoParcialmenteProduzido;
- PedidoProduzido;
- SincronizacaoFalhou.

Nao fica escolhida tecnologia definitiva nesta sessao.

### 6.10 Vendas, Pedidos, Demandas, OPs e Campanhas

O modulo de vendas do MES sera operacional e nao fiscal. Pedidos podem nascer no MES, no ERP, em aplicacao de campo, em e-commerce ou em outro sistema integrado.

O pedido deve preservar origem, sistema de origem, identificador externo, empresa ou estabelecimento, usuario criador quando local e vendedor responsavel quando aplicavel. Usuario criador e vendedor responsavel sao conceitos diferentes.

Numeracao externa nao deve ser utilizada como identificador interno unico sem considerar sistema de origem, empresa, estabelecimento e codigo externo.

Impactos futuros registrados:

- um item de pedido pode ser atendido por varias ordens de producao;
- uma ordem de producao pode atender varios itens ou demandas;
- a relacao devera ocorrer por alocacao de demanda;
- o PCP podera consolidar demandas compativeis antes de gerar uma OP;
- ordens existentes nao deverao ser fundidas por exclusao ou sobrescrita;
- podera existir o conceito de Campanha de Producao;
- uma campanha podera agrupar multiplas OPs para execucao conjunta;
- materiais, perdas e mao de obra serao registrados como fatos reais;
- rateios serao separados, parametrizaveis e auditaveis.

O detalhamento ficara para futura Architecture Session de Ordens e Campanhas de Producao.

### 6.11 Exclusao, Inativacao e Obsolescencia

Dados externos nao deverao provocar exclusao fisica automatica no MES.

Quando um sistema externo excluir ou inativar produto, o MES devera avaliar estoque existente, lotes, ordens abertas, historico, apontamentos, qualidade e movimentacoes.

O comportamento normal sera inativar, marcar como obsoleto, impedir novos usos e preservar historico e rastreabilidade. Registros com historico operacional nunca devem ser excluidos.

### 6.12 Estado, Idempotencia, Conflitos e Ordem das Mensagens

Estados conceituais de sincronizacao:

- Recebida;
- Validando;
- Validada;
- Pendente;
- Processando;
- Processada;
- Sincronizada;
- Ignorada;
- Duplicada;
- Em conflito;
- Aguardando aprovacao;
- Erro;
- Aguardando reprocessamento;
- Reprocessando;
- Desatualizada;
- Cancelada.

A arquitetura devera impedir que a mesma mensagem produza efeitos duplicados, considerando identificador da mensagem, sistema de origem, tipo da mensagem, chave de idempotencia, versao, correlacao, data de recebimento e hash do conteudo quando aplicavel.

Conflitos nao devem sobrescrever silenciosamente dados. Estrategias conceituais incluem sistema oficial vence, MES vence, bloquear alteracao, criar pendencia, exigir aprovacao, mesclar campos permitidos, gerar nova versao ou ignorar mensagem obsoleta.

Mensagens podem chegar duplicadas, atrasadas, fora de ordem, apos versao mais recente ou por reprocessamento. A arquitetura deve prever comparacao de versao, timestamp, sequencia, revisao e estado atual.

### 6.13 Resiliencia, Reprocessamento, Auditoria e Observabilidade

A indisponibilidade de ERP ou sistema externo nao deve interromper o funcionamento operacional do MES.

O MES devera continuar executando, conforme o processo permitir, movimentacoes, tarefas, apontamentos, producao, qualidade e rastreabilidade. Mensagens de saida deverao permanecer pendentes para processamento posterior.

A arquitetura deve permitir retentativa automatica, retentativa com intervalo, limite de tentativas, fila logica de erro, reprocessamento manual, correcao da causa, preservacao do payload original, registro do usuario que reprocessou e historico de todas as tentativas.

Toda sincronizacao devera registrar conceitualmente sistema de origem, sistema de destino, tipo de informacao, identificador externo, identificador interno, mensagem original, mensagem canonica, data e hora, status, resultado, quantidade de tentativas, erro, correlacao e usuario quando houver acao manual.

Dados sensiveis devem ser protegidos. Credenciais nunca deverao ser registradas em logs.

A arquitetura deve prever futura Central de Sincronizacao para consultar mensagens, sistemas, entidades, erros, conflitos, pendencias, tentativas, correlacoes, estados e indicadores. Nao fica implementada tela nesta sessao.

### 6.14 Seguranca

Requisitos conceituais:

- autenticacao entre sistemas;
- autorizacao por integracao;
- principio do menor privilegio;
- protecao de credenciais;
- criptografia em transito;
- mascaramento de dados sensiveis;
- auditoria de acoes administrativas;
- segregacao por organizacao, empresa e estabelecimento;
- prevencao de replay;
- validacao de payload;
- limites de tamanho;
- protecao contra mensagens malformadas.

### 6.15 Organizacao, Empresa, Estabelecimento e Planta

A arquitetura devera futuramente diferenciar organizacao ou tenant, empresa, estabelecimento ou filial e planta industrial.

Integracoes, referencias externas e politicas poderao variar por organizacao, empresa, estabelecimento e planta.

Nao fica criado modelo fisico nesta sessao.

### 6.16 Limites Fiscais

Documento fiscal nao faz parte do dominio fiscal do MES. Nota fiscal podera existir somente como referencia operacional para recebimento, conferencia, origem do material e rastreabilidade documental.

O MES nao devera calcular impostos, emitir documento fiscal, assinar documento fiscal, transmitir documento a SEFAZ ou substituir ERP fiscal.

### 6.17 Principio de Consistencia Operacional

O dominio industrial e o historico operacional do MES nao poderao ser corrompidos por mensagens externas.

O sistema externo podera propor ou informar mudancas. A aplicacao e o dominio deverao decidir se a mudanca e valida, pode ser aplicada, requer versao, requer aprovacao, deve ser rejeitada, deve gerar conflito ou deve gerar pendencia.

## 7. Fora do Escopo

- implementacao de adaptadores especificos;
- implementacao de broker;
- escolha definitiva de mensageria;
- criacao fisica das entidades;
- criacao de tabelas;
- migrations;
- APIs especificas;
- autenticacao especifica de fornecedores;
- sincronizacao real com ERP;
- emissao fiscal;
- comunicacao com SEFAZ;
- definicao detalhada de MRP;
- definicao detalhada de OP;
- definicao detalhada de campanhas;
- rateio fisico ou contabil;
- custeio industrial;
- telas operacionais;
- implementacao de notificacoes;
- definicao fisica de SignalR, WebSocket ou alternativa;
- implantacao de microservicos.

## 8. Impactos Funcionais

- cadastros mestres;
- pedidos;
- demandas;
- ordens de producao;
- estruturas de produto;
- roteiros;
- estoque;
- lotes;
- movimentacoes;
- apontamentos;
- qualidade;
- rastreabilidade;
- tarefas;
- PCP;
- central futura de sincronizacao;
- paineis e dispositivos operacionais.

Estes impactos nao declaram que os itens ja foram implementados.

## 9. Impactos Tecnicos

Necessidades futuras:

- conceito tecnico de Sistema Externo;
- referencias externas desacopladas;
- contratos canonicos;
- adaptadores;
- Hub de Sincronizacao;
- politicas de sincronizacao;
- politicas de evolucao;
- auditoria;
- idempotencia;
- correlacao;
- reprocessamento;
- observabilidade;
- seguranca de integracoes;
- eventos internos e externos.

Nao ficam definidas tabelas, propriedades, classes, migrations, endpoints ou tecnologias definitivas.

## 10. Riscos

- acoplamento indevido do dominio a fornecedores externos;
- uso de codigo externo como identificador interno unico;
- sobrescrita silenciosa de dados industriais;
- sincronizacao bidirecional irrestrita;
- perda de historico operacional;
- exclusao fisica indevida;
- mensagens duplicadas gerando efeitos duplicados;
- mensagens atrasadas sobrescrevendo versoes recentes;
- ausencia de auditoria suficiente;
- vazamento de credenciais ou dados sensiveis;
- dependencia operacional de ERP indisponivel;
- mistura entre regra tecnica de integracao e regra de negocio do MES.

## 11. Decisoes Deliberadamente Adiadas

- modelo fisico de Sistema Externo;
- modelo fisico de Referencia Externa;
- definicao de classes canonicas;
- desenho final do Hub de Sincronizacao;
- tecnologia de broker, fila ou mensageria;
- estrategia fisica de eventos internos;
- padrao tecnico de contratos;
- autenticacao especifica por fornecedor;
- adaptadores para SAP, Protheus, Sankhya, Senior ou outros sistemas;
- modelo de Central de Sincronizacao;
- telas;
- APIs;
- migrations;
- detalhamento de MRP;
- detalhamento de OPs e campanhas;
- criterios finais de versao de estruturas e roteiros em ordens existentes.

## 12. Decision Logs Derivados

- DL-0012 - Independencia do MES em Relacao a ERPs
- DL-0013 - Sistema Externo e Referencias Externas
- DL-0014 - Hub de Sincronizacao
- DL-0015 - Modelo Canonico e Adaptadores
- DL-0016 - Politicas de Sincronizacao e Governanca
- DL-0017 - Politicas de Evolucao e Versionamento
- DL-0018 - Classificacao das Sincronizacoes
- DL-0019 - Arquitetura Orientada a Eventos
- DL-0020 - Resiliencia, Idempotencia, Auditoria e Reprocessamento
- DL-0021 - Limites Fiscais do Produto

## 13. Criterios de Encerramento

A sessao foi considerada concluida porque respondeu:

- como o MES funciona sem ERP;
- como funciona com ERP;
- como funciona de maneira hibrida;
- como sistemas externos sao representados;
- como codigos externos sao associados a registros internos;
- como o dominio permanece independente de ERPs;
- como cada tipo de dado define sua origem oficial;
- como edicao e conflitos sao controlados;
- como exclusoes externas sao tratadas;
- como mensagens duplicadas e atrasadas sao tratadas;
- como falhas sao reprocessadas;
- como estruturas e roteiros preservam versoes;
- como mudancas operacionais chegam rapidamente ao chao de fabrica;
- como o MES continua operando quando o ERP esta indisponivel;
- quais responsabilidades fiscais estao fora do produto.

## 14. Proximos Passos

1. Revisar a AS-0003.
2. Revisar os Decision Logs derivados.
3. Validar se nenhuma decisao foi inventada.
4. Fazer commit documental apos aprovacao.
5. Atualizar futuramente AI_CONTEXT, Roadmap e Backlog, se aprovado.
6. Nao implementar codigo antes de uma definicao tecnica aprovada.
