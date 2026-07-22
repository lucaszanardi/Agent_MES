# AS-0004 - Arquitetura do Dominio de Estoque

## 1. Identificacao

| Campo | Valor |
|---|---|
| Codigo | AS-0004 |
| Titulo | Arquitetura do Dominio de Estoque |
| Status | Aprovada |
| Data da sessao | 2026-07-22 |
| Data da ultima revisao | 2026-07-22 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Domain Discovery do dominio de Estoque realizado antes de qualquer implementacao. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- ChatGPT, no papel de apoio arquitetural.
- Codex, no papel de documentador tecnico.

## 2. Contexto

O produto Industria 4.0 e um MES/MOM comercial, ERP-agnostico, voltado principalmente para pequenas e medias industrias brasileiras.

Os modulos iniciais planejados sao:

- estoque e logistica interna;
- gestao da producao;
- apontamento de producao.

A AS-0004 consolida o Domain Discovery do dominio de Estoque e logistica interna. O escopo evoluiu em relacao ao planejamento anterior: nao se limita ao Recebimento de Materiais.

## 3. Problema

O estoque nao pode ser tratado apenas como cadastro, saldo administrativo ou conjunto de telas de entrada e saida.

O sistema precisa representar os processos fisicos da fabrica, preservar rastreabilidade, impedir edicao direta de saldo e manter aderencia entre estoque fisico e virtual.

## 4. Objetivos

- Definir Estoque como dominio funcional.
- Definir os Aggregate Roots iniciais do dominio.
- Separar processos operacionais de projecoes de saldo.
- Definir Unidade Logistica como principal agregado fisico.
- Definir ciclos, estados, eventos e invariantes fundamentais.
- Registrar limites para implementacao futura.

## 5. Escopo

Esta sessao cobre:

- ExpectativaDeRecebimento;
- Recebimento;
- UnidadeLogistica;
- LocalDeEstoque;
- MovimentacaoDeEstoque;
- ReservaDeEstoque;
- PoliticaDeContagem;
- Inventario;
- saldo como projecao derivada;
- autorizacao, auditoria, eventos, consistencia, concorrencia e experiencia operacional futura.

## 6. Fora de Escopo

- implementacao;
- migrations;
- definicao definitiva de tabelas;
- endpoints REST;
- contratos GraphQL;
- componentes frontend;
- envio de e-mail;
- autenticacao;
- login;
- recuperacao de senha;
- implementacao de MRP;
- sequenciamento;
- apontamento de producao;
- integracao completa com ERP;
- escolha final de message broker;
- escolha final de banco temporal;
- definicao final de infraestrutura;
- codigo de coletores;
- integracao com RFID;
- regras definitivas de qualidade;
- expedicao completa;
- faturamento;
- fiscal.

## 7. Premissas

- A metodologia do projeto permanece Discussao -> Architecture Session -> Decision Logs -> Implementacao -> Atualizacao do Project Book.
- Nenhuma implementacao foi iniciada por esta sessao.
- O dominio deve permanecer ERP-agnostico.
- O sistema deve ser parametrizavel sem violar invariantes fundamentais.
- AS-0001, AS-0002 e AS-0003 permanecem preservadas.

## 8. Principios Arquiteturais

1. Estoque e um dominio, nao um Aggregate Root.
2. O sistema deve representar os processos fisicos da fabrica, e nao apenas seus resultados administrativos.
3. O sistema deve priorizar a aderencia entre estoque fisico e virtual.
4. Todas as alteracoes relevantes de estoque devem ocorrer como transacoes operacionais simples, confirmadas, rastreaveis e auditaveis.
5. O estoque virtual deve ser consequencia dos fatos operacionais confirmados.
6. O estoque nao podera ser alterado por edicao direta de saldo, localizacao, disponibilidade, quantidade ou condicao operacional.
7. Nenhuma alteracao relevante do estoque devera ocorrer de forma instantanea quando, no mundo fisico, ela representa um processo operacional.
8. O dominio pode possuir alta sofisticacao interna, mas a interface deve ser simples e orientada as tarefas do operador.
9. Conceitos permanentes pertencem ao dominio; fluxos e variacoes operacionais pertencem a politicas configuraveis.
10. O produto deve ser altamente parametrizavel, sem comprometer invariantes fundamentais do dominio.
11. Autorizacao de usuario e autoridade de dominio sao conceitos distintos.
12. O administrador pode configurar permissoes, mas nao deve existir edicao administrativa direta do estado interno de uma Unidade Logistica.

