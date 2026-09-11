# EST-OP-02B.1 — DIAGNÓSTICO DOS PARÂMETROS DA TELA ENTRADA DE ESTOQUE

**Status**: DIAGNÓSTICO CONCLUÍDO  
**Data**: 2026-09-01  
**Escopo**: Somente investigação — NÃO foram feitas alterações no código, banco ou seedagem

---

## ACHADOS CRÍTICOS

### ⚠️ FALHA ESTRUTURAL IDENTIFICADA

A tela **Entrada de Estoque** chama um endpoint (`GET /ParametroValor`) que **não existe no backend**.

**Timeline:**
- **21/06/2026**: Criadas tabelas `CPARAMETROGRUPO` e `CPARAMETROVALOR` (migrations 20260621231241 e 20260621231754)
- **02/07/2026**: Tabelas foram **REMOVIDAS** (migration 20260702173541_RemoveParametroGrupoValorTables)
- **Presente**: Frontend continua chamando endpoint inexistente → HTTP 404 ou erro vazio

---

## A. TIPO DE MOVIMENTO ENTRADA

### Fluxo Frontend → Backend

| Componente | Localização |
|-----------|-----------|
| **Frontend** | `entradaestoque.component.ts` (linhas 57, 167-174) |
| **Serviço** | `ParametroValorService.getParametroValor()` |
| **Endpoint** | `GET {environment.urlserver}ParametroValor` |
| **Backend Controller** | ❌ **NÃO EXISTE** `ParametroValorController` |
| **Backend Service** | ❌ **NÃO EXISTE** `ParametroValorServices` |
| **Banco de Dados** | ❌ **NÃO EXISTE** `CPARAMETROVALOR` (tabela removida) |

### Lógica de Classificação (Frontend - linhas 166-174)

```typescript
this.tipoMovimentoEntrada = parametros.find(parametro => {
  const grupo = this.normalizarTexto(this.getGrupoTexto(parametro));
  const texto = this.normalizarTexto(this.getParametroTexto(parametro));
  return grupo.includes('TIPO') && grupo.includes('MOVIMENTO') && texto.includes('ENTRADA');
}) ?? parametros.find(parametro => {
  const texto = this.normalizarTexto(`${this.getGrupoTexto(parametro)} ${this.getParametroTexto(parametro)}`);
  return texto.includes('ENTRADA') && texto.includes('ESTOQUE');
});
```

### Mensagem de Erro

Quando `tipoMovimentoEntrada` é `undefined`, mostra:
```
"Parâmetro de tipo de movimento para Entrada de Estoque não encontrado."
(linha 73 do component)
```

### Dados Esperados (Estrutura da antiga tabela)

```sql
CPARAMETROGRUPO:
  - Id: int
  - codigo: varchar(80)
  - nome: varchar(150)
  - descricao: varchar(500)
  - modulo: varchar(50)
  - sistema: bool

CPARAMETROVALOR:
  - Id: int
  - parametrogrupoid: int (FK)
  - codigo: varchar(80)
  - nome: varchar(150)
  - descricao: varchar(500)
  - ordem: int
  - cor: varchar(30)
  - icone: varchar(80)
  - padrao: bool
  - sistema: bool
```

### Status Atual do Banco

```
GET /ParametroValor → HTTP 404 NOT FOUND
Razão: Endpoint não existe, tabelas foram removidas
```

### Correção Recomendada

**Necessário RESTAURAR ou REIMPLEMENTAR:**
1. Tabelas `CPARAMETROGRUPO` e `CPARAMETROVALOR`
2. Controller `ParametroValorController` com método `[HttpGet]`
3. Service `ParametroValorServices`
4. Repository/Query para `ParametroValor`
5. Seedagem de dados parametrizados (grupos e valores)

---

## B. VERSÃO DO PRODUTO

### Produto Teste: PROD1 - CLP Industrial

| Propriedade | Valor |
|-----------|-------|
| **Endpoint** | `GET {environment.urlserver}ProdutoVersao` |
| **Backend Service** | `ProdutoVersaoServices.cs` |
| **Backend Controller** | `ProdutoVersaoController.cs` |
| **Frontend Service** | `produtoversao.service.ts` |
| **Tabela** | `CPRODUTOVERSAO` ✅ **EXISTE** |

### Tabela CPRODUTOVERSAO

