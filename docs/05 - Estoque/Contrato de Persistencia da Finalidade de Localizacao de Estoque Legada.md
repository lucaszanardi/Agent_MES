# Contrato de Persistencia da Finalidade de Localizacao de Estoque Legada

## 1. Objetivo

Especificar o contrato tecnico de persistencia para a finalidade configurada do legado `LocalizacaoEstoque`/`CLOCALIZACAOESTOQUE`, conforme aprovado pela DL-0042 e pela AS-0008.

Este documento nao implementa persistencia concreta. Ela define a coluna nova, o enum correspondente, a regra de backfill, as restricoes de rollback e a estrategia de validacao pos-migration, orientando uma etapa posterior de migration EF Core + MySQL/Pomelo a ser realizada apenas apos validacao humana.

## 2. Escopo

Incluido:

- nova coluna `finalidadelocalizacao` em `CLOCALIZACAOESTOQUE`;
- enum `FinalidadeLocalizacao` em `App.Domain`;
- propriedade `Finalidade` em `LocalizacaoEstoque`;
- desenho da migration aditiva e do backfill;
- regras de validacao pos-migration;
- estrategia de rollback.
- restricoes do backfill (nao atingir outras tabelas do legado nem a nova vertical).

Fora de escopo:

- migration executavel;
- DbContext/mappings EF Core concretos;
- repository, service, controller, endpoint e frontend;
- alteracao em `CLOCALDEESTOQUE`, `UnidadeLogistica`, `MovimentacaoDeEstoque`, `SaldoEstoque`;
- alteracao em `CALMOXARIFADO`, `CAREAESTOQUE`, `CTIPOLOCALIZACAO`;
- trigger, indice novo (nao necessario inicialmente) ou FK nova;
- sincronizacao automatica entre legado e nova vertical.

## 3. Fontes e rastreabilidade

| Fonte | Uso nesta especificacao |
|---|---|
| AS-0008 | Decisao arquitetural de origem. |
| DL-0042 | Decisao aprovada que formaliza a regra, a excecao ao congelamento e os criterios de conclusao. |
| DL-0001 | Decisao hierarquica de enderecamento (complementada, nao substituida). |
| DL-0004 | Ciclo de vida de localizacoes (compativel). |
| DL-0041 | Congelamento semantico do legado (excecao controlada declarada por DL-0042). |
| `BACKEND/PRPA/App.Domain/Entities/PRPA/LocalizacaoEstoque.cs` | Entidade legada que recebera a nova propriedade. |
| `BACKEND/PRPA/App.Infra.Data/Mapping/LocalizacaoEstoqueConfig.cs` | Mapping atual de `CLOCALIZACAOESTOQUE`. |
| `BACKEND/PRPA/App.Infra.Data/Context/ProjetoContext.cs` | DbContext com `DbSet<LocalizacaoEstoque>`. |
| `BACKEND/PRPA/App.Infra.Data/Migrations/ProjetoContextModelSnapshot.cs` | Snapshot atual da tabela. |
| `BACKEND/PRPA/App.Service/Services/LocalizacaoEstoqueServices.cs` | Servico que hoje calcula a classificacao em runtime. |
| `BACKEND/PRPA/App.Infra.Data/Persistence/LocalizacaoEstoque/LocalizacaoEstoqueConsultaService.cs` | Consulta paginada legada que replica a regra. |

## 4. Ambiente de persistencia

| Item | Padrao | Aplicacao |
|---|---|---|
| Banco | MySQL | Provider compativel. |
| Provider EF | Pomelo.EntityFrameworkCore.MySql 8.0.0 | smallint mapeado para `int`/enum. |
| Tabela alvo | `CLOCALIZACAOESTOQUE` | Legada, congelada semantica per DL-0041 com excecao controlada declarada nesta DL-0042. |

## 5. Contrato da coluna

| Item | Valor |
|---|---|
| Tabela | `CLOCALIZACAOESTOQUE` |
| Coluna nova | `finalidadelocalizacao` |
| Propriedade C# | `Finalidade` |
| Enum C# | `FinalidadeLocalizacao` |
| Namespace do enum sugerido | `App.Domain.Enums` (ou `App.Domain` se ja adotado) |
| Valores do enum | `Estrutural = 1`, `Armazenagem = 2` |
| Tipo SQL | `smallint` |
| Nulabilidade | `NOT NULL` |
| Default | `1` (Estrutural) |
| Comentario/descricao | "Finalidade configurada pelo operador; usada pela regra de classificacao efetiva de LocalizacaoEstoque. Veja DL-0042." |
| Ordem na coluna | Apos `bloqueada` (apenas por legibilidade; nao funcional) |
| Aplicar a historico? | Sim; todos os registros existentes recebem backfill |

