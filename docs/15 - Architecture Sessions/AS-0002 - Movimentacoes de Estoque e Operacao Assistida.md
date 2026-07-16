# AS-0002 - Movimentacoes de Estoque e Operacao Assistida

## 1. Identificacao da Sessao

| Campo | Valor |
|---|---|
| Codigo | AS-0002 |
| Titulo | Movimentacoes de Estoque e Operacao Assistida |
| Status | Aprovada |
| Data da sessao | 2026-07-16 |
| Data da ultima revisao | 2026-07-16 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Sessao de arquitetura realizada antes da implementacao das alteracoes. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- ChatGPT, no papel de apoio arquitetural.
- Codex, no papel de documentador tecnico.

## 2. Objetivo

A sessao teve como objetivo definir a arquitetura de movimentacoes de estoque e operacao assistida, incluindo:

- entrada de estoque;
- saida de estoque;
- transferencia interna;
- transferencia entre armazens;
- material em transito;
- cancelamentos;
- divergencias;
- auditoria;
- historico imutavel;
- operacao assistida;
- tarefas operacionais;
- Jornada do Material;
- Jornada do Operador;
- distribuicao por usuario, Role e Equipe Operacional.

## 3. Principios Arquiteturais

### 3.1 Operacao Guiada

O sistema deve conduzir o operador passo a passo, validando cada etapa e apresentando sempre a proxima acao esperada.

### 3.2 Dificil de Errar, Facil de Operar e Completo para Auditar

A experiencia operacional deve reduzir erros, simplificar treinamento e preservar rastreabilidade.

### 3.3 Historico Imutavel

Movimentacoes concluidas nao podem ser alteradas ou excluidas.

Correcoes devem ocorrer por estorno, reversao ou nova movimentacao relacionada.

### 3.4 Jornada do Material

Todo evento relevante deve contribuir para reconstruir a vida completa do material, lote ou unidade logistica.

### 3.5 Jornada do Operador

O sistema deve registrar as tarefas recebidas, aceitas, executadas, pausadas, divergentes e concluidas por cada operador.

### 3.6 Parametrizacao

Fluxos, alertas, tarefas, prioridades, prazos e validacoes devem ser parametrizaveis sempre que possivel.

### 3.7 Evolucao Gradual

A primeira versao utilizara regras parametrizadas e operacao assistida, ficando preparada para automacao, AMRs, robos e IA futuramente.

## 4. Tipos de Movimentacao

### 4.1 Entrada de Estoque

Origens possiveis:

- recebimento de fornecedor;
- devolucao de producao;
- devolucao de cliente;
- ajuste positivo;
- producao concluida;
- transferencia de outro armazem.

Fluxo aprovado:

```text
Documento de origem
-> identificacao do material
-> validacao de lote/serial
-> area de recebimento ou staging
-> conferencia ou inspecao, quando aplicavel
-> armazenagem definitiva
```

O material podera permanecer em staging antes de entrar no estoque disponivel.

### 4.2 Saida de Estoque

Motivos possiveis:

- consumo de producao;
- expedicao;
- transferencia;
- devolucao a fornecedor;
- ajuste negativo;
- sucateamento;
- amostra de qualidade.

Toda saida devera registrar:

- motivo;
- documento de origem;
- operador;
- data e hora;
- localizacao;
- lote;
- serial, quando aplicavel;
- quantidade;
- saldo anterior;
- saldo posterior.

### 4.3 Transferencia Interna em Duas Etapas

Fluxo aprovado:

```text
Origem
-> retirada confirmada
-> em transito
-> destino informado
-> armazenagem confirmada
```

Estados aprovados:

- Pendente
- Em Separacao
- Retirada Confirmada
- Em Transito
- Armazenada
- Cancelada
- Com Divergencia

Cada etapa devera registrar seu proprio responsavel.

### 4.4 Transferencia entre Armazens

Fluxo aprovado:

```text
Armazem de origem
-> expedicao interna
-> em transito
-> recebimento no armazem de destino
-> armazenagem definitiva
```

A transferencia somente sera concluida apos confirmacao do destino.

### 4.5 Material em Transito

Regras aprovadas:

