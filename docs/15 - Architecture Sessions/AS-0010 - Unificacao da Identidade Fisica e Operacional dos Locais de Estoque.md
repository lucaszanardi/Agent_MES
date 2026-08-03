# AS-0010 - Unificacao da Identidade Fisica e Operacional dos Locais de Estoque

## Metadados

| Campo | Valor |
|---|---|
| Status | Concluida |
| Data | 2026-08-03 |
| Origem | Analise final da unificacao entre `LocalizacaoEstoque` e `LocalDeEstoque` |
| Decision Log relacionado | DL-0044 |
| Relaciona-se com | AS-0004, AS-0005, AS-0007, AS-0008, AS-0009, DL-0038, DL-0041, DL-0042, DL-0043 |
| Fora de escopo | Alteracao de backend/frontend, migration, database update, DDL, remocao fisica de tabela e definicao final de nomes fisicos sem auditoria tecnica |

## 1. Contexto

O sistema possui dois modelos de endereco de estoque.

Modelo legado fisico:

- entidade `LocalizacaoEstoque`;
- tabela `CLOCALIZACAOESTOQUE`;
- `Almoxarifado`;
- Area de Estoque;
- hierarquia pai-filho;
- Tipo de Localizacao;
- capacidade;
- bloqueio;
- permissoes de entrada, saida e producao;
- finalidade configurada: `Estrutural` ou `Armazenagem`;
- classificacao efetiva: `ESTRUTURAL`, `ARMAZENA` ou `BLOQUEADO`.

Modelo operacional da nova vertical:

- entidade `LocalDeEstoque`;
- tabela `CLOCALDEESTOQUE`;
- `PlantId`;
- `WarehouseId`;
- `Codigo`;
- `Status`;
- `Version`;
- referencias em `UnidadeLogistica` e `MovimentacaoDeEstoque`.

Estado real informado para a decisao:

| Tabela | Registros |
|---|---:|
| `CLOCALIZACAOESTOQUE` | 18 |
| `CLOCALDEESTOQUE` | 0 |
| `CUNIDADELOGISTICA` | 0 |
| `CMOVIMENTACAODEESTOQUE` | 0 |

Nao existem dados operacionais que precisem ser migrados.

## 2. Problema

A coexistencia de `LocalizacaoEstoque` e `LocalDeEstoque` como identidades operacionais distintas criaria duplicidade de endereco, risco de divergencia e necessidade de publicacao/sincronizacao entre cadastros.

Essa duplicidade afetaria codigo, status, bloqueio, escopo de Planta/Armazem, rastreabilidade, suporte, auditoria e experiencia do usuario.

## 3. Modelo atual

| Modelo | Identidade | Tabela | Papel atual |
|---|---|---|---|
| Legado fisico | `LocalizacaoEstoque` | `CLOCALIZACAOESTOQUE` | Cadastro fisico real, hierarquia, bloqueio, finalidade e elegibilidade. |
| Nova vertical | `LocalDeEstoque` | `CLOCALDEESTOQUE` | Identidade operacional planejada, ainda sem dados operacionais. |

## 4. Duplicidades identificadas

- Codigo de endereco.
- Identidade de armazem/warehouse.
- Escopo de Planta.
- Status/bloqueio.
- Elegibilidade para armazenagem.
- Rastreabilidade do endereco.
- Origem e destino de movimentacoes.
- Posicao atual de Unidade Logistica.

## 5. Impactos de usabilidade

A manutencao de duas identidades exigiria que o usuario cadastrasse ou publicasse o mesmo endereco em mais de um lugar. A decisao aprovada busca permitir que o operador configure o endereco uma unica vez.

## 6. Alternativas avaliadas

| Alternativa | Descricao | Avaliacao |
|---|---|---|
| A | Manter `LocalDeEstoque` e criar publicacao operacional | Rejeitada. Mantem fluxo adicional, risco de atraso e divergencia. |
| B | Manter duas tabelas com sincronizacao automatica | Rejeitada. Aumenta complexidade e cria duplicidade tecnica permanente. |
| C | Usar codigo como correlacao sem FK | Rejeitada. Fragil para rastreabilidade e concorrencia. |
| D | Unificar a identidade em `LocalizacaoEstoque` | Aprovada. Menor complexidade, melhor usabilidade e fonte unica de verdade. |
| E | Substituir o legado por `LocalDeEstoque` | Rejeitada para o momento. Exigiria recriar capacidades fisicas ja existentes no legado. |

## 7. Decisao

`LocalizacaoEstoque` sera a identidade unica fisica e operacional dos enderecos de estoque.

`CLOCALIZACAOESTOQUE` sera a fonte de verdade para:

- estrutura hierarquica;
- identificacao do endereco;
- elegibilidade para armazenagem;
- posicionamento de Unidade Logistica;
- origem e destino de movimentacoes;
- bloqueio;
- permissoes operacionais;
- capacidade;
- rastreabilidade do endereco.

`LocalDeEstoque` e `CLOCALDEESTOQUE` deixam de ser utilizados como identidade operacional independente.

Nao havera:

- publicacao de `LocalizacaoEstoque` para `LocalDeEstoque`;
- sincronizacao entre duas tabelas de endereco;
- duplicacao de codigo;
- duplicacao de `WarehouseId`;
- duplicacao de `PlantId`;
- cadastro operacional separado de local.

## 8. Modelo conceitual final

```text
Planta
`-- Almoxarifado
    `-- Area de Estoque
        `-- LocalizacaoEstoque
            |-- UnidadeLogistica
            `-- MovimentacaoDeEstoque
```

Fluxo operacional desejado:

```text
Planta -> Almoxarifado -> Area de Estoque -> LocalizacaoEstoque -> UnidadeLogistica -> MovimentacaoDeEstoque
```

## 9. Elegibilidade operacional

Nem toda `LocalizacaoEstoque` sera operacionalmente elegivel.

Uma Unidade Logistica somente podera ser posicionada em uma localizacao cuja classificacao efetiva seja `ARMAZENA`.

| Condicao | Classificacao efetiva | Pode receber UL | Pode ser origem/destino |
|---|---|---|---|
| Localizacao bloqueada | `BLOQUEADO` | Nao | Nao |
| Localizacao com filhos | `ESTRUTURAL` | Nao | Nao |
| Folha com `Finalidade=Estrutural` | `ESTRUTURAL` | Nao | Nao |
| Folha com `Finalidade=Armazenagem` | `ARMAZENA` | Sim, respeitando permissoes e capacidade | Sim, respeitando permissoes e regras operacionais |

A profundidade da localizacao nao define elegibilidade. Podem armazenar, conforme configuracao: Rua, Bloco, Coluna, Andar, Posicao, Buffer, Supermercado de linha ou outra localizacao folha.

## 10. Regras de entrada e saida

Para receber uma Unidade Logistica:

- localizacao existe;
- classificacao efetiva = `ARMAZENA`;
- bloqueada = false;
- `permiteentrada = true`;
- capacidade disponivel, quando aplicavel;
- pertence ao escopo valido de Planta e Almoxarifado.

Para fornecer uma Unidade Logistica:

- localizacao existe;
- classificacao efetiva = `ARMAZENA`;
- bloqueada = false;
- `permitesaida = true`;
- contem a Unidade Logistica;
- nao possui impedimento operacional.

Para uso produtivo, deve-se respeitar `permiteproducao`. O vinculo com linhas, buffers e supermercados sera definido pelo modulo de Producao.

## 11. Alteracoes hierarquicas

Nao sera permitido transformar uma localizacao operacionalmente armazenadora em estrutural enquanto houver dependencias operacionais.

Antes de adicionar filho abaixo de uma localizacao `ARMAZENA`, o backend devera validar:

- nenhuma Unidade Logistica posicionada diretamente;
- nenhum saldo diretamente associado;
- nenhuma movimentacao ativa;
- nenhuma reserva;
- nenhum inventario em andamento;
- nenhum bloqueio operacional incompativel.

Se houver dependencia, a alteracao hierarquica devera ser rejeitada. Se nao houver dependencia, o filho podera ser criado, o pai passara a `ESTRUTURAL`, a finalidade persistida do pai podera ser preservada e, ao perder o ultimo filho, o pai voltara a seguir sua finalidade persistida.

## 12. Unidade Logistica

`CUNIDADELOGISTICA` devera referenciar diretamente `LocalizacaoEstoque`.

Nome logico recomendado: `LocalizacaoEstoqueId`.

A Unidade Logistica nao podera referenciar localizacao inexistente, `ESTRUTURAL` ou `BLOQUEADA`.

A posicao atual da Unidade Logistica sera determinada por sua referencia a `LocalizacaoEstoque`.

A nulabilidade em estados como recebimento ainda nao enderecado, material em transito, producao e expedicao sera detalhada em contrato de implementacao posterior.

## 13. Movimentacoes

`MovimentacaoDeEstoque` devera usar diretamente:

- `LocalizacaoOrigemId`;
- `LocalizacaoDestinoId`.

Ambos deverao referenciar `LocalizacaoEstoque`.

O backend devera validar novamente a elegibilidade no momento de solicitacao, confirmacao e cancelamento, quando implementado. Nao se deve confiar somente na classificacao exibida pelo frontend.

A movimentacao devera preservar idempotencia, correlacao, causalidade, concorrencia otimista, transactional outbox, reserva transacional e rastreabilidade.

## 14. Concorrencia e versionamento

`LocalizacaoEstoque` precisara de mecanismo de concorrencia compativel com o papel operacional consolidado.

A versao operacional podera ser incorporada a propria `LocalizacaoEstoque`. Nao devera ser criada outra identidade apenas para manter `Version`.

A forma fisica da coluna e do token de concorrencia sera definida no contrato de persistencia futuro. Nenhuma coluna e criada nesta AS.

## 15. Status operacional

`BLOQUEADO` sera derivado do bloqueio da `LocalizacaoEstoque`.

`ESTRUTURAL` e `ARMAZENA` sao classificacoes funcionais.

Um eventual conceito de `Desativado` devera ser modelado explicitamente apenas se houver requisito real. Nao se deve criar status duplicado sem necessidade.