## 9. Linguagem Ubiqua

Termos centrais:

- Dominio de Estoque;
- Aggregate Root;
- Unidade Logistica;
- Tipo de Unidade Logistica;
- Instancia de Unidade Logistica;
- Local de Estoque;
- LocalizacaoEstoque;
- Expectativa de Recebimento;
- Origem Operacional;
- Recebimento;
- Custodia;
- Disponibilidade;
- Movimentacao de Estoque;
- Reserva de Estoque;
- Politica de Contagem;
- Inventario;
- Contagem;
- Recontagem;
- Reconciliacao;
- Ajuste de Estoque;
- Projecao de Saldo;
- Evento de Dominio;
- Comando;
- Correlacao;
- Causacao;
- Idempotencia.

## 10. Visao do Dominio

Estoque representa um dominio ou bounded context funcional.

Nao sera criado um Aggregate Root denominado `Estoque`.

Aggregate Roots iniciais:

- `ExpectativaDeRecebimento`;
- `Recebimento`;
- `UnidadeLogistica`;
- `LocalDeEstoque`;
- `MovimentacaoDeEstoque`;
- `ReservaDeEstoque`;
- `PoliticaDeContagem`;
- `Inventario`.

O saldo de estoque sera tratado como projecao derivada de fatos operacionais confirmados.

Exemplos de projecoes:

- saldo por material;
- saldo por lote;
- saldo por localizacao;
- saldo fisico;
- saldo disponivel;
- saldo reservado;
- saldo bloqueado;
- saldo em movimentacao;
- saldo em consumo;
- saldo por condicao operacional.

Essas projecoes nao sao fonte primaria de verdade para alteracoes operacionais.

## 11. Fronteiras dos Agregados

Cada Aggregate Root protege suas invariantes locais e se coordena com outros agregados por processos de dominio, eventos, orquestracao ou consistencia eventual controlada.

Processos entre agregados nao devem depender de uma transacao distribuida global.

## 12. Responsabilidades dos Agregados

`ExpectativaDeRecebimento`: representa algo que o dominio preve receber.

`Recebimento`: conduz o processo operacional de entrada fisica.

`UnidadeLogistica`: representa o objeto fisico identificavel, manipulavel e rastreavel.

`LocalDeEstoque`: representa onde a UL pode estar armazenada, bloqueada ou movimentada.

`MovimentacaoDeEstoque`: controla processo operacional com inicio, transito e fim.

`ReservaDeEstoque`: coordena alocacao para atendimento de demanda.

`PoliticaDeContagem`: define regras configuraveis para gerar inventarios ou tarefas.

`Inventario`: executa a contagem operacional, reconciliacao e solicitacao de ajuste.

## 13. Invariantes

- Uma UL nao pode participar de duas movimentacoes ativas simultaneamente.
- Uma quantidade nao pode ser alocada para duas reservas distintas alem de sua disponibilidade.
- Local bloqueado nao recebe UL.
- Local desativado nao participa de novas operacoes.
- Capacidade do local nao pode ser excedida.
- Materiais ou tipos de UL incompativeis nao podem ser armazenados.
- Restricoes ambientais devem ser respeitadas.
- Saldo projetado nao pode ser usado como unica protecao contra concorrencia.
- Ajustes de estoque devem ser formais, autorizados e auditaveis.
- Unidades Logisticas historicas nunca devem ser apagadas.

## 14. Ciclos de Vida