- nao fica disponivel na origem;
- ainda nao fica disponivel no destino;
- permanece rastreavel;
- aparece em painel de pendencias;
- gera alerta quando ultrapassar o tempo esperado;
- mantem responsavel, origem, quantidade, data e hora;
- permite identificar materiais retirados e ainda nao armazenados.

## 5. Eventos da Movimentacao

Uma movimentacao nao sera representada apenas por um status atual. Ela possuira uma linha do tempo de eventos.

Exemplos de eventos:

- Solicitada
- Atribuida
- Aceita
- Separacao iniciada
- Retirada confirmada
- Em transito
- Destino informado
- Armazenagem confirmada
- Concluida
- Cancelada
- Com divergencia
- Corrigida

Cada evento devera registrar, quando aplicavel:

- operador;
- data;
- hora;
- dispositivo;
- origem;
- destino;
- produto;
- lote;
- serial;
- quantidade;
- documento relacionado;
- observacao;
- tempo gasto;
- geolocalizacao, apenas se futuramente aplicavel;
- motivo de excecao.

Nao fica definida nesta sessao a entidade fisica definitiva para eventos de movimentacao.

## 6. Cancelamentos e Correcoes

Decisoes aprovadas:

- nunca apagar movimentacoes concluidas;
- nunca editar diretamente o historico concluido;
- cancelamentos geram movimentacao corretiva;
- estornos devem manter vinculo com a movimentacao original;
- motivo e obrigatorio;
- usuario e data/hora sao obrigatorios;
- o historico deve permitir reconstruir o ocorrido.

## 7. Divergencias

### 7.1 Divergencia de Quantidade

Quando a quantidade encontrada for diferente da esperada:

- registrar quantidade esperada;
- registrar quantidade encontrada;
- registrar motivo;
- permitir foto opcional;
- permitir observacao;
- marcar como Com divergencia;
- encaminhar para decisao de supervisor;
- permitir aprovacao, ajuste ou recontagem.

### 7.2 Divergencia de Localizacao

Quando o material for encontrado em local diferente:

- registrar a localizacao esperada;
- registrar a localizacao encontrada;
- registrar ocorrencia;
- manter rastreabilidade;
- sugerir correcao do endereco;
- permitir continuidade mediante autorizacao, quando aplicavel;
- preservar a divergencia no historico.

## 8. Historico Imutavel e Auditoria

Decisoes aprovadas:

- nenhuma movimentacao concluida podera ser excluida;
- nenhuma movimentacao concluida podera ser editada;
- correcoes ocorrem por novos eventos ou movimentos relacionados;
- cada etapa podera ter operadores diferentes;
- o sistema devera registrar quem retirou, transportou, armazenou, aprovou ou corrigiu;
- o dispositivo utilizado devera ser registrado quando disponivel;
- o historico deve permitir auditoria completa.

## 9. Jornada do Material

A Jornada do Material e um conceito funcional central. Ela devera apresentar uma linha do tempo contendo, conforme o processo:

- recebido;
- conferido;
- inspecionado;
- aprovado ou rejeitado;
- armazenado;
- reservado;
- bloqueado;
- transferido;
- colocado em transito;
- consumido;
- transformado;
- produzido;
- reclassificado;
- expedido;
- devolvido;
- ajustado;
- inventariado.

A jornada devera conectar estoque, qualidade, producao, rastreabilidade, expedicao, documentos, operadores, locais, lotes e ordens de producao.

Nao fica definida nesta sessao a entidade fisica definitiva da Jornada.

## 10. Jornada do Operador

A Jornada do Operador e um conceito funcional central. O operador devera receber uma lista de tarefas relacionadas ao seu contexto operacional.

Exemplos:

- receber nota fiscal;
- conferir lote;
- armazenar material;
- transferir big bags;
- abastecer producao;
- separar ordem;
- realizar inventario;
- tratar divergencia.

A jornada devera registrar tarefas recebidas, aceitas, executadas, pausadas, reatribuidas, divergencias, tempos, produtividade e conclusao.

## 11. Operacao Assistida

Fluxo guiado aprovado para exemplo de transferencia:

1. Ler QR Code da origem.
2. Validar origem.
3. Ler o material.
4. Validar produto, lote e serial.
5. Confirmar quantidade.
6. Confirmar retirada.
7. Colocar material em transito.
8. Ler destino.
9. Validar capacidade e compatibilidade.
10. Confirmar armazenagem.

