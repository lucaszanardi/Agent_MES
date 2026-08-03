# DL-0042 - Finalidade de Armazenagem por Localizacao de Estoque Legada

## Codigo

DL-0042

## Status

Aprovado

Status tecnico: decisao arquitetural aprovada; implementacao tecnica pendente; migration pendente; database update nao executado; backend nao alterado; frontend nao alterado; banco nao alterado.

## Data

2026-07-31

## Origem

Sessao de arquitetura registrada em `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0008 - Finalidade Configurada e Classificacao Efetiva dos Locais de Estoque Legados.md`.

## Decisao

Introduzir, no legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`, uma "finalidade configurada" por local com valores `Estrutural` e `Armazenagem`, persistida em nova coluna aditiva `finalidadelocalizacao`, e derivar em runtime a "classificacao efetiva" com valores `ESTRUTURAL`, `ARMAZENA` e `BLOQUEADO`, eliminando o estado `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deixa de ser a origem autoritativa da classificacao de folhas e passa a funcionar apenas como sugestao inicial para novos locais. Funciona apenas como sugestao; nunca como proibicao. Qualquer folha, de qualquer `TipoLocalizacao`, pode ser configurada como `Estrutural` ou como `Armazenagem`.

Esta decisao e uma excecao controlada, aditiva e pontual ao congelamento semantico do legado aprovado em DL-0041 (Estrategia B), restrita a inclusao de uma unica coluna e ao ajuste correspondente da regra de classificacao; nao promove `LocalizacaoEstoque` a Aggregate Root concorrente, nao altera `LocalDeEstoque` da nova vertical e nao cria sincronizacao automatica entre os dois modelos.

## Contexto

A funcionalidade legada de Locais de Estoque classifica cada local em runtime como `ESTRUTURAL`, `ARMAZENA`, `REQUER_FILHO` ou `BLOQUEADO` a partir de tres entradas:

- `LocalizacaoEstoque.bloqueada` (boolean persistido por local);
- existencia de filhos (consulta por `localizacaopaiid`);
- `TipoLocalizacao.permitearmazenagem` (boolean persistido por tipo).

A classificacao nao e persistida; e calculada por `LocalizacaoEstoqueServices.GetFuncaoEstrutura` (arvore) e por replicacao parcial em `LocalizacaoEstoqueConsultaService` (grid paginado), com divergencia semantica entre as duas saidas (a grid nao expoe `REQUER_FILHO` enquanto a arvore expoe). No frontend do mapa a regra e recalculada em `CadlocalizacaoestoqueComponent.getStatusNode/getPermiteArmazenagem/getFuncaoFormularioStatus`.

Constatou-se:

- `LocalizacaoEstoque` nao possui propriedade de finalidade configurada pelo usuario;
- `TipoLocalizacao.permitearmazenagem` e a unica origem persistida que influencia a classificacao de folhas;
- folhas do mesmo tipo sao classificadas de forma identica, impedindo diferenciar, dentro da mesma Area de Estoque, um local como armazenador e outro como estrutural;
- layouts reais com profundidades variaveis dentro da mesma Area exigem criar Areas separadas apenas para acomodar ramos com terminacoes diferentes;
- `REQUER_FILHO` forcava criacao de descendentes mesmo quando o operador desejava encerrar a hierarquia em uma folha estrutural.

## Motivacao

Permitir configurar, dentro da mesma Area de Estoque, ramos com profundidades variaveis e folhas com finalidades distintas (estrutural vs armazenadora), eliminando a rigidez derivada de `TipoLocalizacao.permitearmazenagem` como unica origem de classificacao de folhas, e remover o estado artificial `REQUER_FILHO`.

## Escopo

Incluido:

- nova coluna `finalidadelocalizacao` (smallint, NOT NULL, default `1 - Estrutural`) em `CLOCALIZACAOESTOQUE`;
- enum `FinalidadeLocalizacao` (`Estrutural=1`, `Armazenagem=2`) em `App.Domain`;
- propriedade C# `Finalidade` em `LocalizacaoEstoque`;
- mapping EF Core da nova coluna;
- migration aditiva + backfill;
- ajuste de `LocalizacaoEstoqueServices.GetFuncaoEstrutura` e `MontarNo`;
- ajuste de `LocalizacaoEstoqueConsultaService` e do filtro por armazenagem, com centralizacao da regra;
- DTOs de Create/Update/Read e `LocalizacaoEstoqueListItemDto`;
- validator `LocalizacaoEstoqueValidator`;
- controller `LocalizacaoEstoqueController` (Post/Put);
- AutoMapper `MappingProfile`;
- frontend: modelo, service, editor, listagem, badges e estilos;
- eliminacao de `REQUER_FILHO` em todas as camadas;
- testes automatizados novos e adaptados;
- contrato de persistencia especifico `Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`.