Os ciclos de vida devem representar processos operacionais reais.

Nenhuma alteracao relevante deve ocorrer por edicao direta quando representar uma etapa fisica no mundo real.

## 15. Estados

Estados devem ser especificos por agregado. Nao deve haver um unico campo generico de status que misture existencia, condicao operacional, localizacao, custodia e conteudo.

## 16. Processos Operacionais

Processos operacionais relevantes devem ter inicio, confirmacoes intermediarias quando aplicavel, conclusao, eventos, auditoria, divergencias e rastreabilidade.

## 17. Unidade Logistica

A Unidade Logistica representa um objeto fisico identificavel, manipulavel e rastreavel, que contem ou representa uma determinada quantidade de material.

Pode representar:

- pallet;
- caixa;
- tambor;
- bobina;
- big bag;
- rack;
- recipiente;
- conteiner;
- peca unitaria;
- Unidade Logistica virtual.

A Unidade Logistica nao e material, lote, localizacao, saldo, recebimento ou documento fiscal.

Ela possui identidade propria e e o principal agregado fisico do dominio de Estoque.

Dimensoes independentes:

1. Estado existencial.
2. Condicao operacional.
3. Localizacao.
4. Custodia.
5. Estrutura logistica e conteudo.

Estados existenciais minimos:

- Ativa;
- Encerrada;
- Invalidada.

Motivos de encerramento possiveis:

- consumida;
- consolidada;
- dividida;
- expedida;
- devolvida;
- destruida;
- descartada;
- outro.

Tipo de Unidade Logistica e cadastro mestre. Instancia de Unidade Logistica e o objeto operacional identificado, por exemplo `UL-000123`.

Uma UL pode nascer durante recebimento, existir previamente, nascer por divisao, nascer por consolidacao, ser reutilizavel, permanecer existindo vazia ou representar embalagem retornavel.

A homogeneidade sera governada por politicas configuraveis. A politica podera permitir ou impedir multiplos materiais, lotes, validades, series, condicoes de qualidade ou caracteristicas configuradas.

A hierarquia de UL sera opcional e configuravel. Movimentar uma UL pai podera movimentar suas ULs filhas, respeitando politicas e regras aplicaveis.

Genealogia deve ser registrada em agrupamentos, desagrupamentos, divisoes e consolidacoes.

## 18. Local de Estoque

Local de Estoque responde a pergunta: onde esta?

Unidade Logistica responde a pergunta: o que esta sendo movimentado?

Modelo conceitual:

```text
Local de Estoque
-> contem
Unidade Logistica
-> contem ou representa
Material
```

Modelo conceitual complementar:

```text
LocalDeEstoque = endereco ou espaco fisico governado pelo dominio
LocalizacaoEstoque = referencia de posicionamento atual da Unidade Logistica
```

`LocalDeEstoque` sera Aggregate Root e tambem possuira caracteristicas de cadastro mestre.

O Local de Estoque possui identidade, hierarquia, capacidade, restricoes, tipo, compatibilidade e estado operacional.

`LocalizacaoEstoque` representa a referencia de posicionamento atual de uma Unidade Logistica. Ela nao e Aggregate Root concorrente, referencia um `LocalDeEstoque`, responde onde a UL esta posicionada em determinado momento e podera futuramente ser modelada como Value Object ou conceito equivalente.

Esta distincao preserva compatibilidade conceitual com AS-0001 e DL-0001, sem tratar `LocalDeEstoque` e `LocalizacaoEstoque` como sinonimos.

Responsabilidades:

- identidade do endereco;
- hierarquia fisica;
- tipo;
- estado operacional;
- capacidade;
- restricoes;
- compatibilidade com materiais;
- compatibilidade com tipos de UL;
- condicoes ambientais;
- bloqueio;
- desativacao.

Nao armazenar dentro do agregado `LocalDeEstoque` uma colecao completa e ilimitada de todas as ULs existentes no local.

