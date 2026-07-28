# Glossario Arquitetural do MES

## 1. Objetivo

Este documento define o vocabulario arquitetural oficial do produto MES/MOM.

O glossario consolida conceitos ja discutidos, aprovados ou planejados no Project Book. Ele nao substitui Architecture Sessions, nao substitui Decision Logs e nao cria novas decisoes. Qualquer nova definicao relevante devera nascer primeiro em discussao e Architecture Session apropriada.

## 2. Escopo

O escopo cobre termos relacionados a arquitetura geral do MES/MOM, estoque operacional, movimentacoes, operacao guiada, tarefas, jornadas, rastreabilidade, integracao, sincronizacao, governanca, eventos, pedidos, demandas, ordens, engenharia e producao.

Quando um termo estiver marcado como futuro ou preliminar, ele nao deve ser tratado como decisao de implementacao.

## 3. Regras de Manutencao

- Preservar as decisoes aprovadas nas AS-0001, AS-0002 e AS-0003.
- Nao transformar hipoteses em decisoes.
- Nao criar entidades, campos, tabelas, migrations, endpoints, classes, servicos ou adaptadores.
- Nao escolher tecnologia fisica.
- Usar este glossario para padronizar linguagem, nao para aprovar escopo tecnico.
- Atualizar referencias quando novas Architecture Sessions ou Decision Logs aprovarem mudancas conceituais.

## 4. Termos Arquiteturais

### MES

**Definicao**

Sistema responsavel pela execucao, controle, rastreabilidade e acompanhamento das operacoes industriais.

**Nao significa**

Nao substitui obrigatoriamente ERP, sistema fiscal ou sistema financeiro.

**Referencias**

- `../15 - Architecture Sessions/AS-0003 - Arquitetura de Integracao Sincronizacao Governanca e Eventos.md`

### MOM

**Definicao**

Conjunto mais amplo de capacidades de gestao das operacoes de manufatura, incluindo producao, qualidade, estoque operacional, manutencao e desempenho.

**Referencias**

- `../15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`
- `../15 - Architecture Sessions/AS-0002 - Movimentacoes de Estoque e Operacao Assistida.md`

### Produto

**Definicao**

Entidade interna do MES que representa um item comprado, produzido, consumido, armazenado ou comercializado.

**Nao significa**

Produto nao deve conhecer diretamente codigos especificos de ERPs.

**Referencias**

- `../12 - Decision Log/DL-0012 - Independencia do MES em Relacao a ERPs.md`
- `../12 - Decision Log/DL-0013 - Sistema Externo e Referencias Externas.md`

### Tipo de Produto

**Definicao**

Classificacao funcional do produto, como materia-prima, componente, produto intermediario, produto acabado, material de embalagem, insumo ou consumivel.

**Nao significa**

Nao cria enumeracao fisica nesta documentacao.

### Unidade de Medida

**Definicao**

Unidade utilizada para quantificar, movimentar, consumir, produzir ou armazenar materiais.

### Area de Estoque

**Definicao**

Agrupamento logico ou fisico de localizacoes de estoque com finalidade operacional comum.

**Referencias**

- `../15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`
- `../12 - Decision Log/DL-0001 - Arquitetura de Endereçamento de Estoque.md`

### Localizacao de Estoque

**Definicao**

Referencia do posicionamento atual de uma Unidade Logistica em determinado momento. Nao e Aggregate Root concorrente, referencia um LocalDeEstoque e podera futuramente ser modelada como Value Object ou conceito equivalente.

**Observacao**

Preserva compatibilidade conceitual com os documentos anteriores de enderecamento, mas nao deve ser tratada como sinonimo de LocalDeEstoque.

**Referencias**

- `../12 - Decision Log/DL-0002 - Identidade Unicidade e Historico de Localizacoes.md`
- `../12 - Decision Log/DL-0004 - Ciclo de Vida e Alteracoes Estruturais de Localizacoes.md`

### Tipo de Localizacao

**Definicao**

Catalogo global que classifica localizacoes conforme sua funcao operacional.

