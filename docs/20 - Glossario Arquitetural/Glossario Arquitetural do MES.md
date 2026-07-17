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

Posicao fisica ou logica onde materiais podem ser armazenados, movimentados, reservados ou bloqueados.

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

