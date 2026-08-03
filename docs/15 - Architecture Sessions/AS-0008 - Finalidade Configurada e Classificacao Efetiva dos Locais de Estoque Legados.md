# AS-0008 - Finalidade Configurada e Classificacao Efetiva dos Locais de Estoque Legados

## 1. Identificacao

| Campo | Valor |
|---|---|
| Codigo | AS-0008 |
| Titulo | Finalidade Configurada e Classificacao Efetiva dos Locais de Estoque Legados |
| Status | Concluida |
| Status tecnico | Decisao arquitetural aprovada; implementacao tecnica pendente; migration pendente; database update nao executado; backend nao alterado; frontend nao alterado; banco nao alterado. |
| Data da sessao | 2026-07-31 |
| Data da ultima revisao | 2026-07-31 |
| Responsavel pelo produto | Lucas Zanardi |
| Origem | Inspecao tecnica read-only do legado de Locais de Estoque (`LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`) que concluiu pela ausencia de campo persistido para finalidade configurada por local, pela duplicacao da regra de classificacao entre arvore, grid, filtros e frontend, e pela necessidade de eliminacao do estado `REQUER_FILHO`. |

Participantes:

- Product Owner / Responsavel pelo Projeto MES.
- Codex, no papel de arquiteto tecnico documental.

Nenhum codigo, migration, endpoint, dependencia, schema, script SQL ou refatoracao foi criado nesta sessao. Esta sessao consolida apenas a decisao arquitetural documental e indica a Decision Log recomendada.

## 2. Contexto

A funcionalidade legada de Locais de Estoque usa o modelo `LocalizacaoEstoque` persistido em `CLOCALIZACAOESTOQUE`, com hierarquia por `localizacaopaiid`, vinculada a `Almoxarifado`, `AreaEstoque` e `TipoLocalizacao`. A classificacao de cada local em `ESTRUTURAL`, `ARMAZENA`, `REQUER_FILHO` ou `BLOQUEADO` e calculada em runtime, nao persistida, pelo metodo `LocalizacaoEstoqueServices.GetFuncaoEstrutura` (arvore) e por replicacao parcial em `LocalizacaoEstoqueConsultaService` (grid paginado) e no frontend do mapa em `CadlocalizacaoestoqueComponent`.

Existe paralelamente uma nova vertical de Estoque baseada em `LocalDeEstoque` (`CLOCALDEESTOQUE`), `UnidadeLogistica`, `MovimentacaoDeEstoque`, `SaldoEstoque` etc., aprovada em AS-0004/AS-0007/DL-0022..DL-0041. Essa vertical e separada, nao foi alterada nesta sessao e nao e tratada como substituta imediata do legado. O congelamento semantico do legado esta aprovado em DL-0041 (Estrategia B: congelamento + implementacao paralela + carga inicial + correlacao + substituicao gradual).

A inspecao tecnica read-only realizada confirmou que:

- `LocalizacaoEstoque` nao possui propriedade de finalidade configurada pelo usuario;
- `TipoLocalizacao.permitearmazenagem` e a unica origem persistida que influencia a classificacao de folhas;
- a regra atual produz `BLOQUEADO`, `ESTRUTURAL`, `ARMAZENA` e `REQUER_FILHO`;
- a regra esta duplicada em quatro pontos fisicos (arvore backend, grid backend, filtro backend, frontend do mapa);
- a regra desejada exige nova coluna em `CLOCALIZACAOESTOQUE`;
- a mudanca exige migration;
- a mudanca representa excecao controlada ao congelamento semantico do legado definido em DL-0041.

## 3. Problema

A regra atual e rigida demais: a classificacao de folhas deriva exclusivamente de `TipoLocalizacao.permitearmazenagem`. Dois locais folha do mesmo `TipoLocalizacao` sao sempre classificados de forma identica, sem permitir diferenciar, dentro da mesma Area de Estoque, um como armazenador e outro como estrutural.

Isso obriga a criar Areas de Estoque diferentes apenas para acomodar ramos com profundidades variaveis, dificultando a representacao de layouts industriais reais em que, dentro de uma mesma Area, alguns ramos terminam em `Coluna` armazenadora enquanto outros descem ate `Andar`.

