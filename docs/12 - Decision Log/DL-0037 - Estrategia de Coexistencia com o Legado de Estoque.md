# DL-0037 - Estrategia de Coexistencia com o Legado de Estoque

## Codigo

DL-0037

## Status

Aprovado

## Contexto

O codigo atual possui entidades, services, controllers, endpoints e telas de estoque orientados a CRUD, incluindo `MovimentoEstoque`, `TransferenciaEstoque`, `SaldoEstoque` e `LocalizacaoEstoque`.

DL-0031 definiu que o legado e insumo de implementacao, nao autoridade de dominio.

## Problema

Promover o legado diretamente para a nova vertical pode consolidar saldo como fonte primaria, movimento como evento/processo misturado, endpoints anonimos e ausencia de idempotencia, concorrencia e historico imutavel.

## Decisao

A coexistencia com o legado sera incremental, usando estrategia de strangler pattern e camada anticorrupcao quando necessario.

Regras:

- novos comandos de movimentacao devem usar o novo dominio da primeira vertical;
- `MovimentoEstoque` legado nao sera Aggregate Root da nova vertical;
- `TransferenciaEstoque` legada nao sera base da primeira slice;
- `SaldoEstoque` legado sera tratado como consulta/projecao/reconciliacao, nao como fonte primaria;
- `LocalizacaoEstoque` podera ser adaptado como fonte de LocalDeEstoque, sem virar AR concorrente;
- controllers legados nao devem atualizar o novo dominio diretamente;
- endpoints legados podem permanecer temporariamente para compatibilidade, mas nao recebem autoridade sobre a nova vertical;
- nao sera usada escrita dupla nao transacional;
- dados legados podem ser expostos por adaptadores/read models quando necessario;
- migracoes de dados e descontinuacao serao graduais e dependerao de decisao futura;
- metricas de uso do legado devem ser previstas para orientar desativacao.

Classificacao inicial:

| Componente legado | Estrategia |
|---|---|
| `LocalizacaoEstoque` | Adaptar/encapsular como fonte de `LocalDeEstoque`. |
| `MovimentoEstoque` | Manter temporariamente; usar como historico/projecao legada ou reconciliacao; nao usar como AR. |
| `TransferenciaEstoque` | Investigar/adaptar futuramente; fora da primeira slice. |
| `TransferenciaEstoqueItem` | Investigar; fora da primeira slice de uma unica UL. |
| `SaldoEstoque` | Encapsular como leitura legada/projecao; nao fonte primaria. |
| Controllers CRUD de estoque | Manter temporariamente; nao reaproveitar para comandos criticos. |
| Services genericos | Reutilizar somente quando nao violar invariantes; criar application services especificos para comandos. |
| Repositories genericos | Reutilizar parcialmente; adicionar repositorios/metodos especificos para agregados. |
| DTOs create/update | Substituir por contratos de comando na nova vertical. |
| Telas frontend legadas | Manter temporariamente; adaptar ou substituir gradualmente. |
| Relatorios legados | Manter temporariamente e reconciliar com novas projecoes. |
| Integracoes legadas | Investigar; nao acoplar ao novo dominio sem adaptador. |

## Alternativas

- Big bang substituindo todo o estoque: rejeitado.
- Adaptar cegamente o legado: rejeitado.
- Escrever simultaneamente no legado e no novo dominio sem transacao: rejeitado.
- Strangler incremental com anticorrupcao: aprovado.

## Consequencias

### Consequencias positivas

- Reduz risco de regressao operacional.
- Permite aproveitar consultas e cadastros existentes.
- Protege o novo dominio contra semantica incorreta do legado.
- Permite migracao gradual.

### Consequencias negativas

- Durante a transicao havera dois modelos conceituais coexistindo.
- Sera necessario reconciliar leituras legadas e novas projecoes.
- Exige disciplina para nao reaproveitar controllers CRUD como comandos.

### Riscos e compromissos

- Divergencia entre legado e novo dominio pode confundir usuarios.
- Falta de metricas pode atrasar descontinuacao.
- Escrita dupla acidental pode gerar inconsistencia.

## Compatibilidade tecnica

- A stack atual permite manter endpoints legados e adicionar novos recortes sem renomear pastas existentes.
- EF Core permite mapear novas estruturas ao lado das tabelas legadas.
- O frontend Angular pode conviver com telas legadas e telas novas por rotas separadas.
- Controllers legados usam `[AllowAnonymous]`; novos endpoints da vertical nao devem repetir esse padrao.

## Referencias

- `../15 - Architecture Sessions/AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `../15 - Architecture Sessions/AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque.md`
- `DL-0031 - Codigo Legado de Estoque como Insumo de Implementacao.md`
- `DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
- `DL-0025 - Movimentacao de Estoque como Processo Operacional.md`

## Relacao com AS-0007

Consolida a decisao pendente de coexistencia com legado registrada nas secoes 6, 17, 18, 20, 22, 24 e 25 da AS-0007.