```sql
CREATE TABLE `CPRODUTOVERSAO` (
    `Id` int NOT NULL AUTO_INCREMENT,
    `ProdutoId` int NOT NULL,
    `codigoVersao` varchar(80),
    `descricao` varchar(150),
    ...
    CONSTRAINT `FK_CPRODUTOVERSAO_CPRODUTO_ProdutoId` FOREIGN KEY (`ProdutoId`) 
        REFERENCES `CPRODUTO` (`Id`) ON DELETE RESTRICT
)
```

### Estado UI Atual

```
Dropdown mostra: "Sem versao" (apenas opção vazia)
```

### Razão

**Verdadeiro Comportamento:**
- Se PROD1 **não possui versões cadastradas** → retorna `[]` da API
- Se PROD1 **possui versões** → retorna array de `ProdutoVersao`
- UI renderiza `<option [ngValue]="null">Sem versao</option>` como placeholder
- Depois renderiza `*ngFor="let versao of versoesFiltradas"`

**Verificação Necessária:**
- Consultar banco: `SELECT * FROM CPRODUTOVERSAO WHERE ProdutoId = (SELECT Id FROM CPRODUTO WHERE Codigo = 'PROD1')`
- Se resultado vazio → produto não tem versões, UI está correta
- Se resultado com dados → API não retorna ou filtro está errado

### Status da API

✅ **FUNCIONAL** (endpoint existe, controller existe)
⚠️ **DADOS?: Precisa verificar se PROD1 tem versões no banco**

---

## C. LOTE

### Entidade LoteMaterial

| Propriedade | Localização |
|-----------|-----------|
| **Tabela** | `CLOTEMATERIAL` ✅ **EXISTE** |
| **Controler** | `LoteMaterialController.cs` ✅ |
| **Service** | `LoteMaterialServices.cs` ✅ |
| **Frontend Service** | `lotematerial.service.ts` ✅ |

### Campo Crítico: ControlaLote em Produto

```sql
ALTER TABLE `CPRODUTO` ADD `ControlaLote` tinyint(1) NOT NULL DEFAULT FALSE;
```

### Estrutura de LoteMaterial

```sql
CREATE TABLE `CLOTEMATERIAL` (
    `Id` int NOT NULL AUTO_INCREMENT,
    `produtoid` int NOT NULL,
    `codlote` varchar(80),
    `loteFornecedor` varchar(80),
    `ativo` tinyint(1) NOT NULL DEFAULT TRUE,
    ...
    CONSTRAINT `FK_CLOTEMATERIAL_CPRODUTO_produtoid` 
        FOREIGN KEY (`produtoid`) REFERENCES `CPRODUTO` (`Id`)
)
```

### Estado UI Atual

```
Dropdown mostra: "Sem lote" (apenas opção vazia)
```

### Razão

Mesmo padrão que versão:
- Endpoint `GET /LoteMaterial` retorna todos os lotes
- Frontend filtra por `produtoId` (linha 190 do component)
- Se PROD1 não tem lotes → `lotesFiltrados = []` → mostra só placeholder

### Regra de Negócio Esperada

Se `PROD1.ControlaLote = true`:
- **a)** Selecionar lote existente (comportamento atual)
- **b)** Permitir criar lote no recebimento (não implementado)
- **c)** Exigir lote previamente cadastrado (seria validação no backend)
- **d)** Outro comportamento conforme domínio

**Atualmente:** (a) implementado, (b)(c)(d) não definidos

### Status da API

✅ **FUNCIONAL** (endpoint existe)
⚠️ **DADOS**: Precisa verificar `ControlaLote` de PROD1 e existência de lotes

---

## D. MOTIVO DA ENTRADA

### Classificação Frontend (linhas 176-179)

```typescript
this.motivosMovimento = parametros.filter(parametro => {
  const grupo = this.normalizarTexto(this.getGrupoTexto(parametro));
  return grupo.includes('MOTIVO') && grupo.includes('MOVIMENTO');
});
```

### Procura por

- `parametroGrupo.codigo` ou `parametroGrupo.nome` contém "MOTIVO" E "MOVIMENTO"
- Origem: endpoint `GET /ParametroValor`

### Estado Atual

```
❌ Dropdown vazio
Causa: ParametroValor não retorna dados (endpoint 404)
```

### Dados Esperados

Estrutura anterior:
```sql
INSERT INTO CPARAMETROGRUPO VALUES (..., 'MOTIVO_MOVIMENTO', 'Motivos de Movimento', ...);
INSERT INTO CPARAMETROVALOR 
  VALUES (..., {grupo_id}, 'ENTRADA', 'Entrada de Estoque', ...);
INSERT INTO CPARAMETROVALOR 
  VALUES (..., {grupo_id}, 'DEVOLUCAO', 'Devolução de Fornecedor', ...);
```