O estado `REQUER_FILHO` forca, artificialmente, o operador a criar descendentes para locais cujo tipo tem `permitearmazenagem=false`, mesmo quando o operador deseja encerrar a hierarquia naquele local como estrutural.

## 4. Exemplos reais

Layout que nao pode ser representado pela regra atual sem criar Areas diferentes:

```text
AREA
+-- RUA1
|   +-- COLUNA1 -> deve poder ser ARMAZENA
+-- RUA2
|   +-- COLUNA2 -> deve poder ser ARMAZENA
+-- RUA3
    +-- COLUNA3
        +-- ANDAR1 -> deve poder ser ARMAZENA
```

Dentro da mesma Area de Estoque o usuario precisa configurar `COLUNA1`, `COLUNA2` e `ANDAR1` como armazenadores. Nao deve ser necessario criar Areas diferentes apenas porque `COLUNA3` e estrutural enquanto `COLUNA1`, `COLUNA2` e `ANDAR1` sao armazenadores.

## 5. Limitacoes da regra atual

- A finalidade de armazenagem nao e configuravel por local; apenas por tipo.
- Folhas do mesmo tipo sao classificadas de forma identica.
- Nao existe conceito de "finalidade persistida por local".
- O estado `REQUER_FILHO` obriga criacao de descendentes mesmo quando o operador deseja encerrar a hierarquia.
- A regra e calculada em runtime e duplicada em arvore, grid, filtros e frontend, gerando divergencia semantica (a grid nao expoe `REQUER_FILHO` enquanto a arvore expoe).
- O frontend do mapa recalcula a classificacao em vez de consumir status consolidado do backend.

## 6. Regra atual confirmada no codigo

Definida no metodo `LocalizacaoEstoqueServices.GetFuncaoEstrutura` em `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs`, linhas 102 a 111:

1. Se `bloqueada` retorna `BLOQUEADO`.
2. Se `possuiFilhos` retorna `ESTRUTURAL`.
3. Se folha e `TipoLocalizacao.permitearmazenagem=true` retorna `ARMAZENA`.
4. Se folha e `TipoLocalizacao.permitearmazenagem=false` retorna `REQUER_FILHO`.

Replicacao parcial em `BACKEND/PRPA/App.Infra.Data/Persistence/LocalizacaoEstoque/LocalizacaoEstoqueConsultaService.cs` (linhas 53, 61, 98 e 144-149) e no frontend em `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque/cadlocalizacaoestoque.component.ts` em `getStatusNode`, `getPermiteArmazenagem` e `getFuncaoFormularioStatus`.

## 7. Alternativas avaliadas

| Alternativa | Descricao | Resultado |
|---|---|---|
| A | Manter a regra atual por `TipoLocalizacao.permitearmazenagem`, sem permitir configuracao por local. | Rejeitada. Nao resolve a rigidez nem a necessidade de variedade de profundidades dentro da mesma Area. |
| B | Tornar toda folha automaticamente armazenadora, exceto bloqueados. | Rejeitada. Remove a possibilidade de folhas estruturais (ex.: ponto de passagem, corredor curto) e conflita com DL-0001 (no terminal configurado). |
| C | Finalidade configurada por local (Estrutural/Armazenagem), persistida em nova coluna de `CLOCALIZACAOESTOQUE`, com classificacao efetiva derivada em runtime, eliminando `REQUER_FILHO`. | Aprovada. |
| D | Mover a mudanca exclusivamente para `LocalDeEstoque` da nova vertical (`CLOCALDEESTOQUE`). | Rejeitada como solucao imediata. A nova vertical e separada, nao deve ser alterada nesta decisao, e a operacao legada ainda depende de `LocalizacaoEstoque`. Pode ser revisitada na transicao aprovada por DL-0041. |

## 8. Decisao proposta

Adotar a alternativa C: introduzir "finalidade configurada" por `LocalizacaoEstoque` (valores `Estrutural` e `Armazenagem`), persistida em nova coluna de `CLOCALIZACAOESTOQUE`, e derivar em runtime a "classificacao efetiva" (`ESTRUTURAL`, `ARMAZENA` ou `BLOQUEADO`), eliminando o estado `REQUER_FILHO`.