Ocupacao e conteudo do local devem ser apresentados por projecoes ou consultas especializadas.

Invariantes proprias:

- local desativado nao recebe novas operacoes;
- local bloqueado nao recebe UL, salvo politica excepcional explicita;
- capacidade nao pode ser excedida;
- restricoes de material e tipo de UL devem ser respeitadas;
- hierarquia nao pode formar ciclos;
- um local nao pode ser descendente de si mesmo;
- alteracoes estruturais devem preservar rastreabilidade;
- ocupacao e consultada por projecao e nao por colecao ilimitada interna ao agregado.

## 19. Recebimento

Recebimento e um processo operacional.

Nao e um documento, nao pertence ao ERP e possui ciclo de vida, estados, regras, eventos, tarefas e divergencias.

Estados principais:

- Rascunho;
- Planejado;
- Em Recebimento;
- Em Conferencia;
- Aguardando Destinacao;
- Concluido.

Estados ou situacoes excepcionais:

- Suspenso;
- Cancelado;
- Concluido com Divergencia.

Responsabilidades:

- iniciar a entrada fisica;
- realizar conferencia;
- registrar divergencias;
- estabelecer custodia inicial;
- criar ou associar Unidades Logisticas;
- direcionar as Unidades Logisticas;
- concluir o processo.

Portaria deve ser opcional.

Fluxo conceitual:

```text
Origem Operacional
-> Expectativa opcional
-> Recebimento
-> Unidade Logistica
-> Custodia
-> Disponibilidade
-> Consumo
```

Custodia e disponibilidade sao conceitos diferentes.

## 20. Expectativa de Recebimento

A Expectativa de Recebimento representa algo que o dominio preve receber, independentemente de sua origem tecnologica.

Ela pode ser criada manualmente, originada de ERP, originada de outro sistema, originada de transferencia, alterada antes da chegada, parcialmente atendida, atendida por multiplos recebimentos, cancelada, expirar ou permanecer pendente.

A expectativa deve ser independente do Recebimento.

Um Recebimento pode existir sem Expectativa. Uma Expectativa pode existir antes de qualquer Recebimento.

Origem Operacional pode representar:

- Compra;
- Transferencia;
- Industrializacao;
- Devolucao;
- Ajuste;
- Avulso;
- Outro Sistema.

O dominio deve evitar acoplamento direto ao conceito de Pedido de Compra.

## 21. Movimentacao de Estoque

Movimentacao de Estoque e um processo operacional e um Aggregate Root proprio.

Nao e somente um evento.

Toda movimentacao devera possuir inicio e fim explicitos.

Ciclo principal sugerido:

- Criada;
- Planejada;
- Aguardando Retirada;
- Retirada Confirmada;
- Em Transito;
- Aguardando Confirmacao;
- Concluida.

Estados ou situacoes excepcionais:

- Suspensa;
- Retomada;
- Cancelada;
- Expirada;
- Atrasada;
- Divergente.

Toda retirada sem conclusao deve permanecer visivel no sistema. Movimentacoes em aberto devem ser monitoradas.

Durante a movimentacao:

- a UL nao estara disponivel na origem;
- a UL ainda nao estara confirmada no destino;
- a UL estara em condicao explicita de transito;
- a UL nao podera participar de duas movimentacoes simultaneas;
- uma segunda movimentacao devera ser bloqueada;
- consumo devera obedecer politica especifica;
- a rastreabilidade devera ser preservada.

A localizacao final somente sera alterada apos confirmacao fisica de chegada.

## 22. Reserva e Disponibilidade

`ReservaDeEstoque` sera Aggregate Root proprio.

A reserva representa o processo de atendimento de uma demanda.

Origens possiveis:

- Ordem de Producao;
- necessidade de abastecimento;
- transferencia;
- expedicao;
- outra demanda operacional.

A reserva podera envolver multiplas ULs, ser parcial, total, expirar, ser realocada, liberada, cancelada ou consumida.

Estrutura conceitual:

- demanda;
- material;
- quantidade requerida;
- quantidade alocada;
- quantidade pendente;
- prioridade;
- validade;
- estado;
- alocacoes por UL.

A Reserva coordena a alocacao. A Unidade Logistica protege sua quantidade fisica e alocavel.

A reserva nao podera ultrapassar a disponibilidade real das ULs.

Devem ser distinguidas:

- quantidade fisica;
- quantidade disponivel;
- quantidade reservada;
- quantidade bloqueada;
- quantidade em movimentacao;
- quantidade em consumo.

## 23. Politica de Contagem

`PoliticaDeContagem` sera Aggregate Root proprio e configuravel.

Ela devera permitir regras baseadas em classificacao ABC, criticidade, valor financeiro, risco de parada, historico de divergencia, tipo de material, localizacao, lote, validade, frequencia de movimentacao, requisito regulatorio, frequencia temporal, tolerancia, dupla contagem e necessidade de aprovacao.

A politica devera gerar Inventarios ou tarefas de contagem conforme configuracao.

Nao embutir frequencias fixas no codigo.

`PoliticaDeContagem` possui identidade, versao, vigencia, estado ativo ou inativo, criterios de selecao, frequencia, escopo, tolerancias, regras de recontagem, regras de aprovacao e ciclo de vida proprio.

Invariantes minimas:

- somente versoes ativas e vigentes geram Inventarios;
- alteracoes relevantes geram nova versao ou preservam historico equivalente;
- criterios nao podem produzir uma politica invalida ou sem escopo interpretavel;
- frequencia e tolerancias devem respeitar parametros validos;
- desativacao nao deve apagar Inventarios ja gerados;
- alteracao da politica nao modifica retroativamente execucoes ja iniciadas.

## 24. Inventario

Inventario representa a execucao operacional de uma contagem.

Politica de Contagem e Inventario sao agregados diferentes.

Inventario devera possuir identidade, escopo, data, locais, ULs, materiais, tarefas de contagem, responsaveis, resultados, divergencias, recontagens, reconciliacao, solicitacao de ajuste e encerramento.

Ciclo sugerido:

- Planejado;
- Liberado;
- Em Contagem;
- Em Reconciliacao;
- Concluido.

Estados excepcionais:

- Suspenso;
- Cancelado;
- Divergente.

Para a primeira versao, `TarefaDeContagem` permanecera como entidade interna do agregado Inventario.

Essa decisao podera ser revista futuramente caso exista necessidade de milhares de tarefas simultaneas, execucao distribuida, multiplos coletores, alta concorrencia ou escalabilidade independente.

## 25. Ajustes

Contagem fisica, reconciliacao e ajuste de estoque sao conceitos diferentes.

```text
Contagem fisica
!= Reconciliacao
!= Ajuste de estoque
```

A contagem registra o que foi encontrado. A reconciliacao compara fisico e virtual. O ajuste e uma operacao posterior, autorizada e auditavel.

Ajustes deverao registrar valor anterior, valor contado, diferenca, justificativa, usuario executor, usuario aprovador, data e hora, UL, local, material, correlacao e origem da solicitacao.

## 26. Autorizacoes

Permissoes de usuario deverao ser parametrizaveis.

O administrador podera configurar usuarios, papeis, permissoes, escopo por planta, escopo por armazem, acoes permitidas, politicas de aprovacao e segregacao de funcoes.

Autorizacao de usuario nao permite edicao direta do estado interno do dominio.

Separar:

1. Quem pode solicitar a operacao.
2. Qual processo de dominio pode efetivar a alteracao.

Nenhum usuario, incluindo administrador, podera realizar edicao generica direta da UL.

## 27. Auditoria

Todas as operacoes relevantes deverao registrar:

- usuario executor;
- usuario aprovador, quando aplicavel;
- data e hora;
- planta;
- estacao;
- dispositivo;
- operacao;
- estado anterior;
- estado posterior;
- motivo;
- correlacao;
- origem da solicitacao;
- resultado;
- divergencia;
- justificativa.