**Referencias**

- `../12 - Decision Log/DL-0001 - Arquitetura de Endereçamento de Estoque.md`

### Jornada do Material

**Definicao**

Historico completo dos estados, movimentos, eventos e operacoes pelos quais um material, lote ou unidade logistica passou.

**Referencias**

- `../12 - Decision Log/DL-0009 - Jornada do Material e Jornada do Operador.md`

### Jornada do Operador

**Definicao**

Historico das tarefas, decisoes, movimentacoes, apontamentos e operacoes executadas pelo operador.

**Referencias**

- `../12 - Decision Log/DL-0009 - Jornada do Material e Jornada do Operador.md`

### Movimentacao de Estoque

**Definicao**

Registro imutavel de transferencia, entrada, saida, consumo, retorno, ajuste ou mudanca de estado de um material.

**Nao significa**

Nao deve ser editada ou apagada depois de concluida; correcoes devem preservar historico.

**Referencias**

- `../12 - Decision Log/DL-0006 - Movimentacoes de Estoque em Etapas e Material em Transito.md`
- `../12 - Decision Log/DL-0007 - Historico Imutavel Eventos e Correcoes de Estoque.md`

### Material em Transito

**Definicao**

Material que deixou uma localizacao de origem, mas ainda nao foi confirmado na localizacao de destino.

### Estorno

**Definicao**

Operacao compensatoria utilizada para corrigir uma movimentacao anterior sem apagar o historico.

### Tarefa Operacional

**Definicao**

Unidade de trabalho atribuivel a operador, equipe ou funcao, com origem, prioridade, estado, instrucoes e resultado.

**Referencias**

- `../12 - Decision Log/DL-0010 - Arquitetura de Tarefas Operacionais.md`

### Operacao Guiada

**Definicao**

Modelo no qual o sistema orienta o operador, valida etapas e reduz decisoes livres que possam causar erros.

**Referencias**

- `../12 - Decision Log/DL-0008 - Operacao Assistida e Validacoes em Tempo Real.md`

### Equipe Operacional

**Definicao**

Agrupamento de colaboradores que pode receber, executar ou compartilhar tarefas operacionais.

**Nao significa**

Nao substitui Role, permissao ou autenticacao.

**Referencias**

- `../12 - Decision Log/DL-0011 - Distribuicao de Tarefas por Identity Role e Equipe Operacional.md`

### Sistema Externo

**Definicao**

Qualquer sistema, aplicacao, plataforma, arquivo ou servico que se comunique com o MES.

**Exemplos**

ERP, WMS, APS, CRM, RH, SCADA, PIMS, LIMS, marketplace, API, aplicacao movel, arquivo estruturado ou sistema proprio.

**Referencias**

- `../12 - Decision Log/DL-0013 - Sistema Externo e Referencias Externas.md`

### Referencia Externa

**Definicao**

Associacao desacoplada entre uma entidade interna do MES e um identificador utilizado em um Sistema Externo.

**Nao significa**

Nao e um campo unico `CodigoErp` dentro da entidade principal.

### Codigo Interno

**Definicao**

Identificador funcional pertencente ao dominio do MES.

### Codigo Externo

**Definicao**

Identificador utilizado por um sistema externo, associado ao MES por meio de Referencia Externa.

### Modelo Canonico

**Definicao**

Representacao padronizada e independente de fornecedor utilizada para troca de dados entre sistemas externos e o MES.

**Referencias**

- `../12 - Decision Log/DL-0015 - Modelo Canonico e Adaptadores.md`

### Adaptador

**Definicao**

Componente responsavel por interpretar o formato de um sistema externo e converte-lo para o modelo canonico, e vice-versa.

**Nao significa**

Nao contem regra de negocio industrial do MES.

### Hub de Sincronizacao

**Definicao**

Modulo responsavel por coordenar tecnicamente o recebimento, validacao, transformacao, rastreabilidade, idempotencia, retentativa e saida de mensagens.

**Nao significa**