Fora de escopo:

- alterar `CLOCALDEESTOQUE` ou qualquer agregado da nova vertical (`LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque`, `SaldoEstoque`);
- criar, alterar ou remover colunas em outras tabelas do legado (`CALMOXARIFADO`, `CAREAESTOQUE`, `CTIPOLOCALIZACAO`);
- sincronizacao automatica entre `LocalizacaoEstoque.finalidadelocalizacao` e a nova vertical;
- persistir a classificacao efetiva (`ESTRUTURAL`/`ARMAZENA`/`BLOQUEADO`) em coluna adicional;
- substituir `LocalizacaoEstoque` por `LocalDeEstoque` nesta decisao;
- alterar a entidade `TipoLocalizacao` ou seu mapping (continua legado/valido);
- criar indice novo, FK nova ou trigger.

## Regra formal

Sejam:

- `B = LocalizacaoEstoque.bloqueada`;
- `F = LocalizacaoEstoque.finalidadelocalizacao em { Estrutural, Armazenagem }`;
- `H = LocalizacaoEstoque possui um ou mais filhos`.

Classificacao efetiva `C`:

1. Se `B = true` entao `C = BLOQUEADO`.
2. Senao, se `H = true` entao `C = ESTRUTURAL`.
3. Senao, se `F = Armazenagem` entao `C = ARMAZENA`.
4. Senao `C = ESTRUTURAL`.

Complementos:

5. O estado `REQUER_FILHO` deixa de ser produzido pela arvore, pelo grid, pelos filtros, pelos DTOs e pelo frontend.
6. `TipoLocalizacao.permitearmazenagem` apenas sugere a finalidade inicial (`Armazenagem` se true, `Estrutural` se false) na criacao de um novo local; nunca funciona como proibicao; qualquer folha de qualquer tipo pode ser `Estrutural` ou `Armazenagem`.
7. O usuario pode alterar `F` enquanto `H = false`.
8. Se `H = true`, `C = ESTRUTURAL` independentemente de `F`; `F` pode permanecer como `Armazenagem` persistida. Enquanto o local possuir filhos, o campo de finalidade fica somente leitura no frontend e o backend **rejeita** a tentativa de alteracao de `F`. O backend nao apaga nem sobrescreve `F` apenas porque um filho foi criado.
9. Se o local perde o ultimo filho, `C` e recalculado em leitura com base em `F` persistida (sem reprocesso automatico); se `F = Armazenagem`, o local volta a `ARMAZENA`.
10. `LocalizacaoEstoqueValidator` deve bloquear a transicao de `F` para `Armazenagem` em local que ja possui filhos (rejeicao, nao coalescencia).
11. O backend e a autoridade da classificacao efetiva; o frontend nao recalcula a regra. Arvore, grid e demais consultas consomem classificacao produzida pelo backend. `GET /api/localizacao-estoque/arvore-por-area/{areaEstoqueId}` e a fonte canonica da classificacao da arvore; `/por-area/{areaEstoqueId}` permanece por compatibilidade, mas nao e a fonte da classificacao visual da arvore apos a implementacao.

## Invariantes

- `LocalizacaoEstoque.finalidadelocalizacao` e sempre `Estrutural` ou `Armazenagem`; nunca nulo (NOT NULL com default `Estrutural`).
- Um local com `H = true` jamais e classificado como `ARMAZENA`.
- Um local com `B = true` e sempre classificado como `BLOQUEADO`, independentemente de `F` ou `H`.
- A classificacao efetiva nunca e persistida; e derivada em leitura.
- `TipoLocalizacao.permitearmazenagem` nao define mais a classificacao autoritativa de folhas existentes.
- A nova vertical `LocalDeEstoque` nao e alterada e nao sincroniza com `LocalizacaoEstoque`.
- Pai e filho permanecem na mesma `AreaEstoque` e mesmo `Almoxarifado` (regras ja vigentes em `LocalizacaoPaiPertenceMesmaAreaAsync` e `ValidarHierarquiaAsync`).
- Ciclos hierarquicos permanecem proibidos (`ValidarCicloPutAsync`).
- Exclusao de local com filhos permanece bloqueada.