Para operacoes criticas, prever de forma configuravel segregacao entre executor e aprovador, justificativa obrigatoria, assinatura eletronica e aprovacao em multiplas etapas.

## 28. Modelo de Eventos

Eventos representam fatos ocorridos no passado. Comandos representam intencoes.

Exemplos:

```text
PlanejarMovimentacao -> comando
MovimentacaoPlanejada -> evento
ConcluirMovimentacao -> comando
MovimentacaoConcluida -> evento
RegistrarContagem -> comando
ContagemRegistrada -> evento
```

Comandos, eventos, estados e operacoes nao devem ser misturados.

## 29. Envelope Comum

Todo evento devera possuir, no minimo:

- `eventId`;
- `eventType`;
- `eventVersion`;
- `occurredAt`;
- `aggregateId`;
- `aggregateType`;
- `correlationId`;
- `causationId`;
- `tenantId`;
- `plantId`;
- `actorId`;
- `source`.

Campos especificos pertencem ao payload de cada evento.

Regras de evolucao:

- novos campos opcionais podem ser adicionados;
- significado de campos existentes nao deve mudar silenciosamente;
- mudancas incompativeis exigem nova versao do evento;
- consumidores devem suportar versionamento;
- eventos devem ser idempotentes;
- publicacoes devem permitir correlacao e rastreabilidade.

## 30. Catalogo Inicial de Eventos

### Unidade Logistica

- UnidadeLogisticaCriada
- IdentificacaoDaUnidadeLogisticaAtribuida
- ConteudoDaUnidadeLogisticaAlterado
- UnidadeLogisticaBloqueada
- UnidadeLogisticaLiberada
- UnidadeLogisticaDividida
- UnidadesLogisticasConsolidadas
- UnidadeLogisticaAgrupada
- UnidadeLogisticaDesagrupada
- CustodiaDaUnidadeLogisticaAlterada
- UnidadeLogisticaEncerrada
- UnidadeLogisticaInvalidada

### MovimentacaoDeEstoque - Estados

Os estados abaixo pertencem ao ciclo de vida da movimentacao e nao devem ser tratados automaticamente como eventos:

- Criada
- Planejada
- AguardandoRetirada
- RetiradaConfirmada
- EmTransito
- AguardandoConfirmacao
- Concluida
- Suspensa
- Cancelada
- Expirada
- Divergente

### MovimentacaoDeEstoque - Eventos de Dominio

- MovimentacaoPlanejada
- MovimentacaoIniciada
- UnidadeLogisticaRetirada
- TransitoDaMovimentacaoIniciado
- MovimentacaoConcluida
- MovimentacaoSuspensa
- MovimentacaoRetomada
- DivergenciaDeMovimentacaoIdentificada
- MovimentacaoCancelada
- MovimentacaoExpirada

### MovimentacaoDeEstoque - Alertas ou Eventos Operacionais Derivados

- LimiteDeTempoDaMovimentacaoAproximado
- AtrasoDeMovimentacaoDetectado

Alertas derivados podem ser produzidos por monitoramento temporal, projecoes ou servicos operacionais. Eles nao representam necessariamente uma transicao direta do Aggregate Root, devem possuir correlacao com a movimentacao, devem ser idempotentes e podem gerar notificacao, tarefa ou escalonamento conforme politica.

### Recebimento

- RecebimentoPlanejado
- RecebimentoIniciado
- ConferenciaDeRecebimentoIniciada
- MaterialConferido
- DivergenciaDeRecebimentoIdentificada
- UnidadeLogisticaRecebida
- RecebimentoAguardandoDestinacao
- RecebimentoConcluido
- RecebimentoConcluidoComDivergencia
- RecebimentoSuspenso
- RecebimentoCancelado

### Expectativa de Recebimento