`TipoLocalizacao.permitearmazenagem` deixa de ser a origem autoritativa da classificacao de folhas e passa a funcionar apenas como sugestao inicial para novos locais.

## 9. Separacao entre finalidade e classificacao

| Conceito | Natureza | Persistido | Valores | Origem |
|---|---|---|---|---|
| Finalidade configurada | Configuracao do usuario por local | Sim, em `CLOCALIZACAOESTOQUE.finalidadelocalizacao` | `Estrutural` (1), `Armazenagem` (2) | Escolha do usuario (`Estrutural` por default) |
| Classificacao efetiva | Resultado calculado em runtime | Nao | `ESTRUTURAL`, `ARMAZENA`, `BLOQUEADO` | Derivada de `finalidadelocalizacao` + `bloqueada` + existencia de filhos |

A classificacao efetiva continua sendo calculada em leitura e nao sera persistida como coluna adicional, mantendo alinhamento com a diretriz de AS-0004 ("Estados devem ser especificos por agregado") e com DL-0041 (nao persistir estado autoritativo derivado).

## 10. Regra efetiva pretendida

1. Se o local estiver bloqueado (`bloqueada=true`): classificacao efetiva = `BLOQUEADO`.
2. Se o local possuir um ou mais filhos: classificacao efetiva = `ESTRUTURAL`.
3. Se o local nao possuir filhos e `finalidadelocalizacao=Armazenagem`: classificacao efetiva = `ARMAZENA`.
4. Se o local nao possuir filhos e `finalidadelocalizacao=Estrutural`: classificacao efetiva = `ESTRUTURAL`.
5. O estado `REQUER_FILHO` sera eliminado da arvore, da grid, dos filtros, dos DTOs e do frontend.
6. `TipoLocalizacao.permitearmazenagem` passa a funcionar apenas como sugestao inicial na criacao de novos locais.
7. O usuario pode alterar a finalidade enquanto o local for folha (sem filhos).
8. Um local com filhos nunca armazena efetivamente, independentemente da finalidade persistida.
9. A `finalidadelocalizacao` pode permanecer como `Armazenagem` enquanto houver filhos.
10. Ao perder o ultimo filho, o local volta a seguir sua finalidade persistida.
11. Diferentes ramos da mesma `AreaEstoque` podem terminar em profundidades diferentes.

## 11. Papel do TipoLocalizacao

- `TipoLocalizacao.permitearmazenagem` continua existindo como propriedade do catalogo de tipos.
- Funciona **apenas como sugestao inicial** da finalidade na criacao de um novo local (se `permitearmazenagem=true`, sugere `Armazenagem`; caso contrario, sugere `Estrutural`).
- **Nunca funciona como proibicao**: qualquer folha, de qualquer tipo, pode ser configurada como `Estrutural` ou como `Armazenagem`.
- Nao define mais a classificacao autoritativa de folhas existentes.
- `TipoLocalizacao.nivelhierarquico` continua orientando a hierarquia e a sugestao de proximo tipo em `getProximoTipoLocalizacao`.
- `TipoLocalizacao.ativo` continua permitindo selecionar tipos validos no cadastro.

## 12. Comportamento com filhos

- Um local com filhos nunca armazena efetivamente; sua classificacao efetiva e **sempre** `ESTRUTURAL`.
- A `finalidadelocalizacao` persistida pode continuar como `Armazenagem` enquanto houver filhos; essa preservacao permite que o local volte a `ARMAZENA` se perder o ultimo filho.
- Ao adicionar um filho a um local com `finalidadelocalizacao=Armazenagem`:
  - o pai passa a classificacao efetiva `ESTRUTURAL` na proxima leitura;
  - a `finalidadelocalizacao` do pai permanece `Armazenagem` (persistida);
  - o backend **nao apaga nem sobrescreve** a `finalidadelocalizacao` apenas porque um filho foi criado;
  - o usuario nao precisa alterar a finalidade do pai.
- Enquanto o local possuir filhos, o campo de finalidade fica **somente leitura** no frontend.
- O backend **rejeita** a tentativa de alterar a finalidade de um local que possui filhos (validador).
- Um local com filhos jamais e exibido, consultado ou tratado como `ARMAZENA`.