O Hub nao contem regra de negocio industrial, nao grava diretamente nas tabelas do dominio, nao decide se uma alteracao e valida e entrega comandos, mensagens ou intencoes aos servicos de aplicacao.

**Referencias**

- `../12 - Decision Log/DL-0014 - Hub de Sincronizacao.md`

### Integracao

**Definicao**

Meio tecnico utilizado para permitir comunicacao entre sistemas.

### Sincronizacao

**Definicao**

Comportamento que determina como informacoes sao recebidas, enviadas, atualizadas, conciliadas, rejeitadas ou reprocessadas.

### Politica de Sincronizacao

**Definicao**

Configuracao que define origem, sistema oficial, destino, direcao, edicao permitida, periodicidade, estrategia, comportamento em conflito e comportamento em indisponibilidade.

**Referencias**

- `../12 - Decision Log/DL-0016 - Politicas de Sincronizacao e Governanca.md`

### Politica de Evolucao

**Definicao**

Regra que determina como uma informacao pode mudar ao longo do tempo.

**Responsabilidade ou finalidade**

Pode estabelecer alteracao direta, bloqueio, aprovacao, versionamento, revisao, inativacao, obsolescencia ou geracao de pendencia.

**Referencias**

- `../12 - Decision Log/DL-0017 - Politicas de Evolucao e Versionamento.md`

### Sistema de Origem

**Definicao**

Sistema no qual um registro ou mensagem foi inicialmente criado.

### Sistema Oficial

**Definicao**

Sistema reconhecido como autoridade principal para determinado tipo de informacao.

**Nao significa**

Sistema de Origem e Sistema Oficial podem ser diferentes em alguns fluxos.

### Propriedade da Informacao

**Definicao**

Responsabilidade oficial pela criacao, alteracao, validacao e evolucao de determinado tipo de dado.

### Sincronizacao Mestre

**Definicao**

Sincronizacao de dados relativamente estaveis, como produtos, clientes, fornecedores, colaboradores, unidades e recursos.

### Sincronizacao Documental

**Definicao**

Sincronizacao de documentos com ciclo de vida, como pedidos, demandas, ordens e planos.

### Sincronizacao Operacional

**Definicao**

Propagacao de eventos e mudancas que precisam chegar rapidamente aos usuarios ou modulos operacionais.

**Referencias**

- `../12 - Decision Log/DL-0018 - Classificacao das Sincronizacoes.md`

### Evento

**Definicao**

Registro de um fato relevante ocorrido no dominio.

**Exemplos**

OrdemProducaoLiberada, SequenciamentoAlterado, MaterialDisponibilizado, LoteBloqueado e TarefaAtribuida.

**Nao significa**

Evento informa que algo ocorreu; ele nao solicita uma acao.

**Referencias**

- `../12 - Decision Log/DL-0019 - Arquitetura Orientada a Eventos.md`

### Comando

**Definicao**

Intencao explicita de solicitar que o sistema execute uma acao.

**Nao significa**

Comando solicita uma acao; evento informa que algo ocorreu.

### Idempotencia

**Definicao**

Capacidade de processar novamente uma mesma mensagem sem produzir efeitos duplicados.

### Correlacao

**Definicao**

Mecanismo que relaciona mensagens, comandos, eventos, tentativas e operacoes pertencentes ao mesmo fluxo.

### Reprocessamento

**Definicao**

Nova tentativa controlada de processar uma mensagem que falhou ou ficou pendente.

**Referencias**

- `../12 - Decision Log/DL-0020 - Resiliencia Idempotencia Auditoria e Reprocessamento.md`

### Conflito de Sincronizacao

**Definicao**

Situacao em que existem alteracoes concorrentes ou incompativeis entre sistemas.

### Estado da Sincronizacao

**Definicao**

Condicao atual de uma mensagem ou processo de sincronizacao.

**Exemplos**

Recebida, validando, pendente, processando, sincronizada, duplicada, em conflito, erro, aguardando reprocessamento, reprocessando e cancelada.

### Documento Operacional

**Definicao**

Registro que possui finalidade operacional e ciclo de vida dentro do MES.