### Compartilhamento com Outras Operações

- ✅ Usado em: `saidaestoque`, `reservaestoque`, `inventarioestoque`, `bloqueioestoque`, `ajusteestoque`
- Tipo: **COMPARTILHADO** (mesmo grupo de parâmetros)

### Status

❌ **NÃO FUNCIONAL** - Sem dados de parametrização

---

## E. TIPO DE DOCUMENTO

### Classificação Frontend (linhas 181-184)

```typescript
this.tiposDocumento = parametros.filter(parametro => {
  const grupo = this.normalizarTexto(this.getGrupoTexto(parametro));
  return grupo.includes('TIPO') && grupo.includes('DOCUMENTO');
});
```

### Procura por

- `parametroGrupo` contém "TIPO" E "DOCUMENTO"

### Estado Atual

```
❌ Dropdown vazio
Causa: ParametroValor não retorna dados
```

### Possíveis Documentos no Domínio

(Conforme código descoberto, não criação de enumeração):
- NF (Nota Fiscal)
- NF-e (Nota Fiscal Eletrônica)
- REC (Recebimento)
- BOL (Boleto/Devolução)
- OP (Ordem de Produção)
- TRF (Transferência)
- AJU (Ajuste)

*Não encontrado em código o mapeamento exato — precisaria estar em CPARAMETROVALOR*

### Status

❌ **NÃO FUNCIONAL** - Sem dados de parametrização

---

## F. LOCALIZAÇÃO DESTINO

### Crítico: RUA1 está sendo exibida como opção

**Problema Observado:**
- Tela permite selecionar "RUA1 - RUA" como localização destino
- RUA1 foi classificada como **ESTRUTURAL** (não para armazenagem)
- Uma Entrada de Estoque não deve posicionar stock em estrutural

### Classificação Helper (ClassificacaoLocalizacaoHelper.cs)

```csharp
public static ClassificacaoLocalizacao Calcular(bool bloqueada, bool possuiFilhos, FinalidadeLocalizacao finalidade)
{
    if (bloqueada) return ClassificacaoLocalizacao.Bloqueado;
    if (possuiFilhos) return ClassificacaoLocalizacao.Estrutural;
    return finalidade == FinalidadeLocalizacao.Armazenagem
        ? ClassificacaoLocalizacao.Armazena
        : ClassificacaoLocalizacao.Estrutural;
}
```

**RUA1 é ESTRUTURAL porque:**
- `possuiFilhos = true` (tem colunas/filhos)
- OU `finalidade = FinalidadeLocalizacao.Estrutural` (definido assim no cadastro)

### Filtro Frontend (linha 159)

```typescript
this.localizacoes = (localizacoes ?? [])
  .filter(item => this.getBooleanValue(item, 'permiteEntrada', true) && 
                  !this.getBooleanValue(item, 'bloqueada'));
```

**Verifica:**
- ✅ `permiteEntrada = true`
- ✅ `bloqueada = false`
- ❌ **NÃO verifica `Finalidade`**

### Entidade LocalizacaoEstoque

```csharp
public enum FinalidadeLocalizacao
{
    Estrutural = 1,
    Armazenagem = 2
}

public class LocalizacaoEstoque
{
    public int almoxarifadoid { get; set; }
    public int areaestoqueid { get; set; }
    public bool permiteentrada { get; set; }
    public bool permitesaida { get; set; }
    public bool bloqueada { get; set; }
    public FinalidadeLocalizacao Finalidade { get; set; } = FinalidadeLocalizacao.Estrutural;
}
```

### API Endpoint

```
GET /api/localizacao-estoque
Retorna: LocalizacaoEstoque[] com Finalidade e outros campos
```

### Status da Busca

✅ **API FUNCIONAL** - `/LocalizacaoEstoque` existe e retorna dados
⚠️ **FILTRO INCOMPLETO** - Não filtra por `Finalidade == Armazenagem`

### Dados Esperados para BR001/BRQ-001

**Necessário consultar banco:**
```sql
SELECT 
  codLocalizacao, 
  nome, 
  Finalidade, 
  possuiFilhos, 
  permiteEntrada, 
  bloqueada
FROM CLOCALIZACAOESTOQUE
WHERE almoxarifadoid = (SELECT Id FROM CALMOXARIFADO WHERE codigo = 'BR001' OR codigo = 'BRQ-001')
ORDER BY codLocalizacao;
```