Registro explicito:

| Finalidade persistida | Possui filhos | Classificacao efetiva |
|---|---|---|
| Armazenagem | Sim | ESTRUTURAL |

## 13. Comportamento ao excluir ultimo filho

- A exclusao de localizacao com filhos permanece bloqueada no service legado (`LocalizacaoEstoqueServices.DeleteAsync`).
- Ao excluir o unico/ultimo filho de um pai:
  - o pai passa a nao possuir mais filhos;
  - a classificacao efetiva do pai e recalculada na proxima leitura com base em `finalidadelocalizacao` persistida;
  - se a `finalidadelocalizacao` for `Armazenagem`, o pai volta a ser classificado como `ARMAZENA`;
  - se for `Estrutural`, o pai passa a `ESTRUTURAL`.
- Nao havera reprocesso automatico de `finalidadelocalizacao` na exclusao; o valor persistido e respeitado.

## 14. Comportamento de bloqueio

- `bloqueada` (boolean ja existente em `LocalizacaoEstoque`) tem prioridade absoluta.
- Folha ou pai, armazenador ou estrutural, bloqueado gera classificacao efetiva `BLOQUEADO`.
- Bloquear/desbloquear nao altera `finalidadelocalizacao`.
- Apos desbloquear, o local volta a seguir a regra efetiva (finalidade + filhos).

## 15. Estruturas com profundidades diferentes

A regra aprovada permite, dentro de uma mesma `AreaEstoque`:

```text
AREA
+-- RUA1
|   +-- COLUNA1 (finalidade=Armazenagem) -> ARMAZENA
+-- RUA2
|   +-- COLUNA2 (finalidade=Armazenagem) -> ARMAZENA
+-- RUA3
    +-- COLUNA3 (finalidade=Estrutural) -> ESTRUTURAL (pai)
        +-- ANDAR1 (finalidade=Armazenagem) -> ARMAZENA
```

Nao e necessario criar Areas diferentes apenas por profundidade variavel.

## 16. Impacto no legado

- Entidade `LocalizacaoEstoque` recebe nova propriedade `Finalidade` (enum `FinalidadeLocalizacao { Estrutural=1, Armazenagem=2 }`).
- Mapping `LocalizacaoEstoqueConfig` mapeia a nova coluna `finalidadelocalizacao` (smallint, NOT NULL, default 1).
- Nova migration aditiva em `App.Infra.Data/Migrations/`.
- `LocalizacaoEstoqueServices.GetFuncaoEstrutura` e `MontarNo` sao ajustados para usar a finalidade persistida e eliminar `REQUER_FILHO`.
- `LocalizacaoEstoqueConsultaService` e o filtro `PermiteArmazenagem` sao ajustados para usar a finalidade persistida; idealmente a regra e centralizada em um unico ponto compartilhado (eliminando duplicacao).
- DTOs `LocalizacaoEstoqueCreateDto`, `LocalizacaoEstoqueUpdateDto`, `LocalizacaoEstoqueReadDto` recebem `Finalidade`.
- `LocalizacaoEstoqueListItemDto` expoe a classificacao consistente com a arvore (uniformizacao).
- `LocalizacaoEstoqueValidator` valida `Finalidade` e impede `Armazenagem` em local com filhos.
- Controller `LocalizacaoEstoqueController` (Post/Put) propaga `finalidade` e valida regras de transicao.
- AutoMapper `MappingProfile` mapeia a nova propriedade.

## 17. Impacto na nova vertical

- Nenhum. `LocalDeEstoque` (`CLOCALDEESTOQUE`), `UnidadeLogistica`, `MovimentacaoDeEstoque` e `SaldoEstoque` da nova vertical nao serao alterados.
- Nao havera sincronizacao automatica entre `LocalizacaoEstoque.finalidadelocalizacao` e qualquer agregado da nova vertical.
- A nova vertical continua sendo autoridade do dominio de Estoque.
- Esta decisao nao promove `LocalizacaoEstoque` a Aggregate Root concorrente.
- A correlacao opcional aprovada em DL-0038/DL-0041 permanece intocada.

## 18. Impacto no frontend