O sistema deve impedir ou alertar antes da confirmacao quando houver inconsistencia.

## 12. Modos de Operacao

### 12.1 Assistido

O sistema conduz todas as etapas. Aplicavel principalmente a operadores, coletores e processos criticos.

### 12.2 Livre

Usuarios autorizados podem selecionar dados e acoes com maior flexibilidade. Aplicavel a supervisores, logistica e inventario extraordinario.

O modo livre nao elimina auditoria nem validacoes obrigatorias.

### 12.3 Automatizado

Evolucao futura para AMRs, AGVs, robos, integracoes automaticas, IA e equipamentos autonomos.

## 13. Validacoes em Tempo Real

Validacoes aprovadas:

- localizacao;
- produto;
- lote;
- serial;
- quantidade;
- ordem de producao;
- capacidade;
- compatibilidade;
- status da localizacao;
- material bloqueado;
- reserva;
- validade;
- permissao do operador;
- Role;
- Equipe Operacional;
- documento de origem;
- saldo disponivel;
- condicao de transito;
- regras parametrizadas.

## 14. Tecnologias de Identificacao

Meios suportados ou planejados:

- QR Code;
- codigo de barras;
- selecao manual, mediante autorizacao;
- RFID, como evolucao futura;
- NFC, como evolucao futura.

A arquitetura nao devera depender de uma unica tecnologia de leitura.

## 15. Alertas Operacionais

Na primeira versao, alertas serao baseados em regras parametrizadas.

Exemplos:

- localizacao com ocupacao elevada;
- lote proximo do vencimento;
- localizacao mais proxima disponivel;
- material reservado para outra ordem;
- material bloqueado;
- transferencia em atraso;
- material em transito por tempo superior ao esperado;
- incompatibilidade de localizacao;
- falta de capacidade;
- divergencia de quantidade;
- divergencia de localizacao.

A arquitetura devera ficar preparada para alertas preditivos futuros.

## 16. Arquitetura Baseada em Tarefas

Decisao central: o operador nao devera executar diretamente acoes isoladas de menu como principal forma de operacao. O operador executara tarefas.

Fluxo conceitual:

```text
Evento de negocio
-> regra de geracao
-> tarefa
-> etapas guiadas
-> eventos operacionais
-> movimentacoes de estoque
-> Jornada do Material
-> Jornada do Operador
-> auditoria
```

## 17. Origem das Tarefas

### 17.1 Tarefas Automaticas

Geradas por eventos do processo, como recebimento aprovado, ordem de producao liberada, material retirado, inventario programado, localizacao em desativacao, divergencia registrada ou abastecimento abaixo do minimo.

### 17.2 Tarefas Parametrizadas pelo Gestor

O gestor configura modelos e regras de geracao, nao cada tarefa individual.

O modelo podera definir evento de origem, tipo de tarefa, prioridade, prazo, Role necessaria, equipe responsavel, etapas, instrucoes, validacoes, alertas e escalonamento.

### 17.3 Tarefas Manuais

Permitidas para excecoes controladas, como reorganizacao, conferencia extraordinaria, retirada de material avariado, contagem extraordinaria, transferencia manual ou acao corretiva.

Devem exigir motivo e auditoria.

## 18. Estados da Tarefa

Estados sugeridos e aprovados:

- Pendente
- Atribuida
- Aceita
- Em Execucao
- Pausada
- Aguardando Validacao
- Concluida
- Cancelada
- Com Divergencia

O modelo definitivo podera ser refinado em sessao futura, sem alterar o principio aprovado.

## 19. Distribuicao de Tarefas

Modos aprovados:

- usuario especifico;
- Role;
- Equipe Operacional;
- Equipe Operacional mais Role obrigatoria.

Exemplo:

```text
Tarefa: Armazenar lote
Equipe: Almoxarifado MP - Turno A
Role obrigatoria: OperadorEstoque
```

## 20. ASP.NET Identity

Decisao aprovada:

- ASP.NET Identity continuara responsavel por autenticacao;
- usuarios;
- Role unica;
- autorizacao;
- permissoes;
- menus dinamicos;
- acesso as acoes.

Nao sera criada outra solucao de autenticacao ou autorizacao.

## 21. Equipes Operacionais

Equipes Operacionais sao conceito complementar ao Identity.

