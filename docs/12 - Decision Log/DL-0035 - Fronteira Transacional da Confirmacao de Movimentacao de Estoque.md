# DL-0035 - Fronteira Transacional da Confirmacao de Movimentacao de Estoque

## Codigo

DL-0035

## Status

Aprovado

## Contexto

A confirmacao de uma MovimentacaoDeEstoque altera o estado da movimentacao e a posicao atual da Unidade Logistica. A AS-0007 definiu que essa alteracao precisa ser atomica para evitar estado parcialmente confirmado.

## Problema

Se a confirmacao da movimentacao, a atualizacao da UL, o historico, a idempotencia e os eventos forem gravados em transacoes separadas, o sistema pode registrar uma movimentacao concluida sem mover a UL, mover a UL sem historico, ou publicar evento sem persistencia consistente.

## Decisao

Na confirmacao da movimentacao, uma unica transacao local devera conter:

1. validacao das versoes esperadas;
2. confirmacao da `MovimentacaoDeEstoque`;
3. atualizacao da posicao da `UnidadeLogistica`;
4. incremento das versoes dos agregados alterados;
5. registro do historico transacional;
6. atualizacao do registro de idempotencia;
7. insercao dos eventos na outbox.

Nao entram na mesma transacao:

- publicacao no broker;
- chamadas ERP;
- chamadas HTTP externas;
- atualizacao de sistemas externos;
- analytics;
- notificacoes;
- envio de e-mail;
- atualizacao direta de saldo legado como fonte primaria.

Projecoes:

- a posicao transacional da UL deve ser atualizada sincronicamente na propria UL;
- historico transacional minimo deve ser gravado na mesma transacao;
- projecoes de leitura podem ser atualizadas de forma assincrona via outbox;
- se ainda nao houver publicador/outbox worker, a primeira implementacao pode atualizar projecoes criticas localmente na mesma transacao, desde que sejam tratadas como read models e possam ser reconstruidas;
- `SaldoEstoque` legado nao participa como fonte de verdade e nao deve bloquear a confirmacao.

## Alternativas

- Confirmar movimentacao e atualizar UL em transacoes separadas: rejeitado.
- Atualizar apenas projecao e reconstruir depois: rejeitado para posicao atual transacional.
- Incluir publicacao externa na transacao: rejeitado.
- Usar transacao local com outbox: aprovado.

## Consequencias

### Consequencias positivas

- Evita estado parcialmente confirmado.
- Garante historico e evento persistidos com o estado de dominio.
- Mantem compatibilidade com o `UnitOfWork` e EF Core.
- Evita dependencia de transacao distribuida.

### Consequencias negativas

- A transacao fica maior que um CRUD simples.
- Exige services/handlers especificos, nao apenas service generico.
- Exige modelagem de outbox e idempotencia antes da API publica.

### Riscos e compromissos

- Incluir projecoes demais na transacao pode degradar desempenho.
- Falha no publicador de outbox exige monitoramento e reprocessamento.
- Atualizar saldo legado sem cuidado pode reintroduzir saldo como autoridade.

## Compatibilidade tecnica

- EF Core e MySQL suportam transacao local.
- O `UnitOfWork.ExecuteAsync(Func<Task>)` atual cria transacao e faz commit/rollback.
- Nao ha transacao distribuida configurada e ela nao e necessaria para a primeira slice.
- Nao ha broker ou worker identificado; a outbox pode ser persistida primeiro e publicada em etapa posterior.

## Referencias

- `../15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `DL-0025 - Movimentacao de Estoque como Processo Operacional.md`
- `DL-0029 - Autoridade de Dominio Auditoria e Eventos.md`
- `DL-0033 - Estrategia de Idempotencia dos Comandos da Primeira Vertical de Estoque.md`
- `DL-0034 - Concorrencia Otimista e Exclusividade de Movimentacao de Unidade Logistica.md`

## Relacao com AS-0007

Consolida a decisao pendente de fronteira transacional registrada nas secoes 11, 14, 17, 18, 21, 22, 24 e 25 da AS-0007.