- Modelo `LocalizacaoEstoque` em `localizacaoestoque.interface.ts` recebe `finalidade` e remove o ramo `'requer-filho'` de `StatusLocalizacao`.
- Service `localizacaoestoque.service.ts` envia `finalidade` em `toApiPayload` e tolera diferentes casings em `normalizarLocalizacao`.
- Editor `cadlocalizacaoestoque.component.ts`:
  - adiciona Control `finalidade` no FormGroup;
  - `getStatusNode`, `getPermiteArmazenagem` e `getFuncaoFormularioStatus` passam a usar `finalidade` e nao `TipoLocalizacao.permitearmazenagem`;
  - `tipoPermiteTerminal` e mantido apenas como sugestao de criacao de novo local;
  - detalhe recomendado: o frontend do mapa passa a consumir classificacao consolidada do backend (preferencialmente via `getArvorePorArea`), eliminando recalculo duplicado.
- Template `cadlocalizacaoestoque.component.html`: adiciona select/radios de `Estrutural`/`Armazenagem`; revisa badges e legenda.
- SCSS: remove/revisa `.function-requer-filho` e `.status-requer-filho`.
- Listagem `listlocalizacaoestoque.component.ts`: `getArmazenagemLabel` reflete a nova classificacao (e idealmente usa campo retornado pelo backend, nao recalcula).

## 19. Impacto no backend

Onde a classificacao e produzida hoje (`LocalizacaoEstoqueServices.GetFuncaoEstrutura`) e onde e derivada no grid (`LocalizacaoEstoqueConsultaService`) devem ser revisados para:

- ler `finalidadelocalizacao` persistida no ramo folha;
- eliminar `REQUER_FILHO`;
- centralizar a regra em uma unica funcao compartilhada para arvore e grid (acaba com a divergencia atual);
- expor a mesma classificacao em ambos os DTOs de saida (TreeNode e ListItem).

Recomenda-se criar `enum FinalidadeLocalizacao` em `App.Domain` e, opcionalmente, `enum ClassificacaoLocalizacao` para saida.

O backend sera a autoridade da classificacao efetiva; o frontend nao recalculara a regra. A arvore, a grid e demais consultas devem consumir a classificacao produzida pelo backend.

## 20. Impacto na persistencia

Detalhado no contrato especifico: `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md`.

Resumo:

- Tabela: `CLOCALIZACAOESTOQUE`.
- Coluna nova: `finalidadelocalizacao`.
- Propriedade C#: `Finalidade`.
- Enum: `FinalidadeLocalizacao` (`Estrutural=1`, `Armazenagem=2`).
- Tipo: `smallint`.
- Nulabilidade: `NOT NULL`.
- Default: `1` (`Estrutural`).
- CHECK recomendado: `CHECK (finalidadelocalizacao IN (1,2))` (depende de confirmacao da versao do MySQL; pode ser omitido da primeira migration se a compatibilidade nao estiver comprovada; nao substitui validacao da aplicacao).
- Classificacao efetiva: nao persistida, calculada no backend.
- Sem FK nova, sem indice novo, sem trigger.
- Backfill detalhado no contrato; sera executado apenas apos aprovacao e em script incremental revisavel; nao altera `CLOCALDEESTOQUE`, `UnidadeLogistica` ou `MovimentacaoDeEstoque`.
- Rollback: remocao da coluna (com risco de perda de valores configurados manualmente pos-migration).

## 21. Riscos

- Regressao do workspace continuo (criar filho/irmao/raiz, salvar/excluir sem retorno a grid, preservacao de contexto, recuperacao por query params).
- Alteracao indevida da nova vertical `LocalDeEstoque` (mitigada: nada e alterado na nova vertical).
- Mudanca semantica de campo legado reutilizado (mitigada: a decisao aprova campo novo, nao reutilizacao de `bloqueada` ou `permiteentrada/saida/producao`).
- Consumidores externos da API (DTOs de `LocalizacaoEstoque` sao consumidos por telas operacionais como `transferenciaestoque`, `bloqueioestoque`, `ajusteestoque`, `saidaestoque`, `entradaestoque`, `reservaestoque`, `inventarioestoque`); adicionar campo e retrocompativel, mas testes manuais obrigatorios.
- DTOs compartilhados: manter `PermiteArmazenar` em `LocalizacaoEstoqueListItemDto` por retrocompatibilidade e adicionar `Classificacao` (string) consistente com a arvore.
- Testes dependentes de `REQUER_FILHO`: gap de cobertura atual (nao existem); nenhum teste deve quebrar, mas cenarios novos devem ser adicionados.
- Inconsistencia entre grid e arvore (preexistente): a mudanca deve uniformizar.
- Classificacao calculada no frontend: deve ser descontinuada em favor de classificacao do backend.
- Concorrencia: ja atualmente a leitura depende de existencia de filhos no momento da consulta; sem mudanca.
- Exclusao do ultimo filho e adicao de filho a armazenador: cobertos por itens 12 e 13.
- Excecao ao congelamento semantico do legado (DL-0041): tratada explicitamente na DL recomendada como excecao pontual, aditiva, sem mudanca semantica de campos existentes.