A Role responde: o usuario tem permissao para executar?

A Equipe Operacional responde: para qual grupo de trabalho a tarefa deve ser enviada?

Decisoes aprovadas:

- um usuario podera pertencer a uma ou mais equipes;
- a equipe nao substitui a Role;
- tarefas poderao exigir equipe e Role;
- uma equipe inativa nao recebe novas tarefas;
- remocao do usuario da equipe nao apaga historico;
- tarefa ja aceita permanece vinculada ao operador;
- menus continuam controlados por Role;
- equipes filtram o contexto operacional.

Nao fica definida nesta sessao a entidade definitiva de Equipe Operacional.

## 22. Impactos Funcionais

Impactos futuros:

- recebimento;
- estoque;
- transferencias;
- inventario;
- producao;
- qualidade;
- expedicao;
- rastreabilidade;
- tarefas;
- usuarios;
- roles;
- equipes;
- paineis;
- coletores;
- alertas;
- auditoria;
- relatorios.

Estes impactos nao declaram que os itens ja foram implementados.

## 23. Impactos Tecnicos

Necessidades futuras:

- modelo de tarefa;
- modelo de evento da tarefa;
- modelo de evento da movimentacao;
- vinculo com usuario e Identity;
- vinculo com Role;
- conceito de equipe operacional;
- historico imutavel;
- estorno e correcao;
- material em transito;
- painel de pendencias;
- SLA e alertas;
- suporte a leitura;
- linha do tempo;
- Jornada do Material;
- Jornada do Operador.

Nao ficam definidas tabelas ou propriedades definitivas.

## 24. Riscos

- tarefas duplicadas;
- tarefa aceita por mais de um operador;
- perda de rastreabilidade;
- movimentacao sem documento;
- material esquecido em transito;
- cancelamento incorreto;
- correcao sem vinculo;
- uso excessivo do modo livre;
- equipe ou Role incorreta;
- alertas excessivos;
- telas complexas;
- indisponibilidade de coletor;
- perda de conexao durante a operacao;
- divergencia entre saldo e eventos;
- excesso de parametrizacao sem governanca.

## 25. Decisoes Deliberadamente Adiadas

Pendencias futuras:

- modelo fisico definitivo de Tarefa;
- modelo fisico definitivo de EventoTarefa;
- modelo fisico definitivo de EventoMovimentacao;
- modelo definitivo de EquipeOperacional;
- modelo de associacao usuario-equipe;
- modelo definitivo de movimentacao em transito;
- estrategia offline dos coletores;
- tratamento de concorrencia;
- lock de tarefas;
- SLA e escalonamento;
- prioridade automatica;
- regras completas de reatribuicao;
- integracao com notificacoes;
- integracao com AMR/AGV;
- modelo de IA;
- desenho final das telas;
- migrations;
- endpoints;
- politicas completas de autorizacao.

## 26. Decision Logs Derivados

- DL-0006 - Movimentacoes de Estoque em Etapas e Material em Transito
- DL-0007 - Historico Imutavel, Eventos e Correcoes de Estoque
- DL-0008 - Operacao Assistida e Validacoes em Tempo Real
- DL-0009 - Jornada do Material e Jornada do Operador
- DL-0010 - Arquitetura de Tarefas Operacionais
- DL-0011 - Distribuicao de Tarefas por Identity, Role e Equipe Operacional

## 27. Criterios de Encerramento

A sessao foi concluida porque foram aprovadas decisoes sobre:

- entrada;
- saida;
- transferencias;
- material em transito;
- divergencias;
- cancelamentos;
- auditoria;
- historico imutavel;
- operacao assistida;
- modos de operacao;
- validacoes;
- tecnologias de leitura;
- alertas;
- tarefas;
- Jornada do Material;
- Jornada do Operador;
- Identity;
- Roles;
- Equipes Operacionais.

## 28. Proximos Passos

1. Revisar a AS-0002.
2. Revisar os Decision Logs derivados.
3. Validar se nenhuma decisao foi inventada.
4. Fazer commit documental apos aprovacao.
5. Atualizar futuramente AI_CONTEXT, Roadmap e Backlog.
6. Nao implementar codigo antes de uma definicao tecnica aprovada.
7. Iniciar a proxima Architecture Session somente apos concluir a revisao documental.