### Documento Fiscal de Referencia

**Definicao**

Informacao fiscal recebida exclusivamente para apoiar recebimento, conferencia ou rastreabilidade.

**Nao significa**

O MES nao calcula impostos, nao emite nota e nao se comunica com a SEFAZ.

**Referencias**

- `../12 - Decision Log/DL-0021 - Limites Fiscais do Produto.md`

### Pedido

**Definicao**

Documento de demanda comercial ou operacional que pode nascer no MES ou em sistema externo.

### PedidoItem

**Definicao**

Item que representa a necessidade de determinado produto e quantidade dentro de um pedido.

### Demanda

**Definicao**

Necessidade produtiva originada por pedido, planejamento, reposicao ou outro processo.

### Alocacao de Demanda

**Definicao**

Associacao entre uma demanda e uma ou mais ordens de producao.

### Ordem de Producao

**Definicao**

Documento operacional que autoriza e controla a fabricacao de determinado produto, quantidade e versao de engenharia.

### Campanha de Producao

**Definicao**

Agrupamento de multiplas ordens de producao para execucao conjunta ou coordenada.

**Nao significa**

Conceito futuro ainda nao detalhado em Architecture Session propria.

### Estrutura de Produto

**Definicao**

Composicao de materiais, componentes e quantidades necessaria para fabricar um produto.

### Roteiro

**Definicao**

Sequencia planejada de operacoes, recursos e criterios necessarios para executar a fabricacao.

### Revisao

**Definicao**

Identificacao de uma evolucao controlada de estrutura, roteiro, especificacao ou outro dado critico.

### Execucao de Operacao

**Definicao**

Ocorrencia real de execucao de uma operacao produtiva em determinado contexto de tempo, recurso, equipe e turno.

### Apontamento

**Definicao**

Registro operacional de producao, consumo, quantidade boa, refugo, perda, tempo, parada ou outro fato ocorrido na execucao.

### Fato Real

**Definicao**

Registro original da ocorrencia operacional, antes de qualquer rateio ou distribuicao analitica.

### Rateio

**Definicao**

Distribuicao posterior de um fato real entre pedidos, demandas, clientes, centros ou outros objetos.

### Lote

**Definicao**

Identificacao utilizada para agrupar materiais ou produtos com origem, fabricacao, validade ou caracteristicas comuns.

### Rastreabilidade

**Definicao**

Capacidade de reconstruir origem, transformacao, movimentacao, consumo, producao e destino de materiais e produtos.

### Standalone

**Definicao**

Modo em que o MES opera sem depender de ERP.

### Integrado

**Definicao**

Modo em que o MES recebe ou envia dados para sistemas externos conforme politicas definidas.

### Hibrido

**Definicao**

Modo em que parte dos dados nasce no MES e parte em sistemas externos.

## 4.1 Termos do Dominio de Estoque

### Primeira Vertical Funcional de Estoque

**Definicao**

Recorte incremental definido pela AS-0005 para validar ponta a ponta Local de Estoque, Unidade Logistica, Movimentacao, Confirmacao, Consulta e Historico antes da retomada da codificacao.

**Nao significa**

Nao entrega todo o Dominio de Estoque e nao inclui automaticamente recebimento, reserva, inventario, ajuste, MRP ou apontamento.

**Referencias**

- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `../12 - Decision Log/DL-0030 - Primeira Vertical Funcional do Dominio de Estoque.md`
### Dominio de Estoque

**Definicao**

Bounded context funcional responsavel por representar processos fisicos e virtuais de estoque e logistica interna.

**Nao significa**

Nao e um Aggregate Root chamado Estoque.

**Referencias**