## Consequencias positivas

- Permite representar layouts reais com profundidades variaveis dentro da mesma Area.
- Elimina a rigidez de `TipoLocalizacao.permitearmazenagem` como unica origem de classificacao de folhas.
- Elimina o estado artificial `REQUER_FILHO`.
- Mantem `LocalizacaoEstoque` como legado, preservando consumidores e telas operacionais.
- Centraliza e uniformiza a regra entre arvore, grid, filtros e frontend (acaba com a divergencia preexistente).
- A excecao ao congelamento semantico e aditiva e pontual, sem impacto semantico em outros campos legados.
- Backfill deterministico e reversivel por remocao de coluna.

## Consequencias negativas

- Exige migration no legado congelado (excecao controlada, justificada por esta DL).
- Exige convivencia temporaria entre finalidade persistida e `TipoLocalizacao.permitearmazenagem` (que continua existindo como sugestao).
- Exige revisao cuidadosa de consumidores operacionais (`transferenciaestoque`, `bloqueioestoque`, `ajusteestoque`, `saidaestoque`, `entradaestoque`, `reservaestoque`, `inventarioestoque`) para garantir compatibilidade retroativa.
- Testes automatizados existentes nao cobriam `REQUER_FILHO`; novos cenarios precisam ser adicionados.
- Adicao de coluna com backfill requer revisao de script antes da aplicacao.
- Risco de rollback dos valores de `finalidadelocalizacao` configurados manualmente pos-migration se a coluna for removida.

## Riscos

- Regressao do workspace continuo (criar filho/irmao/raiz, salvamento/exclusao sem retorno a grid, preservacao de contexto, recuperacao por query params).
- Alteracao indevida da nova vertical `LocalDeEstoque` (mitigada: nada e alterado em `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque`).
- Mudanca semantica de campo legado reutilizado (mitigada: a decisao aprova campo novo, nao reutilizacao de `bloqueada` ou `permiteentrada/saida/producao`).
- Consumidores externos da API: adicionar campo e retrocompativel, mas telas operacionais devem ser testadas manualmente.
- DTOs compartilhados: manter `PermiteArmazenar` em `LocalizacaoEstoqueListItemDto` e adicionar `Classificacao` (string) consistente com a arvore.
- Concorrencia: leitura ja depende de existencia de filhos no momento da consulta; sem mudanca semantica.
- Excecao ao congelamento semantico do legado (DL-0041): tratada explicitamente como excecao pontual, aditiva, sem mudanca semantica em campos existentes.
- Tipos cadastrados com `permitearmazenagem=false` para garantir estrutura perdem a garantia automatica: mitigacao via `LocalizacaoEstoqueValidator` ou regra adicional se o dominio aprovar.

## Impacto no legado

- `LocalizacaoEstoque` continua sendo modelo legado; nao e promovido a Aggregate Root.
- Tabela `CLOCALIZACAOESTOQUE` recebe coluna aditiva `finalidadelocalizacao` (smallint, NOT NULL, default `1`).
- `LocalizacaoEstoqueServices.GetFuncaoEstrutura` deixa de produzir `REQUER_FILHO` e passa a ler `finalidadelocalizacao` para folhas.
- `LocalizacaoEstoqueConsultaService` e filtro de armazenagem usam a mesma regra centralizada.
- DTOs, validator e controller propagam `finalidadelocalizacao`.
- Migration + backfill incrementais e revisaveis.

## Impacto na nova vertical

- Nenhum. `CLOCALDEESTOQUE`, `LocalDeEstoque`, `UnidadeLogistica`, `MovimentacaoDeEstoque`, `SaldoEstoque` e demais agregados da nova vertical nao serao alterados.
- Nao havera sincronizacao automatica entre `LocalizacaoEstoque.finalidadelocalizacao` e qualquer agregado da nova vertical.
- A nova vertical continua sendo autoridade do dominio de Estoque (DL-0024).
- A correlacao opcional aprovada em DL-0038/DL-0041 permanece intocada.
- Esta decisao nao substitui `LocalizacaoEstoque` por `LocalDeEstoque`; a transicao permanece regida por DL-0041 (congelamento + carga inicial + substituicao gradual).