## 22. Criterios de aceitacao

1. Uma folha pode ser `Estrutural`.
2. Uma folha pode ser `Armazenagem`.
3. Um local com filhos e sempre `ESTRUTURAL` efetivo.
4. Um local bloqueado e sempre `BLOQUEADO` efetivo.
5. Um pai pode manter `finalidade=Armazenagem` persistida enquanto tiver filhos.
6. Ao perder o ultimo filho, o local volta a seguir a finalidade persistida.
7. `REQUER_FILHO` nao e retornado por arvore, grid ou API.
8. Grid e arvore usam a mesma classificacao, consumida do backend; o frontend nao recalcula a regra.
9. `GET /api/localizacao-estoque/arvore-por-area/{areaEstoqueId}` e a fonte canonica da classificacao da arvore; `/por-area/{areaEstoqueId}` permanece por compatibilidade, mas nao e a fonte da classificacao visual da arvore.
10. `TipoLocalizacao` apenas sugere a finalidade inicial; qualquer folha de qualquer tipo pode ser `Estrutural` ou `Armazenagem`.
11. Nenhuma alteracao ocorre em `CLOCALDEESTOQUE`, `UnidadeLogistica` ou `MovimentacaoDeEstoque`.
12. O workspace continuo permanece funcional.
13. Migration e backfill sao incrementais e revisaveis.
14. Testes automatizados cobrem os cenarios principais.

## 23. Decisoes definitivas (encerradas)

As decisoes a seguir estao encerradas como definitivas nesta AS-0008 e nao dependem de validacao posterior. A implementacao tecnica permanece pendente, mas a regra de negocio esta fechada.

### 23.1 TipoLocalizacao.permitearmazenagem

- Funciona apenas como sugestao inicial na criacao de um novo local.
- Nunca funciona como proibicao.
- Se `false`: sugere `Estrutural`.
- Se `true`: sugere `Armazenagem`.
- O usuario pode alterar a sugestao enquanto o local for folha.
- Qualquer folha, de qualquer `TipoLocalizacao`, pode ser configurada como `Estrutural` ou como `Armazenagem`.

### 23.2 Finalidade Armazenagem em local com filhos

- Um local com filhos nunca armazena efetivamente; sua classificacao efetiva e sempre `ESTRUTURAL`.
- A finalidade persistida pode continuar como `Armazenagem`; essa preservacao permite que o local volte a `ARMAZENA` se perder o ultimo filho.
- Enquanto possuir filhos, o campo de finalidade fica somente leitura no frontend.
- O backend rejeita a tentativa de alteracao da finalidade enquanto o local possuir filhos.
- O backend nao apaga nem sobrescreve a finalidade ja persistida apenas porque um filho foi criado.

Registro explicito:

| Finalidade persistida | Possui filhos | Classificacao efetiva |
|---|---|---|
| Armazenagem | Sim | ESTRUTURAL |

### 23.3 Fonte unica de classificacao

- O backend sera a autoridade da classificacao efetiva.
- O frontend nao recalcula a regra.
- Arvore, grid e demais consultas devem consumir a classificacao produzida pelo backend.
- `GET /api/localizacao-estoque/arvore-por-area/{areaEstoqueId}` e a fonte canonica da classificacao da arvore.
- O endpoint `/por-area/{areaEstoqueId}` pode continuar existindo por compatibilidade, mas nao deve ser a fonte da classificacao visual da arvore apos a implementacao.