- `../15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `../12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`

### Aggregate Root

**Definicao**

Raiz de consistencia de um agregado, responsavel por proteger invariantes e coordenar alteracoes internas.

**Nao significa**

Nao representa necessariamente uma tabela ou tela.

### Unidade Logistica

**Definicao**

Objeto fisico identificavel, manipulavel e rastreavel, que contem ou representa determinada quantidade de material.

**Referencias**

- `../12 - Decision Log/DL-0023 - Unidade Logistica como Agregado Fisico.md`

### Tipo de Unidade Logistica

**Definicao**

Cadastro mestre que classifica formas logisticas como pallet, caixa, big bag, rack, tambor ou recipiente.

### Instancia de Unidade Logistica

**Definicao**

Objeto operacional identificado no MES, como `UL-000123`.

### UL explicita

**Definicao**

Modo em que a Unidade Logistica aparece claramente para o operador e e identificada durante a operacao.

### UL implicita

**Definicao**

Modo em que o backend opera sobre Unidade Logistica, mas a interface oculta esse conceito em operacoes simples.

### UL virtual

**Definicao**

Unidade Logistica conceitual utilizada quando o processo precisa de identidade operacional sem uma embalagem fisica destacada.

### Local de Estoque

**Definicao**

Aggregate Root do dominio de Estoque que representa o endereco ou espaco fisico governado. Possui identidade, hierarquia, capacidade, restricoes, tipo, compatibilidade e estado operacional.

**Modelo conceitual**

- LocalDeEstoque = endereco fisico governado pelo dominio.
- LocalizacaoEstoque = referencia de posicionamento atual da Unidade Logistica.

**Referencias**

- `../12 - Decision Log/DL-0024 - Local de Estoque como Aggregate Root.md`

### Expectativa de Recebimento

**Definicao**

Previsao de algo que o dominio espera receber, independente de origem tecnologica e independente do Recebimento.

**Referencias**

- `../12 - Decision Log/DL-0027 - Expectativa de Recebimento e Recebimento Operacional.md`

### Origem Operacional

**Definicao**

Classificacao da origem de uma expectativa ou recebimento, como Compra, Transferencia, Industrializacao, Devolucao, Ajuste, Avulso ou Outro Sistema.

### Recebimento

**Definicao**

Processo operacional de entrada fisica, conferencia, divergencia, custodia inicial, criacao ou associacao de UL e destinacao.

**Nao significa**

Nao e documento de ERP.

### Custodia

**Definicao**

Responsabilidade operacional temporaria sobre uma Unidade Logistica ou material em determinado momento do processo.

### Disponibilidade

**Definicao**

Condicao derivada que indica se quantidade fisica pode ser utilizada, reservada, consumida ou movimentada conforme regras do dominio.

### Reserva de Estoque

**Definicao**

Aggregate Root que coordena alocacao de estoque para atendimento de demanda.

**Referencias**

- `../12 - Decision Log/DL-0026 - Reserva de Estoque Disponibilidade e Concorrencia.md`

### Alocacao

**Definicao**

Associacao de quantidade disponivel de uma ou mais ULs a uma demanda ou reserva.

### Quantidade fisica

**Definicao**

Quantidade material real contida ou representada por uma Unidade Logistica.

### Quantidade disponivel

**Definicao**

Quantidade fisica que pode ser usada apos descontar bloqueios, reservas, movimentacoes e consumos conforme politica.

### Quantidade reservada

**Definicao**

Quantidade alocada para uma demanda especifica.

### Quantidade bloqueada

**Definicao**

Quantidade impedida de uso por qualidade, divergencia, decisao operacional ou regra configurada.

### Quantidade em transito

**Definicao**

Quantidade retirada da origem e ainda nao confirmada no destino.

### Quantidade em consumo

**Definicao**

Quantidade comprometida por processo de consumo ainda nao concluido ou conciliado.

### Politica de Contagem

**Definicao**

Aggregate Root configuravel que define regras para gerar inventarios ou tarefas de contagem. Possui identidade, versao, vigencia, estado ativo ou inativo, criterios de selecao, frequencia, escopo, tolerancias, regras de recontagem, regras de aprovacao e ciclo de vida proprio.

**Referencias**

- `../12 - Decision Log/DL-0028 - Politica de Contagem Inventario e Ajuste de Estoque.md`

### Inventario

**Definicao**

Execucao operacional de uma contagem, com escopo, tarefas, responsaveis, resultados, divergencias, recontagens, reconciliacao e encerramento.

### Tarefa de Contagem

**Definicao**

Unidade interna de trabalho do Inventario para orientar e registrar uma contagem especifica.

**Nao significa**

Na primeira versao, nao e Aggregate Root proprio.

### Contagem

**Definicao**

Registro do que foi encontrado fisicamente durante inventario.

### Recontagem

**Definicao**

Nova contagem solicitada para confirmar ou revisar divergencia.

### Reconciliacao

**Definicao**

Comparacao entre contagem fisica e estoque virtual projetado.

### Ajuste de Estoque

**Definicao**

Operacao formal, autorizada e auditavel que altera o estoque virtual apos divergencia validada.

### Projecao de Saldo

**Definicao**

Visao derivada de fatos operacionais confirmados, usada para consulta, disponibilidade e relatorios.

**Nao significa**

Nao e fonte primaria para alteracoes operacionais.

### Evento de Dominio

**Definicao**

Fato relevante ocorrido no dominio, registrado no passado. Nao deve ser confundido com comando, estado do agregado ou alerta operacional derivado.

### Alerta Operacional Derivado

**Definicao**

Sinal produzido por monitoramento temporal, projecao ou servico operacional. Pode estar correlacionado a um Aggregate Root, mas nao representa necessariamente uma transicao direta dele.

**Exemplos**

- LimiteDeTempoDaMovimentacaoAproximado.
- AtrasoDeMovimentacaoDetectado.

**Referencias**

- `../15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `../12 - Decision Log/DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `../12 - Decision Log/DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `../12 - Decision Log/DL-0030 - Primeira Vertical Funcional do Dominio de Estoque.md`