## 6. Enum C#

```text
public enum FinalidadeLocalizacao
{
    Estrutural = 1,
    Armazenagem = 2
}
```

## 7. Materializacao EF Core (orientativa, nao implementada aqui)

Resumo do que a migration aditiva devera conter (a ser criado em etapa posterior autorizada):

- `ALTER TABLE CLOCALIZACAOESTOQUE ADD finalidadelocalizacao smallint NOT NULL DEFAULT 1;`
- Backfill (secao 9) executado preferencialmente como parte da mesma migration.
- Atualizacao do `ProjetoContextModelSnapshot.cs` para incluir a coluna.
- Mapping em `LocalizacaoEstoqueConfig` com `HasColumnType("smallint")`, `IsRequired()`, `HasDefaultValue(1)` e conversao para `FinalidadeLocalizacao`.

## 8. Classificacao efetiva (nao persistida)

A classificacao efetiva (`ESTRUTURAL`, `ARMAZENA`, `BLOQUEADO`) permanecera calculada em runtime. Nenhum campo extra sera persistido para armazenar esse estado. A regra e a definida em DL-0042:

1. `bloqueada=true` -> `BLOQUEADO`.
2. Possui filhos -> `ESTRUTURAL`.
3. Folha com `finalidadelocalizacao=Armazenagem` -> `ARMAZENA`.
4. Folha com `finalidadelocalizacao=Estrutural` -> `ESTRUTURAL`.

## 9. Regra de backfill proposta

O backfill e determinado pela classificacao atual em runtime (nao persistida) e sera aplicado na mesma migration aditiva, apos a criacao da coluna com default `1`. Em SQL conceitual:

```text
UPDATE CLOCALIZACAOESTOQUE L
LEFT JOIN CTIPOLOCALIZACAO T
  ON T.Id = L.tipolocalizacaoid
SET L.finalidadelocalizacao = 2 /* Armazenagem */
WHERE L.bloqueada = 0
  AND NOT EXISTS (
      SELECT 1
      FROM CLOCALIZACAOESTOQUE F
      WHERE F.localizacaopaiid = L.Id
  )
  AND T.permitearmazenagem = 1;
```

Apos essa atualizacao, todas as demais linhas permanecem com `finalidadelocalizacao = 1` (Estrutural), cobrindo:

| Categoria atual (em runtime) | `finalidadelocalizacao` apos backfill | Justificativa |
|---|---|---|
| Folhas `ARMAZENA` (`!bloqueada`, sem filhos, `permitearmazenagem=true`) | `Armazenagem` (2) | Preserva o comportamento atual para esses locais. |
| Folhas `REQUER_FILHO` (`!bloqueada`, sem filhos, `permitearmazenagem=false`) | `Estrutural` (1) | Estado deixa de existir; folha estrutural. |
| Locais com filhos (`ESTRUTURAL` em runtime) | `Estrutural` (1) | Default conservador; nao promove pai a armazenador sem intencao explicita do operador. |
| Bloqueados (`BLOQUEADO` em runtime) | `Estrutural` (1) | `bloqueada=true` continua prioridade absoluta; `finalidade` fica `Estrutural`. |
| Folhas sem `TipoLocalizacao` | (nao ocorre) | FK NOT NULL com validador obriga `tipolocalizacaoid`. |

## 10. Condicionantes do backfill

- Sera executado apenas apos aprovacao explicita e revisao humana do script incremental.
- Nao sera aplicado nesta etapa documental.
- Devera ser revisado em script incremental versionado, com inspecao em diretorio ignorado quando aplicavel.
- Nao podera alterar `CLOCALDEESTOQUE`.
- Nao podera alterar `UnidadeLogistica`.
- Nao podera alterar `MovimentacaoDeEstoque`.
- Nao podera alterar outras tabelas do legado (`CALMOXARIFADO`, `CAREAESTOQUE`, `CTIPOLOCALIZACAO`).
- Nao cria trigger; nenhuma tabela adicional e criada.
- O `Id` dos registros nao e alterado; a migracao opera apenas sobre a nova coluna.
- Pode ser executada em manutencao programada por ser uma migration aditiva com backfill controlado.

## 11. Indices, FK e triggers

| Item | Decisao |
|---|---|
| Indice novo | Nao necessario inicialmente. Avaliar futuramente se aparecerem filtros por `finalidadelocalizacao` em alta frequencia. |
| FK nova | Nao ha. `finalidadelocalizacao` e scalar, enum smallint. |
| Trigger | Proibida. O sistema nao usara triggers para manter ou sincronizar classificacao ou finalidade. |
| Constraint CHECK | Opcional a nivel de banco: `CHECK (finalidadelocalizacao IN (1, 2))`. Recomendado para defesa em profundidade, mas depende da versao real do MySQL, pode ser omitido da primeira migration se a compatibilidade nao estiver comprovada e nao substitui a validacao da aplicacao. |