### 23.4 Backfill dos pais

- Todos os locais com filhos recebem `finalidadelocalizacao=Estrutural` no backfill (estrategia conservadora).
- Nao promover pais automaticamente para `Armazenagem`.
- Apos a migration, se um pai voltar a ser folha, o usuario podera alterar sua finalidade para `Armazenagem`.
- Nenhum dado existente deve ser promovido automaticamente a armazenador sem intencao explicita, exceto folhas que hoje ja sao classificadas como `ARMAZENA`.

## 24. Comportamento do frontend (definitivo)

- **Local folha**:
  - finalidade editavel;
  - opcoes `Estrutural` e `Armazenagem`;
  - sugestao inicial vem de `TipoLocalizacao`;
  - usuario pode alterar.
- **Local com filhos**:
  - finalidade somente leitura;
  - finalidade persistida e exibida;
  - classificacao efetiva exibida como `ESTRUTURAL`;
  - mensagem: "Este local possui niveis abaixo e, por isso, e efetivamente estrutural."
- **Local bloqueado**:
  - finalidade persistida permanece;
  - classificacao efetiva exibida como `BLOQUEADO`.
- **Ao adicionar filho a local armazenador**:
  - exibir confirmacao;
  - nao alterar finalidade persistida do pai;
  - pai passa a classificacao efetiva `ESTRUTURAL`.
- **Ao excluir o ultimo filho**:
  - recalcular por leitura;
  - seguir finalidade persistida.

## 25. Coerencia com a nova vertical

- `LocalizacaoEstoque` continua sendo modelo legado.
- `LocalDeEstoque` continua sendo autoridade da nova vertical.
- Nenhum campo e adicionado a `CLOCALDEESTOQUE`.
- Nenhuma sincronizacao automatica entre `LocalizacaoEstoque.finalidadelocalizacao` e `LocalDeEstoque`.
- Nenhum relacionamento novo entre os dois modelos.
- Nenhum write-through entre modelos.
- Os dois contratos de persistencia (primeira vertical e finalidade legada) continuam independentes.

## 26. Relacao com AS-0001, AS-0004 e demais sessoes pertinentes

- **AS-0001** (Arquitetura de Enderecamento e Localizacao de Estoque), decisao 7.5 ("Armazenagem em Nos Terminais Configurados") e regras de validacao: a AS-0008 refina essa decisao, separando "no terminal configurado por tipo" (AS-0001) de "finalidade configurada por local" (AS-0008). A regra de que "armazenadora nao pode ter filhos ativos" permanece como classificacao efetiva (item 8 da regra aprovada); a novidade e que a `finalidadelocalizacao` persistida pode permanecer `Armazenagem` enquanto houver filhos, sem que isso promova o pai a armazenador.
- **AS-0004** (Arquitetura do Dominio de Estoque), secao 15 ("Estados devem ser especificos por agregado"): ancora a separacao entre `bloqueada` (estado), `finalidadelocalizacao` (configuracao) e classificacao efetiva (derivada). Reforca a decisao de NAO persistir a classificacao efetiva.
- **AS-0007** (Arquitetura Tecnica da Primeira Vertical Funcional de Estoque) e secoes de coexistencia com legado: a AS-0008 e uma excecao controlada e aditiva ao congelamento semantico aprovado em DL-0041; nao altera a nova vertical.
- **DL-0001** (Estrutura Hierarquica de Enderecamento de Estoque): a AS-0008 complementa as decisoes 4 a 6 ("nos terminais configurados", "armazenadora nao pode ter filhos ativos") sem substitui-las. A classificacao efetiva de `ESTRUTURAL` para locais com filhos preserva o principio de DL-0001.
- **DL-0004** (Ciclo de Vida e Alteracoes Estruturais de Localizacoes): a AS-0008 e compativel; `bloqueada` permanece parte do ciclo de vida; `finalidadelocalizacao` e acrescentada como configuracao adicional.
- **DL-0024** (Local de Estoque como Aggregate Root): a AS-0008 nao toca invariantes do AR `LocalDeEstoque` da nova vertical; mantem `LocalizacaoEstoque` como legado.
- **DL-0041** (Transicao do Legado): a AS-0008 declara uma excecao controlada, pontual e aditiva ao congelamento semantico do legado, sem promover `LocalizacaoEstoque` a AR concorrente. A excecao sera formalizada em nova DL.