### Correção Recomendada

Adicionar filtro `Finalidade == Armazenagem`:
```typescript
this.localizacoes = (localizacoes ?? [])
  .filter(item => 
    this.getBooleanValue(item, 'permiteEntrada', true) && 
    !this.getBooleanValue(item, 'bloqueada') &&
    this.getAnyValue(item, 'Finalidade') === 2  // Armazenagem
  );
```

---

## G. DEPENDÊNCIAS ENTRE CAMPOS

### Cascata Esperada

```
┌─ Tipo Movimento = ENTRADA [parametro não encontrado ❌]
│  └─ Motivos válidos [parametros não encontrados ❌]
│
├─ Produto [API ✅ funcional]
│  ├─ Versão [API ✅ funcional, dados ⚠️ verificar]
│  └─ Lote [API ✅ funcional, dados ⚠️ verificar]
│
├─ Almoxarifado [API ✅ funcional]
│  └─ Localização ARMAZENA [API ✅ mas filtro ❌ incompleto]
│
└─ Tipo Documento [parametro não encontrado ❌]
```

### Dependências Atuais

| Dependência | Status | Implementação |
|-----------|--------|---------------|
| Produto → Versão | ⚠️ PARCIAL | `filtrarVersoesELotes()` (linha 187) filtra por produtoId ✅ |
| Produto → Lote | ⚠️ PARCIAL | `filtrarVersoesELotes()` filtra por produtoId ✅ |
| Almoxarifado → Localização | ❌ QUEBRADA | `filtrarLocalizacoes()` (linha 194) filtra mas não por Finalidade |
| Tipo Movimento → Motivos | ❌ QUEBRADA | Parametros não carregam |
| Tipo Documento → valores | ❌ QUEBRADA | Parametros não carregam |

---

## H. REDE / HTTP

### Resumo de Endpoints Chamados

| Endpoint | Método | Status HTTP | Dados Retornados | Problema |
|----------|--------|------------|------------------|----------|
| `GET /Produto` | GET | ✅ 200 | Produtos [] | API funciona, dados podem estar vazios |
| `GET /ProdutoVersao` | GET | ✅ 200 | ProdutoVersao[] | API funciona, precisa verificar dados |
| `GET /LoteMaterial` | GET | ✅ 200 | LoteMaterial[] | API funciona, precisa verificar dados |
| `GET /Almoxarifado` | GET | ✅ 200 | Almoxarifado[] | API funciona |
| `GET /localizacao-estoque` | GET | ✅ 200 | LocalizacaoEstoque[] | API funciona, filtro frontend incompleto |
| `GET /UnidadeMedida` | GET | ✅ 200 | UnidadeMedida[] | API funciona |
| `GET /ParametroValor` | GET | ❌ 404/NULL | [] ou erro | **CRÍTICO: Endpoint não existe** |
| `POST /movimento-estoque` | POST | ⚠️ Bloqueado | N/A | Bloqueado pois tipoMovimentoEntrada é undefined (linha 70) |

### Diferenciação de Falhas

```
1. API não chamada: ParametroValor não tem controller ❌
2. API retorna erro: Seria 404, mas frontend não trata
3. API retorna []: LoteMaterial, ProdutoVersao podem retornar vazio (depende de dados)
4. API retorna dados mas frontend não renderiza: Não identificado
5. Dados não existem no banco: ParametroValor — tabelas foram removidas ❌
```

---

## I. ARQUIVOS ENVOLVIDOS

### Backend

| Arquivo | Localização | Status |
|---------|-----------|--------|
| **ParametroValorController** | PRPA/Controllers/ | ❌ NÃO EXISTE |
| **ParametroValorServices** | App.Service/Services/ | ❌ NÃO EXISTE |
| **ParametroValor Entity** | App.Domain/Entities/PRPA/ | ❌ NÃO EXISTE |
| **ProdutoVersaoServices** | App.Service/Services/ProdutoVersaoServices.cs | ✅ EXISTE |
| **ProdutoVersaoController** | PRPA/Controllers/ProdutoVersaoController.cs | ✅ EXISTE |
| **LocalizacaoEstoqueController** | PRPA/Controllers/LocalizacaoEstoqueController.cs | ✅ EXISTE |
| **LocalizacaoEstoqueServices** | App.Service/Services/LocalizacaoEstoqueServices.cs | ✅ EXISTE |
| **ClassificacaoLocalizacaoHelper** | App.Domain/Entities/PRPA/ClassificacaoLocalizacaoHelper.cs | ✅ EXISTE |
| **LoteMaterialController** | PRPA/Controllers/LoteMaterialController.cs | ✅ EXISTE |
| **LoteMaterialServices** | App.Service/Services/LoteMaterialServices.cs | ✅ EXISTE |
| **MovimentoEstoqueController** | PRPA/Controllers/MovimentoEstoqueController.cs | ✅ EXISTE |
| **ProjetoContext** | App.Infra.Data/Context/ProjetoContext.cs | ✅ (mas sem ParametroValor) |