## Impacto em TipoLocalizacao

- `TipoLocalizacao.permitearmazenagem` permanece existindo como propriedade do catalogo de tipos.
- Deixa de ser a origem autoritativa da classificacao de folhas existentes.
- Passa a funcionar apenas como sugestao inicial de `finalidadelocalizacao` na criacao de um novo local (`Armazenagem` se `permitearmazenagem=true`; `Estrutural` caso contrario).
- `TipoLocalizacao.permitearmazenagem` nunca funciona como proibicao; qualquer folha de qualquer tipo pode ser configurada como `Estrutural` ou como `Armazenagem`.
- `TipoLocalizacao.nivelhierarquico` permanece orientando a hierarquia e a sugestao de proximo tipo.
- `TipoLocalizacao.ativo` permanece controlando disponibilidade do tipo no cadastro.
- Nenhum campo, mapping, migration ou DTO de `TipoLocalizacao` e alterado por esta decisao.

## Impacto nos dados existentes

A classificacao atual nao e persistida, portanto nao ha dado de `finalidadelocalizacao` a migrar; o backfill atribuira `Estrutural` ou `Armazenagem` conforme a classificacao atual em runtime:

- Folhas atualmente classificadas como `ARMAZENA` (`!bloqueada`, sem filhos, `TipoLocalizacao.permitearmazenagem=true`): `finalidadelocalizacao = Armazenagem`.
- Folhas atualmente classificadas como `REQUER_FILHO` (`!bloqueada`, sem filhos, `TipoLocalizacao.permitearmazenagem=false`): `finalidadelocalizacao = Estrutural` (estado deixa de existir).
- Locais com filhos (`ESTRUTURAL` em runtime): `finalidadelocalizacao = Estrutural` (estrategia conservadora; nao promove a armazenador).
- Locais bloqueados (`BLOQUEADO` em runtime): `finalidadelocalizacao = Estrutural`, preservando `bloqueada=true`.
- Locais sem `TipoLocalizacao` (FK NOT NULL, validado): nao ocorrem.

Estrategia:

- Backfill executado apenas apos aprovacao desta DL e em script incremental revisavel (nao aplicado nesta etapa documental).
- Apenas consulta read-only previa para auditoria dos dados existentes.
- Nenhum saneamento retroativo automatico.
- Migracao aditiva, default `Estrutural` colabora para que registros eventualmente nao cobertos pelo backfill permanecam seguros (nao armazenadores).

Ordem da migration futura: (1) adicionar coluna com default `Estrutural`; (2) executar backfill somente das folhas hoje classificadas como `ARMAZENA`; (3) manter `Estrutural` para os demais registros; (4) validar contagens; (5) nao alterar nenhuma outra tabela. Backfill nao executado nesta etapa.

Detalhes tecnicos do contrato no documento `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`.

## Impacto em APIs

- `POST /api/localizacao-estoque` e `PUT /api/localizacao-estoque/{id}` passam a receber `finalidadelocalizacao`.
- `GET /api/localizacao-estoque/arvore-por-area/{areaEstoqueId}` deixa de retornar `REQUER_FILHO` e passa a retornar classificacao consistente com a finalidade; e a fonte canonica da classificacao da arvore.
- `GET /api/localizacao-estoque/por-area/{areaEstoqueId}` permanece por compatibilidade; nao e a fonte da classificacao visual da arvore apos a implementacao.
- `GET /api/localizacao-estoque/paginado` expoe a classificacao consistente com a arvore (uniformizacao), podendo manter `PermiteArmazenar` por retrocompatibilidade e adicionar `Classificacao` (string).
- `GET /api/localizacao-estoque`, `/por-almoxarifado/{almoxarifadoid}` e `/{id}` mantem-se; DTOs de leitura recebem `finalidadelocalizacao`.
- Endpoints seguem `[AllowAnonymous]` exceto `paginado` (`[Authorize]`); a restricao de autorizacao nao e alterada por esta DL (pendencia tecnica separada).
- Retrocompatibilidade: adicionar `finalidadelocalizacao` e nao quebra desserializacao em consumidores existentes.

## Impacto em frontend