- ExpectativaDeRecebimentoCriada
- ExpectativaDeRecebimentoAlterada
- ExpectativaDeRecebimentoAtendidaParcialmente
- ExpectativaDeRecebimentoAtendida
- ExpectativaDeRecebimentoExpirada
- ExpectativaDeRecebimentoCancelada

### Reserva

- ReservaDeEstoqueSolicitada
- ReservaDeEstoqueAlocadaParcialmente
- ReservaDeEstoqueConfirmada
- AlocacaoDeUnidadeLogisticaRealizada
- AlocacaoDeUnidadeLogisticaLiberada
- ReservaDeEstoqueExpirada
- ReservaDeEstoqueCancelada
- ReservaDeEstoqueConsumida

### Inventario

- InventarioPlanejado
- InventarioIniciado
- TarefaDeContagemGerada
- ContagemRegistrada
- DivergenciaDeInventarioIdentificada
- RecontagemSolicitada
- RecontagemRegistrada
- AjusteDeEstoqueSolicitado
- AjusteDeEstoqueAutorizado
- AjusteDeEstoqueRejeitado
- AjusteDeEstoqueAplicado
- InventarioConcluido
- InventarioCancelado

### Local de Estoque

- LocalDeEstoqueCriado
- LocalDeEstoqueAlterado
- LocalDeEstoqueBloqueado
- LocalDeEstoqueLiberado
- CapacidadeDoLocalAlterada
- RestricaoDoLocalAlterada
- LocalDeEstoqueDesativado

### Politica de Contagem

- PoliticaDeContagemCriada
- PoliticaDeContagemAlterada
- PoliticaDeContagemAtivada
- PoliticaDeContagemDesativada
- InventarioGeradoPorPolitica

## 31. Consistencia e Concorrencia

Requisitos arquiteturais:

1. Uma UL nao pode participar de duas movimentacoes ativas simultaneamente.
2. Uma quantidade nao pode ser alocada para duas reservas distintas alem de sua disponibilidade.
3. A confirmacao de movimentacao deve ser idempotente.
4. Eventos e comandos devem possuir identificadores de correlacao.
5. Operacoes criticas devem utilizar controle de concorrencia otimista ou mecanismo equivalente.
6. Processos entre agregados nao devem depender de uma transacao distribuida global.
7. Coordenacao entre agregados devera considerar eventos, orquestracao, consistencia eventual controlada ou mecanismos transacionais locais.
8. Projecoes de saldo nao devem ser usadas como unica protecao contra concorrencia.
9. O agregado UL deve proteger quantidade fisica e alocavel.
10. Operacoes repetidas por falha de rede nao podem duplicar movimentacoes, consumos, reservas ou ajustes.

## 32. Projecoes e Consultas

Projecoes de saldo e ocupacao apoiam leitura, consulta, relatorios, disponibilidade e experiencia operacional.

Elas devem ser derivadas de fatos confirmados e nao devem ser utilizadas como fonte primaria para alterar o estoque.

## 33. Experiencia Operacional

Requisitos para futuro frontend, sem implementacao nesta sessao:

- visao visual do estoque;
- navegacao por local, UL, material, lote e ordem;
- cores para disponibilidade, reserva, bloqueio, transito e divergencia;
- painel de movimentacoes abertas;
- alertas de movimentacao atrasada;
- alertas de UL retirada sem chegada confirmada;
- tarefas objetivas para operadores;
- reducao de digitacao;
- uso preferencial de leitura;
- mensagens de validacao claras;
- identificacao do motivo do bloqueio;
- apresentacao de material em transito para a producao;
- indicacao de origem, destino, operador e tempo estimado.

O backend sempre opera sobre Unidade Logistica. O frontend pode ocultar a UL em operacoes simples por meio de estrategias explicitas, implicitas ou virtuais, mas essa simplificacao nao elimina a existencia da UL no dominio. Operacoes criticas devem preservar identificacao e rastreabilidade, priorizando leitura por codigo de barras, QR Code, RFID ou identificador configuravel. A complexidade do dominio nao deve ser transferida integralmente ao operador.