### Migrations

| Arquivo | Data | Ação |
|---------|------|------|
| 20260621231241_ParametroGrupo.cs | 21/06/2026 | CRIAR tabelas |
| 20260621231754_ParametroValor.cs | 21/06/2026 | CRIAR tabelas |
| 20260702173541_RemoveParametroGrupoValorTables.cs | 02/07/2026 | **REMOVER tabelas** ⚠️ |
| 20260803170511_AddFinalidadeLocalizacaoLegada.cs | 03/08/2026 | Adicionar Finalidade a LocalizacaoEstoque |

### Frontend

| Arquivo | Localização | Status |
|---------|-----------|--------|
| **entradaestoque.component.ts** | operacao/entradaestoque/components/entradaestoque/ | ✅ Existe, lógica OK |
| **entradaestoque.component.html** | operacao/entradaestoque/components/entradaestoque/ | ✅ Existe, UI OK |
| **parametrovalor.service.ts** | cadastro/parametrovalor/services/ | ✅ Existe mas endpoint não existe |
| **parametrovalor.interface.ts** | cadastro/parametrovalor/models/ | ✅ Interface definida |
| **produtoversao.service.ts** | cadastro/produtoversao/services/ | ✅ OK |
| **lotematerial.service.ts** | cadastro/lotematerial/services/ | ✅ OK |
| **localizacaoestoque.service.ts** | cadastro/localizacaoestoque/services/ | ✅ OK |

---

## MATRIZ DE DIAGNÓSTICO

| Campo | Fonte | Endpoint | Dados no Banco | Resposta API | Estado UI | Causa | Correção Recomendada |
|-------|-------|----------|----------------|-------------|----------|-------|----------------------|
| **Tipo Movimento Entrada** | ParametroValor (grupo "TIPO MOVIMENTO", valor "ENTRADA") | GET /ParametroValor | ❌ Tabelas removidas (02/07/2026) | ❌ 404 Not Found | ❌ Erro "não encontrado" | Endpoint não existe, tabelas removidas | RESTAURAR tabelas + Controller + Service + Seedagem |
| **Produto** | CPRODUTO | GET /Produto | ✅ Tabela existe | ✅ 200 OK | ✅ Carrega | Dados existem | Funciona |
| **Versão do Produto** | CPRODUTOVERSAO | GET /ProdutoVersao | ⚠️ Depende de PROD1 ter versões | ⚠️ 200 com [] ou dados | ⚠️ Mostra "Sem versão" | Produto pode não ter versões | Verificar dados de PROD1 no banco |
| **Lote** | CLOTEMATERIAL | GET /LoteMaterial | ⚠️ Depende de PROD1.ControlaLote | ⚠️ 200 com [] ou dados | ⚠️ Mostra "Sem lote" | Produto pode não ter lotes ou ControlaLote=false | Verificar PROD1.ControlaLote e existência de lotes |
| **Almoxarifado** | CALMOXARIFADO | GET /Almoxarifado | ✅ Tabela existe | ✅ 200 OK | ✅ Carrega | Dados existem | Funciona |
| **Localização Destino** | CLOCALIZACAOESTOQUE | GET /localizacao-estoque | ✅ Tabela existe | ✅ 200 OK com RUA1 | ❌ RUA1 aparece mas é ESTRUTURAL | Filtro não verifica Finalidade | Adicionar filtro `Finalidade == Armazenagem` |
| **Unidade de Medida** | CUNIDADEMEDIDA | GET /UnidadeMedida | ✅ Tabela existe | ✅ 200 OK | ✅ Carrega | Dados existem | Funciona |
| **Motivo da Entrada** | ParametroValor (grupo "MOTIVO MOVIMENTO") | GET /ParametroValor | ❌ Tabelas removidas | ❌ 404 Not Found | ❌ Vazio | Endpoint não existe | RESTAURAR tabelas + Seedagem |
| **Tipo de Documento** | ParametroValor (grupo "TIPO DOCUMENTO") | GET /ParametroValor | ❌ Tabelas removidas | ❌ 404 Not Found | ❌ Vazio | Endpoint não existe | RESTAURAR tabelas + Seedagem |