## 27. Recomendacao de Decision Log

Recomenda-se criar a Decision Log `DL-0042 - Finalidade de Armazenagem por Localizacao de Estoque Legada`, aprovada por validacao humana, que formalize:

- a separacao entre finalidade configurada e classificacao efetiva;
- a eliminacao de `REQUER_FILHO`;
- o papel de `TipoLocalizacao.permitearmazenagem` como sugestao inicial;
- a excecao controlada, aditiva e pontual ao congelamento semantico do legado aprovado em DL-0041;
- o contrato de persistencia da nova coluna `finalidadelocalizacao`;
- a estrategia de backfill e rollback;
- os criterios de conclusao e os responsaveis pela validacao humana.

Recomenda-se tambem a criacao do contrato de persistencia especifico `Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada.md` em `docs/05 - Estoque/`.

## 28. Fontes analisadas

### 28.1 Architecture Sessions e Decision Logs

- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `MES-ProjectBook/docs/15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0001 - Arquitetura de Enderecamento de Estoque.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0004 - Ciclo de Vida e Alteracoes Estruturais de Localizacoes.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0024 - Local de Estoque como Aggregate Root.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0038 - Persistencia de Local de Estoque e Relacao com Legado.md`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0041 - Transicao do Legado de Localizacao para Local de Estoque MES.md`

### 28.2 Documentacao funcional e tecnica

- `MES-ProjectBook/docs/05 - Estoque/Inventario da Implementacao Legada de Locais de Estoque.md`
- `MES-ProjectBook/docs/05 - Estoque/Enderecamento.md`
- `MES-ProjectBook/docs/05 - Estoque/Regras de Negocio.md`
- `MES-ProjectBook/docs/01 - Visao Geral do Projeto/INVENTARIO_FUNCIONAL.md`
- `MES-ProjectBook/docs/00 - IA/ESTADO_ATUAL.md`
- `MES-ProjectBook/docs/00 - IA/CHANGELOG.md`
- `MES-ProjectBook/docs/20 - Glossario Arquitetural/Glossario Arquitetural do MES.md`

### 28.3 Codigo (inspecionado somente leitura)

- `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoqueTreeNode.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/TipoLocalizacao.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/LocalizacaoEstoqueConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Mapping/TipoLocalizacaoConfig.cs`
- `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs`
- `BACKEND/PRPA/App.Infra.Data/Migrations/ProjetoContextModelSnapshot.cs`
- `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/LocalizacaoEstoque/LocalizacaoEstoqueConsultaService.cs`
- `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoque/Consultas/LocalizacaoEstoqueConsultaContracts.cs`
- `BACKEND/PRPA/App.Service/DTOs/LocalizacaoEstoque/LocalizacaoEstoqueCreateDto.cs`
- `BACKEND/PRPA/App.Service/DTOs/LocalizacaoEstoque/LocalizacaoEstoqueUpdateDto.cs`
- `BACKEND/PRPA/App.Service/DTOs/LocalizacaoEstoque/LocalizacaoEstoqueReadDto.cs`
- `BACKEND/PRPA/App.Service/Validators/LocalizacaoEstoqueValidator.cs`
- `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`
- `BACKEND/PRPA/App.Domain.Tests/LocalizacaoEstoqueLegacyScenarios.cs`
- `FRONTEND/src/app/application/cadastro/localizacaoestoque/models/localizacaoestoque.interface.ts`
- `FRONTEND/src/app/application/cadastro/localizacaoestoque/services/localizacaoestoque.service.ts`
- `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/cadlocalizacaoestoque/cadlocalizacaoestoque.component.ts`
- `FRONTEND/src/app/application/cadastro/localizacaoestoque/components/listlocalizacaoestoque/listlocalizacaoestoque.component.ts`

Esta AS-0008 nao implementa codigo, migration, script SQL, endpoint, dependencia ou alteracao de banco. Aprovada em 2026-07-31 para documentar a decisao arquitetural e encaminhar a DL-0042.