### Causacao

**Definicao**

Relacao que identifica qual comando, evento ou operacao causou outro evento.

### Concorrencia otimista

**Definicao**

Mecanismo de consistencia que detecta alteracoes concorrentes antes de confirmar uma operacao critica.

### Aderencia fisico versus virtual

**Definicao**

Principio segundo o qual o estoque virtual deve refletir os fatos fisicos confirmados da operacao.

### Homogeneidade

**Definicao**

Regra que define quais atributos podem coexistir dentro de uma Unidade Logistica.

### Hierarquia de UL

**Definicao**

Estrutura opcional em que uma Unidade Logistica pode conter outras ULs, como pallet, caixa e bandeja.

### Genealogia de UL

**Definicao**

Historico de origem, agrupamento, desagrupamento, divisao e consolidacao de Unidades Logisticas.

## 5. Relacoes entre os Principais Conceitos

```text
Area de Estoque
-> Localizacao de Estoque
-> Movimentacao de Estoque
-> Jornada do Material
```

```text
Evento de negocio
-> Tarefa Operacional
-> Operacao Guiada
-> Jornada do Operador
```

```text
Sistema Externo
-> Adaptador
-> Modelo Canonico
-> Hub de Sincronizacao
-> Servico de Aplicacao
-> Dominio MES
```

```text
Pedido
-> PedidoItem
-> Demanda
-> Alocacao de Demanda
-> Ordem de Producao
```

```text
Estrutura de Produto
-> Revisao
-> Ordem de Producao
-> Execucao de Operacao
-> Apontamento
```

## 6. Referencias as Architecture Sessions e Decision Logs

### Architecture Sessions