## 12. Rollback

O rollback conceitual consiste em:

1. Migration descendente que remove a coluna `finalidadelocalizacao`:
   - `ALTER TABLE CLOCALIZACAOESTOQUE DROP COLUMN finalidadelocalizacao;`
2. Atualizacao do snapshot EF (`ProjetoContextModelSnapshot.cs`).
3. Reverter o codigo (entidade, mapping, DTOs, validator, controller, services, frontend) ao estado anterior, com retorno da producao de `REQUER_FILHO` e da classificacao derivada de `TipoLocalizacao.permitearmazenagem`.

### 12.1 Riscos do rollback

- Perda dos valores de `finalidadelocalizacao` configurados manualmente pelos operadores apos a aplicacao original.
- Retorno da divergencia semantica entre grid e arvore (`REQUER_FILHO` voltando a aparecer apenas na arvore).
- Retorno da rigidez de classificacao de folhas derivada unicamente de `TipoLocalizacao.permitearmazenagem`.
- Necessidade de limpar/reverter mensagens, badges e frontends que faziam uso da `finalidade`.
- Auditoria previa para comunicar a operacao e mapear locais que tinham `Finalidade=Armazenagem` e voltariam a `Estrutural` (ou, em caso de rollback parcial, sera preciso decidir politica).

O rollback deve ser tratado como evento controlado, com janela de manutencao e comunicacao previa.

## 13. Estrategia de validacao pos-migration

Antes de liberacao de uso:

1. Consulta read-only auditando classificacao pre-migration (`GetArvorePorAreaAsync` por area) para baseline.
2. Aplicar migration aditiva + backfill em ambiente de homologacao.
3. Comparar classificacao pos-migration com baseline:
   - `ARMAZENA` deve continuar `ARMAZENA` (Finalidade=Armazenagem) em folhas que estavam nesse estado;
   - `ESTRUTURAL` em locais com filhos deve permanecer `ESTRUTURAL` (finalidade persistida pode ser `Estrutural` por default);
   - `BLOQUEADO` deve permanecer `BLOQUEADO`;
   - Folhas antes `REQUER_FILHO` devem passar a `ESTRUTURAL` (nao deve haver mais `REQUER_FILHO`).
4. Testes automatizados do dominio (`LocalizacaoEstoqueLegacyScenarios`) rodando integralmente e verdes (criterios de conclusao da DL-0042).
5. Testes manuais do workspace continuo (criar filho/irmao/raiz, salvar/excluir sem retorno a grid, preservacao de contexto, recuperacao por query params, descarte de formulario sujo).
6. Validacao manual dos cenarios principais da regra (itens 1 a 14 da secao de criterios de aceitacao).
7. Aprovacao pelos responsaveis pela validacao humana (PO, backend, frontend, DBA) antes de promocao para producao.

## 14. Criterios de aceitacao da persistencia

1. Coluna `finalidadelocalizacao` existe em `CLOCALIZACAOESTOQUE` como `smallint NOT NULL DEFAULT 1`.
2. Map EF tem `IsRequired`, `HasDefaultValue(1)` e conversao para enum `FinalidadeLocalizacao`.
3. Migration aditiva inclui backfill conforme secao 9.
4. Nenhuma coluna, FK, indice ou trigger e criada em outra tabela do legado ou da nova vertical.
5. A classificacao efetiva nao e persistida em nenhuma coluna adicional.
6. Rollback (remocao da coluna) e descrito em migration descendente.
7. Pos-migration, `REQUER_FILHO` nao aparece em nenhum DTO, arvore, grid ou frontend.
8. Grid e arvore produzem a mesma classificacao para o mesmo local.

## 15. Relacao com contratos adjacentes

- `MES-ProjectBook/docs/05 - Estoque/Contrato de Persistencia da Primeira Vertical de Estoque.md` define persistencia de `LocalDeEstoque`/`CLOCALDEESTOQUE`, `CUNIDADELOGISTICA`, `CMOVIMENTACAODEESTOQUE`, idempotencia, outbox e reserva transacional. Este contrato de finalidade do legado nao se sobrepoe nem altera aquele; a excecao ao congelamento e pontual e restrita a `CLOCALIZACAOESTOQUE.finalidadelocalizacao`.
- `MES-ProjectBook/docs/12 - Decision Log/DL-0041` aprova o congelamento semantico do legado; `DL-0042` declara excecao controlada aditiva a essa regra, descrita aqui em detalhes de persistencia.

Este documento nao implementa migration, nao cria script SQL executavel e nao altera banco de dados. Sua aplicacao requer validacao humana previa conforme DL-0042.