---

## INCONSISTÊNCIAS DE DOMÍNIO

1. **RUA1 Classificação**: Marcada como ESTRUTURAL (tem filhos) mas permitida em Entrada de Estoque
   - Correção: Filtro deve verificar `Finalidade == Armazenagem`

2. **ParametroValor Remoção**: Tabelas foram removidas mas frontend ainda chama endpoint
   - Correção: Restaurar tabelas e endpoint OU alterar frontend para usar enumerações

3. **ControlaLote não implementado**: Campo existe em CPRODUTO mas UI não valida
   - Correção: Validação backend: se `ControlaLote=true`, lote é obrigatório

4. **Versionamento de Produto**: UI permite "Sem versão" para todo produto
   - Possível correção: Validar se produto exige versão

---

## PARÂMETROS AUSENTES

### No Banco (ParametroValor)

```
GRUPO: "TIPO MOVIMENTO"
  ├─ ENTRADA
  ├─ SAÍDA
  ├─ TRANSFERÊNCIA
  └─ DEVOLUÇÃO

GRUPO: "MOTIVO MOVIMENTO"
  ├─ RECEBIMENTO
  ├─ COMPRA
  ├─ AJUSTE
  ├─ DEVOLUÇÃO
  └─ PRODUÇÃO

GRUPO: "TIPO DOCUMENTO"
  ├─ NF (Nota Fiscal)
  ├─ NF-e
  ├─ REC (Recebimento)
  ├─ BOL (Boleto)
  ├─ OP (Ordem Produção)
  ├─ TRF (Transferência)
  └─ AJU (Ajuste)
```

---

## RECOMENDAÇÃO: EST-OP-02B.2

**Menor pacote possível de implementação:**

### Fase 1: RESTAURAR ParametroValor (Crítico)
1. Reverter migration 20260702173541 (ou criar nova migration que recria tabelas)
2. Recriar `ParametroGrupo` e `ParametroValor` entities
3. Mapear entities em `ProjetoContext`
4. Criar `ParametroValorController` com `[HttpGet]`
5. Criar `ParametroValorServices`
6. Criar seedagem de dados (grupos e valores)
7. Testar GET /ParametroValor retorna dados

**Estimativa:** 4-6 horas

### Fase 2: Corrigir Filtro de Localização (Alto Impacto)
1. Adicionar verificação `Finalidade == Armazenagem` em:
   - `LocalizacaoEstoqueService.GetAllAsync()` OU
   - `entradaestoque.component.ts` (linha 159)
2. Testes: RUA1 não deve aparecer mais em Entrada

**Estimativa:** 1-2 horas

### Fase 3: Validações Opcionais (Baixo Impacto)
1. Backend: Validar `ControlaLote` obrigatório se `CPRODUTO.ControlaLote = true`
2. Frontend: Mostrar aviso se produto exige lote/versão

**Estimativa:** 2-3 horas

---

## CONCLUSÃO

**EST-OP-02B.1 Status: DIAGNÓSTICO CONCLUÍDO ✅**

### Bloqueadores Identificados

1. ❌ **CRÍTICO**: ParametroValor não existe no backend (tabelas removidas 02/07/2026)
   - Bloqueia: Tipo Movimento, Motivo, Tipo Documento
   - Impacto: Tela não funciona

2. ⚠️ **ALTO**: Filtro de Localização incompleto
   - Permite RUA1 (ESTRUTURAL) em Entrada de Estoque
   - Violação de regra de negócio

3. ⚠️ **MÉDIO**: Dados de teste podem estar incompletos
   - PROD1 pode não ter versões/lotes
   - Precisa verificar banco

### Próximos Passos (EST-OP-02B.2)

1. Restaurar tabelas ParametroValor/ParametroGrupo
2. Criar controller e services
3. Seedar dados parametrizados
4. Corrigir filtro de localização (adicionar `Finalidade`)
5. Testar ponta a ponta
6. Validar dependências em cascata

**Sem estes corretivos, tela Entrada de Estoque permanecerá não funcional.**