- `../15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`
- `../15 - Architecture Sessions/AS-0002 - Movimentacoes de Estoque e Operacao Assistida.md`
- `../15 - Architecture Sessions/AS-0003 - Arquitetura de Integracao Sincronizacao Governanca e Eventos.md`
- `../15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `../15 - Architecture Sessions/AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`

### Decision Logs

- `../12 - Decision Log/DL-0001 - Arquitetura de Endereçamento de Estoque.md`
- `../12 - Decision Log/DL-0002 - Identidade Unicidade e Historico de Localizacoes.md`
- `../12 - Decision Log/DL-0003 - Capacidade Ocupacao e Compatibilidade de Armazenagem.md`
- `../12 - Decision Log/DL-0004 - Ciclo de Vida e Alteracoes Estruturais de Localizacoes.md`
- `../12 - Decision Log/DL-0005 - Navegacao Visualizacao e Recomendacao de Localizacoes.md`
- `../12 - Decision Log/DL-0006 - Movimentacoes de Estoque em Etapas e Material em Transito.md`
- `../12 - Decision Log/DL-0007 - Historico Imutavel Eventos e Correcoes de Estoque.md`
- `../12 - Decision Log/DL-0008 - Operacao Assistida e Validacoes em Tempo Real.md`
- `../12 - Decision Log/DL-0009 - Jornada do Material e Jornada do Operador.md`
- `../12 - Decision Log/DL-0010 - Arquitetura de Tarefas Operacionais.md`
- `../12 - Decision Log/DL-0011 - Distribuicao de Tarefas por Identity Role e Equipe Operacional.md`
- `../12 - Decision Log/DL-0012 - Independencia do MES em Relacao a ERPs.md`
- `../12 - Decision Log/DL-0013 - Sistema Externo e Referencias Externas.md`
- `../12 - Decision Log/DL-0014 - Hub de Sincronizacao.md`
- `../12 - Decision Log/DL-0015 - Modelo Canonico e Adaptadores.md`
- `../12 - Decision Log/DL-0016 - Politicas de Sincronizacao e Governanca.md`
- `../12 - Decision Log/DL-0017 - Politicas de Evolucao e Versionamento.md`
- `../12 - Decision Log/DL-0018 - Classificacao das Sincronizacoes.md`
- `../12 - Decision Log/DL-0019 - Arquitetura Orientada a Eventos.md`
- `../12 - Decision Log/DL-0020 - Resiliencia Idempotencia Auditoria e Reprocessamento.md`
- `../12 - Decision Log/DL-0021 - Limites Fiscais do Produto.md`
- `../12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `../12 - Decision Log/DL-0023 - Unidade Logistica como Agregado Fisico.md`
- `../12 - Decision Log/DL-0024 - Local de Estoque como Aggregate Root.md`
- `../12 - Decision Log/DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `../12 - Decision Log/DL-0026 - Reserva de Estoque Disponibilidade e Concorrencia.md`
- `../12 - Decision Log/DL-0027 - Expectativa de Recebimento e Recebimento Operacional.md`
- `../12 - Decision Log/DL-0028 - Politica de Contagem Inventario e Ajuste de Estoque.md`
- `../12 - Decision Log/DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `../12 - Decision Log/DL-0030 - Primeira Vertical Funcional do Dominio de Estoque.md`
- `../12 - Decision Log/DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao.md`
- `../12 - Decision Log/DL-0032 - Definition of Ready para Retomada da Codificacao.md`


### Transactional Outbox

**Definicao**

Padrao em que eventos de dominio sao gravados na mesma transacao local da alteracao do dominio e publicados posteriormente por processo separado.

**Nao significa**

Nao garante entrega exactly once e nao substitui idempotencia dos consumidores.

**Referencias**

- `../12 - Decision Log/DL-0036 - Transactional Outbox para Eventos da Primeira Vertical de Estoque.md`
- `../15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`

### Idempotency Key

**Definicao**

Chave enviada em comandos mutaveis para impedir que repeticoes do mesmo comando produzam efeitos duplicados.

**Nao significa**

Nao e correlation ID; a correlation ID rastreia uma jornada, enquanto a Idempotency Key identifica uma tentativa logica de comando.

**Referencias**

- `../12 - Decision Log/DL-0033 - Estrategia de Idempotencia dos Comandos da Primeira Vertical de Estoque.md`

### Strangler Pattern

**Definicao**

Estrategia incremental para introduzir novo dominio ao lado do legado, encapsulando e substituindo fluxos antigos gradualmente.

**Nao significa**

Nao autoriza big bang nem escrita dupla nao transacional.

**Referencias**

- `../12 - Decision Log/DL-0037 - Estrategia de Coexistencia com o Legado de Estoque.md`