- Modelo `LocalizacaoEstoque` recebe `finalidade`; `StatusLocalizacao` remove `'requer-filho'`.
- Service envia `finalidade` em `toApiPayload` e tolera casing em `normalizarLocalizacao`.
- Editor adiciona Control `finalidade`; `getStatusNode`/`getPermiteArmazenagem`/`getFuncaoFormularioStatus` usam `finalidade`; `tipoPermiteTerminal` fica apenas como sugestao de criacao. Recomenda-se usar `getArvorePorArea` do backend como unica fonte de classificacao.
- Template adiciona select/radio `Estrutural`/`Armazenagem`; revisa badges e legenda; remove/revisa `.function-requer-filho`/`.status-requer-filho`.
- Listagem atualiza `getArmazenagemLabel` para refletir a nova classificacao (preferencialmente via campo retornado pelo backend).
- Workspace continuo permanece funcional: criar filho/irmao/raiz, salvar/excluir sem retorno a grid, preservacao de contexto, recuperacao por query params, retorno a lista apenas explicito.

## Impacto em testes

- `LocalizacaoEstoqueLegacyScenarios`: adaptar 4 cenarios existentes (`PaiComFilhoEhEstrutural`, `FolhaTerminalArmazena`, `BloqueadaNaoArmazena`, `ExcluirPaiComFilhoEhBloqueado`) e adicionar novos:
  - folha com `finalidade=Estrutural` -> `ESTRUTURAL`;
  - folha com `finalidade=Armazenagem` -> `ARMAZENA`;
  - pai com `finalidade=Armazenagem` recebe filho -> `ESTRUTURAL` efetivo, `finalidade` persistida `Armazenagem`;
  - exclusao do ultimo filho com `finalidade=Armazenagem` -> `ARMAZENA`;
  - validator rejeita `finalidade=Armazenagem` em local com filhos;
  - grid e arvore produzem classificacao consistente.
- Gap atual: nao ha cenarios para `ValidarHierarquiaAsync`, `ValidarCicloPutAsync`, `CodlocalizacaoUnicoNaAreaAsync`; recomenda-se cobri-los antes ou junto da mudanca.

## Impacto em documentacao

- Criada AS-0008 e esta DL-0042.
- Criado contrato de persistencia `Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`.
- Blocos "Regra hierarquica de armazenagem dos locais legados" em `ESTADO_ATUAL.md`, `CHANGELOG.md`, `INVENTARIO_FUNCIONAL.md`, `Regras de Negocio.md`, `Enderecamento.md` recebem nota de decisao aprovada e referencia a DL-0042 (preservando o conteudo descritivo do estado atual ate que a implementacao seja concluida).
- `Entidades.md`, `Relacionamentos.md` e `Dicionario de Dados.md` nao serao alterados nesta etapa; somente devem receber marcacao "decisao aprovada, implementacao pendente" quando o dominio concluir pela alteracao.
- Glossario Arquitetural: permanece inalterado nesta etapa; novos termos ("Finalidade Configurada", "Classificacao Efetiva") podem ser adicionados apenas se o padrao documental exigir criacao imediata; avalia-se essa necessidade apos validacao humana.
- AS e DL antigas nao serao alteradas.

## Referencias cruzadas

- **AS-0008** (origem desta DL).
- **AS-0001** decisao 7.5 ("Armazenagem em Nos Terminais Configurados") e regras de validacao - complementada, nao substituida.
- **AS-0004** secao 15 ("Estados devem ser especificos por agregado") - ancora separacao estado/configuracao/derivado.
- **DL-0001** (Estrutura Hierarquica de Enderecamento de Estoque) - decisoes 4 a 6 complementadas; a regra "armazenadora nao pode ter filhos ativos" permanece como classificacao efetiva (`ESTRUTURAL` para locais com filhos).
- **DL-0004** (Ciclo de Vida e Alteracoes Estruturais de Localizacoes) - compativel; `bloqueada` permanece parte do ciclo; `finalidadelocalizacao` e acrescentada como configuracao.
- **DL-0024** (Local de Estoque como Aggregate Root) - invariantes do AR `LocalDeEstoque` nao tocados; `LocalizacaoEstoque` permanece legado.
- **DL-0041** (Transicao do Legado de Localizacao para Local de Estoque MES) - esta DL constitui excecao controlada, aditiva e pontual ao congelamento semantico do legado aprovado em DL-0041; nao altera a Estrategia B de transicao.

## Excecao controlada ao congelamento do legado

DL-0041 aprovou o congelamento semantico de `CLOCALIZACAOESTOQUE` (Estrategia B). Esta DL-0042 declara uma **excecao controlada, aditiva e pontual** a esse congelamento, restrita a:

- inclusao de uma unica coluna `finalidadelocalizacao` (smallint, NOT NULL, default `1`);
- ajuste da regra de classificacao runtime para usar essa coluna;
- eliminacao de `REQUER_FILHO` em todas as camadas;
- centralizacao/uniformizacao da regra entre arvore, grid e filtros.

A excecao **nao** autoriza:

- alteracao semantica de campos legados existentes (`bloqueada`, `permiteentrada`, `permitesaida`, `permiteproducao`, `localizacaopaiid`, `capacidade`, `unidadecapacidadeid`, FKs, indices);
- escrita da nova vertical em `CLOCALIZACAOESTOQUE`;
- alteracao em `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque`;
- promocao de `LocalizacaoEstoque` a Aggregate Root concorrente de `LocalDeEstoque`;
- sincronizacao automatica entre os dois modelos;
- substituicao de `LocalizacaoEstoque` por `LocalDeEstoque` nesta decisao.

A transicao permanente continua regida por DL-0041; esta excecao nao estende o escopo da evolucao semantica geral do legado.

## Criterios de conclusao

1. Existe `enum FinalidadeLocalizacao` em `App.Domain` com valores `Estrutural=1` e `Armazenagem=2`.
2. Existe propriedade `Finalidade` em `LocalizacaoEstoque`, mapeada para `finalidadelocalizacao` em `CLOCALIZACAOESTOQUE`.
3. Migration aditiva e backfill revisados e aplicados; consulta read-only auditada antes da aplicacao.
4. `LocalizacaoEstoqueServices.GetFuncaoEstrutura` e `MontarNo` nao produzem `REQUER_FILHO`.
5. `LocalizacaoEstoqueConsultaService` produz classificacao consistente com a arvore.
6. DTOs de Create/Update/Read e `LocalizacaoEstoqueListItemDto` propagam `finalidadelocalizacao` (e, opcionalmente, `Classificacao`).
7. `LocalizacaoEstoqueValidator` rejeita `Armazenagem` em local com filhos.
8. Controller Post/Put validam regras de transicao de `finalidadelocalizacao`.
9. Frontend envia e consome `finalidadelocalizacao`; nao recalcula a classificacao independentemente.
10. Estado `REQUER_FILHO` nao aparece em arvore, grid, filtro, DTO, badge ou mensagem.
11. Grid e arvore exibem a mesma classificacao para o mesmo local.
12. Nenhum arquivo de `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque` ou `SaldoEstoque` foi alterado.
13. Workspace continuo permanece funcional (criar filho/irmao/raiz, salvar/excluir sem retorno a grid, preservacao de contexto, recuperacao por query params, retorno explicito a lista).
14. Testes automatizados cobrem todos os cenarios principais da regra efetiva.
15. Documentacao de AS, DL e contrato de persistencia reflete o estado implementado.
16. Validacao humana (responsaveis abaixo) aprovou a aplicacao.

## Rollback conceitual

O rollback consiste na remocao da coluna `finalidadelocalizacao` em uma migration descendente, retornando ao comportamento anterior (classificacao derivada de `TipoLocalizacao.permitearmazenagem` em folhas, com retorno de `REQUER_FILHO`). Riscos do rollback:

- Perda dos valores de `finalidadelocalizacao` configurados manualmente pelos operadores apos a aplicacao.
- Retorno da divergencia semantica entre grid e arvore.
- Retorno do estado `REQUER_FILHO`.
- Necessidade de limpar mensagens/badges/frontends que faziam uso da `finalidade`.

O rollback deve ser tratado como evento controlado, com auditoria previa e comunicacao a operacao.

## Responsaveis pela validacao humana

- Product Owner / Responsavel pelo Projeto MES (Lucas Zanardi).
- Responsavel tecnico por backend (.NET/EF Core/MySQL/Pomelo).
- Responsavel tecnico por frontend (Angular).
- Responsavel por dados / DBA (validacao read-only previa e revisao do script de backfill).
- Aprovacao final registrada nesta DL antes da aplicacao da migration e da alteracao de codigo.

Esta DL declara uma decisao aprovada por validacao humana para formalizar a regra; nao implementa codigo, migration, script SQL ou alteracao de banco. Implementacao e aplicacao permanecem pendentes de etapas posteriores autorizadas a partir desta DL.