Ativo/inativo podera depender tambem de Planta, Almoxarifado e Area.

## 16. Impacto em Planta e Almoxarifado

Mantem-se a DL-0043:

- `PlantId` sera derivado pelo Almoxarifado;
- `WarehouseId` corresponde ao `AlmoxarifadoId`;
- `PlantId` nao sera repetido em `LocalizacaoEstoque`;
- `WarehouseId` nao sera repetido em `LocalizacaoEstoque`;
- `LocalizacaoEstoque` ja possui `AlmoxarifadoId`;
- o escopo completo sera derivado por `LocalizacaoEstoque -> Almoxarifado -> Planta`;
- nao havera cadastro separado de Warehouse.

## 17. Impacto em dados

Como `CLOCALDEESTOQUE`, `CUNIDADELOGISTICA` e `CMOVIMENTACAODEESTOQUE` estao vazias, nao ha dados operacionais para migrar.

A remocao fisica de `CLOCALDEESTOQUE` somente podera ocorrer em migration futura, apos refatoracao completa e validacao humana.

## 18. Impacto em backend

Impactos futuros previstos:

- refatorar dominio para usar `LocalizacaoEstoque` como identidade operacional;
- ajustar repositories, handlers, services, validators e controllers;
- alterar referencias de UL e movimentacao para `LocalizacaoEstoque`;
- preservar idempotencia, concorrencia, reserva transacional e outbox;
- adicionar validacoes de elegibilidade no backend.

Nenhum backend foi alterado nesta AS.

## 19. Impacto em frontend

Impactos futuros previstos:

- simplificar telas e remover a ideia de publicacao para `LocalDeEstoque`;
- reutilizar o cadastro/mapa de `LocalizacaoEstoque` como origem operacional;
- garantir que o frontend exiba elegibilidade, mas nao seja autoridade da regra;
- ajustar contratos HTTP apos a refatoracao backend.

Nenhum frontend foi alterado nesta AS.

## 20. Impacto em migrations

Impactos futuros previstos:

- ajustar FKs de `CUNIDADELOGISTICA` e `CMOVIMENTACAODEESTOQUE` para `CLOCALIZACAOESTOQUE`;
- preservar tabelas de reserva, idempotencia e outbox;
- avaliar mecanismo de concorrencia em `CLOCALIZACAOESTOQUE`;
- remover `CLOCALDEESTOQUE` somente ao final, apos refatoracao, testes, revisao SQL e validacao humana.

Nenhuma migration foi criada nesta AS.

## 21. Riscos

- Quebra de invariantes se UL ou movimentacao aceitarem local estrutural ou bloqueado.
- Perda de garantias de idempotencia, concorrencia, reserva ou outbox se a refatoracao for incompleta.
- Remocao prematura de `CLOCALDEESTOQUE` antes de ajustar dominio, FKs e testes.
- Decisao silenciosa sobre estados transitorios de UL sem contrato posterior.

## 22. Criterios de aceitacao

- Usuario cadastra endereco uma unica vez.
- Nao existe publicacao duplicada.
- `ARMAZENA` e elegivel.
- `ESTRUTURAL` nao e elegivel.
- `BLOQUEADO` nao e elegivel.
- UL nunca fica em localizacao estrutural.
- Movimentacao nunca usa local estrutural ou bloqueado.
- Planta e derivada pelo Almoxarifado.
- Warehouse e o proprio Almoxarifado.
- Concorrencia, idempotencia, outbox e reservas sao preservadas.
- Alteracao hierarquica com dependencia operacional e bloqueada.
- `CLOCALDEESTOQUE` nao e removida antes da refatoracao completa.

## 23. Estrategia de transicao

A transicao sera incremental:

1. refatorar dominio sem remover tabela;
2. ajustar repositories e handlers;
3. ajustar contratos HTTP;
4. ajustar testes;
5. gerar migration incremental;
6. revisar SQL;
7. ajustar frontend;
8. validar banco vazio operacional;
9. remover `CLOCALDEESTOQUE`;
10. atualizar documentacao factual.

## 24. Rollback conceitual

Enquanto `CLOCALDEESTOQUE` nao for removida fisicamente, o rollback conceitual ainda e possivel por documentacao e por suspensao da refatoracao tecnica. Apos migration de remocao, rollback exigira nova migration controlada e decisao humana.

## 25. Decisoes tecnicas pendentes

- Nulabilidade de `LocalizacaoEstoqueId` em estados transitorios de Unidade Logistica.
- Nomes fisicos definitivos de colunas e FKs.
- Token de concorrencia em `CLOCALIZACAOESTOQUE`.
- Estrategia fisica para saldo direto por localizacao.
- Regra detalhada de capacidade.
- Contratos HTTP definitivos.
- Ordem exata de migrations e scripts SQL.
- Testes de regressao obrigatorios para a refatoracao.

## 26. Conclusao

A alternativa D esta aprovada: unificar a identidade fisica e operacional em `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`.

`LocalDeEstoque`/`CLOCALDEESTOQUE` sera descontinuada como identidade operacional independente, mas sua remocao fisica e futura, incremental e condicionada a refatoracao completa.