Centro de Controle Logistico futuro podera apresentar movimentacoes planejadas, em transito, proximas do limite, atrasadas, divergentes e concluidas.

## 34. Riscos

- Tratar saldo projetado como fonte primaria de verdade.
- Permitir edicao administrativa direta de UL.
- Misturar comando, evento, estado e operacao.
- Criar agregado `Estoque` amplo demais.
- Acoplar recebimento a Pedido de Compra ou ERP.
- Tornar a interface excessivamente complexa.
- Ignorar concorrencia em movimentacoes e reservas.
- Ajustar estoque sem processo formal e auditoria.

## 35. Limitacoes

Esta sessao define arquitetura conceitual. Modelos fisicos, APIs, telas, componentes, migrations, contratos e infraestrutura permanecem pendentes.

## 36. Decisoes Adiadas

- Modelagem fisica definitiva dos agregados.
- Estrutura tecnica de eventos e persistencia.
- Estrategia de projecoes.
- UX final do Centro de Controle Logistico.
- Politicas detalhadas de qualidade.
- Regras completas de expedicao.
- Integracoes tecnicas.
- Primeira vertical funcional do estoque.

## 37. Impactos Futuros no Backend

- Definir agregados, entidades internas, value objects e servicos de dominio.
- Definir persistencia e transacoes locais.
- Definir eventos de dominio e envelope.
- Definir projecoes e consultas.
- Definir autorizacoes e auditoria.

Nenhuma dessas alteracoes foi implementada nesta sessao.

## 38. Impactos Futuros no Frontend

- Visao visual de estoque.
- Fluxos orientados a tarefas.
- Paineis de movimentacao.
- Leitura por codigo de barras, QR Code, RFID ou identificadores configuraveis.
- Mensagens de validacao claras.

Nenhuma tela foi implementada nesta sessao.

## 39. Relacao Futura com Gestao da Producao

Gestao da Producao devera consumir conceitos de disponibilidade, reserva, movimentacao, consumo, perdas, lote, UL e apontamento.

O fluxo futuro devera integrar consumo, producao, perdas e estoque sem permitir edicao direta de saldo.

## 40. Criterios de Sucesso

- Estoque tratado como dominio, nao como agregado unico.
- Todos os agregados iniciais registrados.
- Movimentacao tratada como processo com inicio, transito e fim.
- Saldo tratado como projecao derivada.
- Reserva tratada como Aggregate Root.
- Expectativa de Recebimento independente.
- Politica de Contagem e Inventario separados.
- Autoridade de usuario e autoridade de dominio separadas.
- Ajustes auditaveis.
- Eventos com envelope e versionamento.
- Comandos e eventos diferenciados.
- Nenhuma implementacao iniciada.

## 41. Decision Logs Relacionados

- DL-0022 - Estoque como Dominio e Saldo como Projecao
- DL-0023 - Unidade Logistica como Agregado Fisico
- DL-0024 - Local de Estoque como Aggregate Root
- DL-0025 - Movimentacao de Estoque como Processo Operacional
- DL-0026 - Reserva de Estoque Disponibilidade e Concorrencia
- DL-0027 - Expectativa de Recebimento e Recebimento Operacional
- DL-0028 - Politica de Contagem Inventario e Ajuste de Estoque
- DL-0029 - Autoridade de Dominio Auditoria e Eventos

## 42. Conclusao

A AS-0004 consolida a arquitetura conceitual do dominio de Estoque e logistica interna, preservando o principio de que o estoque virtual e consequencia de fatos operacionais confirmados.

Nenhuma implementacao foi iniciada.

## 43. Proximos Passos

1. Revisao humana da AS-0004.
2. Revisao humana dos Decision Logs derivados.
3. Validacao do Project Book.
4. Definicao da primeira vertical funcional do estoque.
5. Arquitetura tecnica de backend.
6. Arquitetura tecnica de frontend.
7. Implementacao somente apos aprovacao formal.
