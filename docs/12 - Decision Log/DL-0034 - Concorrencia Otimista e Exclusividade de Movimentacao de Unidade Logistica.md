# DL-0034 - Concorrencia Otimista e Exclusividade de Movimentacao de Unidade Logistica

## Codigo

DL-0034

## Status

Aprovado

## Contexto

A primeira vertical de Estoque movimentara uma unica Unidade Logistica entre dois Locais de Estoque. A AS-0004 e a AS-0005 definem que uma UL nao pode participar de duas movimentacoes ativas simultaneamente.

## Problema

Sem controle de concorrencia, duas requisicoes simultaneas podem confirmar a mesma movimentacao, criar duas movimentacoes ativas para a mesma UL ou confirmar uma movimentacao usando posicao de origem desatualizada.

## Decisao

A primeira vertical usara concorrencia otimista nos Aggregate Roots `UnidadeLogistica` e `MovimentacaoDeEstoque`.

Regras:

- os agregados terao campo conceitual `Version`;
- comandos mutaveis receberao `ExpectedVersion`;
- a persistencia devera verificar atomicamente a versao esperada;
- confirmacao incrementara a versao da `MovimentacaoDeEstoque` e da `UnidadeLogistica`;
- conflito de versao retornara erro explicito de conflito, sem retry automatico de dominio;
- retry automatico so sera permitido para falhas tecnicas transitorias comprovadas;
- posicao de origem deve ser validada contra a posicao transacional atual da UL, nao contra projecao;
- dupla confirmacao deve ser bloqueada por estado, versao e idempotencia;
- duas movimentacoes ativas incompatíveis para a mesma UL devem ser impedidas por constraint ou garantia transacional equivalente.

Conceito de movimentacao ativa na primeira slice:

```text
MovimentacaoDeEstoque em estado Solicitada.
```

Estados `Confirmada`, `Cancelada` e `Rejeitada` nao sao movimentacoes ativas.

Se o banco nao suportar indice unico filtrado por status ativo, a implementacao devera usar uma alternativa transacional equivalente, como:

- campo de controle na UL indicando movimentacao ativa;
- tabela auxiliar de ocupacao de movimentacao ativa por UL;
- constraint composta com estrategia de normalizacao de status ativo.

## Alternativas

- Confiar apenas em `SaldoEstoque`: rejeitado.
- Confiar apenas em validacao de frontend: rejeitado.
- Usar lock pessimista amplo por armazem: rejeitado por reduzir concorrencia sem necessidade.
- Serializar todo o estoque em fila unica: rejeitado.
- Usar optimistic concurrency com expected version e exclusividade por UL ativa: aprovado.

## Consequencias

### Consequencias positivas

- Protege a UL contra movimentacoes simultaneas incompatíveis.
- Evita confirmacao duplicada.
- Mantem compatibilidade com EF Core e MySQL.
- Permite conflito explicito e auditavel.

### Consequencias negativas

- Exige campo de versao nos novos agregados.
- Exige tratamento explicito de conflito na aplicacao e na API futura.
- Pode exigir alternativa caso MySQL/EF nao suporte indice filtrado diretamente.

### Riscos e compromissos

- Resolver conflito automaticamente pode mascarar problema operacional.
- Falta de testes concorrentes pode deixar corrida nao detectada.
- Constraint mal modelada pode bloquear movimentacoes historicas ja concluidas.

## Compatibilidade tecnica

- EF Core suporta concurrency tokens e tratamento de `DbUpdateConcurrencyException`.
- O `UnitOfWork` atual ja captura `DbUpdateConcurrencyException`, mas a nova vertical deve retornar erro de dominio/API mais especifico.
- MySQL suporta transacoes e constraints unicas compostas; indice filtrado pode exigir desenho alternativo.
- Nao ha versionamento nos agregados atuais de estoque; esta decisao se aplica aos novos agregados da vertical.

## Referencias

- `../15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `../15 - Architecture Sessions/AS-0004 - Arquitetura do Dominio de Estoque.md`
- `DL-0023 - Unidade Logistica como Agregado Fisico.md`
- `DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `DL-0032 - Definition of Ready para Retomada da Codificacao.md`

## Relacao com AS-0007

Consolida a decisao pendente de concorrencia registrada nas secoes 9, 10, 14, 16, 18, 21, 22, 24 e 25 da AS-0007.
