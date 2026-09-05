# EST-OP-02C.1A — ESPECIFICAÇÃO DE UNIDADES DE MEDIDA NO ESTOQUE

> **Etapa de Auditoria e Especificação**  
> NÃO alterar código, banco, migrations ou telas.  
> Apenas documentar o estado atual e propor modelo.

---

## 1. DECISÃO FUNCIONAL JÁ APROVADA (REFERÊNCIA)

Registrar como decisão de arquitetura:

1. Todo produto deve possuir uma **UNIDADE DE ESTOQUE**.
2. **SaldoEstoque** é sempre controlado na Unidade de Estoque.
3. A operação pode receber uma **UNIDADE INFORMADA** diferente da Unidade de Estoque.
4. Unidade informada diferente só é permitida se existir **conversão válida**.
5. O **MovimentoEstoque** deve preservar a quantidade/unidade informada na origem e a quantidade/unidade convertida para estoque.
6. Conversões **não podem ser implícitas**.
7. O modelo deve facilitar futura integração com apontamento de produção, sem implementar Produção nesta etapa.

---

## 2. AUDITORIA DE UNIDADEMEDIDA

### 2.1 Entidade e Tabela Física

| Item | Detalhe |
|------|---------|
| Entidade | `App.Domain.Entities.PRPA.UnidadeMedida` |
| Tabela física | `CUNIDADEMEDIDA` (Mapping: `UnidadeMedidaConfig.cs:32`) |
| EF Mapping | `App.Infra.Data.Mapping.UnidadeMedidaConfig` |
| Controller | `PRPA.Controllers.UnidadeMedidaController` |
| Service | `App.Service.Services.UnidadeMedidaServices` |
| Repository | `App.Domain.Interfaces.Repositories.IUnidadeMedidaRepository` |
| DTO Create | `App.Service.DTOs.Produto.UnidadeMedidaCreateDto` |
| Validator | `App.Service.Validators.UnidadeMedidaValidator` |
| Frontend Service | `UnidadeMedidaService` (`cadastro/unidademedida/services/unidademedida.service.ts`) |
| Frontend Model | `UnidadeMedida` interface (`cadastro/unidademedida/models/unidademedida.interface.ts`) |

### 2.2 Campos da Entidade

```csharp
public class UnidadeMedida : BaseEntity
{
    [StringLength(20)]
    public string Codigo { get; set; } = string.Empty;

    [StringLength(100)]
    public string Descricao { get; set; } = string.Empty;

    public int CasasDecimais { get; set; } = 3;

    public bool Ativo { get; set; } = true;

    public ICollection<Produto> Produtos { get; set; } = new List<Produto>();
    public ICollection<ProdutoEspecificacao> ProdutoEspecificacoes { get; set; } = new List<ProdutoEspecificacao>();
}
```

### 2.3 Mapeamento de Capacidade Atual

| Capacidade | Suportado? | Detalhes |
|------------|-----------|----------|
| **Código** | ✅ SIM | `Codigo` (max 20 chars), obrigatório |
| **Símbolo** | ❌ NÃO | Não existe campo `Simbolo` ou `Sigla` |
| **Dimensão** | ❌ NÃO | Não existe campo `Dimensao`, `Tipo`, `Categoria` ou similar |
| **Precisão (casas decimais)** | ✅ SIM | `CasasDecimais` (int, default 3, validado 0-10) |
| **Conversão** | ❌ NÃO | Não há campos de fator, unidade base, ou relacionamento de conversão |
| **Fator** | ❌ NÃO | Não existe |
| **Unidade Base** | ❌ NÃO | Não existe |
| **Status (Ativo/Inativo)** | ✅ SIM | `Ativo` (bool, default true) |

**Resumo UNIDADEMEDIDA ATUAL SUPORTA:**
- código? **SIM**
- símbolo? **NÃO**
- dimensão? **NÃO**
- precisão? **SIM** (casas decimais)
- conversão? **NÃO**
- fator? **NÃO**
- unidade base? **NÃO**
- status? **SIM**

---

## 3. AUDITORIA DE PRODUTO

### 3.1 Entidade e Campos Relevantes

**Entidade:** `App.Domain.Entities.PRPA.Produto`  
**Tabela:** `CPRODUTO`

```csharp
public class Produto : BaseEntity
{
    // ... outros campos ...
    public int UnidadeMedidaId { get; set; }  // FK para UnidadeMedida
    public UnidadeMedida UnidadeMedida { get; set; } = null!;
    // ...
}
```

### 3.2 Análise de Unidades no Produto

| Conceito | Existe? | Campo Real |
|----------|---------|------------|
| UnidadeMedidaId (única) | ✅ SIM | `UnidadeMedidaId` (int, obrigatório) |
| Unidade principal | ✅ SIM | A mesma `UnidadeMedidaId` acima |
| **Unidade de Estoque explícita** | ❌ NÃO | Não existe campo separado |
| Unidade de Compra | ❌ NÃO | Não existe |
| Unidade de Produção | ❌ NÃO | Não existe |
| Unidades alternativas | ❌ NÃO | Não existe relacionamento N:N ou 1:N para unidades alternativas |
| Flags relacionadas | ❌ NÃO | Não existem flags como `PermiteConversao`, `UnidadeEstoqueDiferente`, etc. |

**Resposta:**
> Hoje o Produto possui: **A) uma única unidade** (campo `UnidadeMedidaId`)
> 
> Não possui unidade de estoque explícita separada, nem múltiplas unidades, nem unidades alternativas.

---

## 4. AUDITORIA DE SALDOESTOQUE

### 4.1 Entidade e Tabela

**Entidade:** `App.Domain.Entities.PRPA.SaldoEstoque`  
**Tabela:** `CSALDOESTOQUE`

```csharp
public class SaldoEstoque : BaseEntity
{
    public int produtoid { get; set; }
    public int? versaoprodutoid { get; set; }
    public int? lotematerialid { get; set; }
    public int almoxarifadoid { get; set; }
    public int localizacaoestoqueid { get; set; }
    public decimal qtdfisica { get; set; }
    public decimal qtdreservada { get; set; }
    public decimal qtdbloqueada { get; set; }
    public decimal qtddisponivel { get; set; }
    public int unidademedidaid { get; set; }  // <-- Unidade do saldo
    public DateTime? ultimamovimentacao { get; set; }
    // ... navegações ...
}
```

### 4.2 Análise da Unidade no Saldo

| Pergunta | Resposta | Evidência |
|----------|----------|-----------|
| Unidade de medida do saldo? | Campo `unidademedidaid` próprio | `SaldoEstoque.cs:23`, `SaldoEstoqueConfig.cs:42-43` |
| Vem do Produto? | **Não obrigatoriamente** | O `unidademedidaid` é independente no Saldo; na criação via `EntradaDiretaSincronizacaoServices.cs:161` usa `movimento.unidademedidaid` |
| Pode divergir entre saldos do mesmo produto? | **SIM, tecnicamente possível** | Chave lógica inclui `unidademedidaid` (linha 145 `EntradaDiretaSincronizacaoServices.cs`), permitindo saldos distintos por unidade |
| Existe validação garantindo coerência? | **Parcial** | Em `ValidarEntradaDiretaAsync` (linha 128-129): lança exceção se `movimento.unidademedidaid != produto.UnidadeMedidaId` |

**Resposta:**
> SaldoEstoque hoje opera: **na unidade informada no Movimento** (com validação de que deve ser igual à do Produto na Entrada Direta).
> 
> **Gap:** A validação força a unidade do movimento = unidade do produto, impedindo conversão. Não há regra de "unidade de estoque" separada.

---

## 5. AUDITORIA DE MOVIMENTOESTOQUE

### 5.1 Entidade e Campos de Quantidade/Unidade

**Entidade:** `App.Domain.Entities.PRPA.MovimentoEstoque`  
**Tabela:** `CMOVIMENTOESTOQUE`

```csharp
public class MovimentoEstoque : BaseEntity
{
    public decimal quantidade { get; set; }          // Quantidade única
    public int unidademedidaid { get; set; }         // Unidade única
    // ... demais campos ...
}
```

### 5.2 Verificação de Conceitos de Conversão

| Conceito Necessário | Existe? | Gap |
|---------------------|---------|-----|
| `QuantidadeOriginal` | ❌ NÃO | Apenas `quantidade` |
| `UnidadeOriginalId` | ❌ NÃO | Apenas `unidademedidaid` |
| `QuantidadeConvertida` | ❌ NÃO | Não existe |
| `UnidadeEstoqueId` | ❌ NÃO | Não existe |
| `FatorConversao` | ❌ NÃO | Não existe |
| `OrigemConversao` | ❌ NÃO | Não existe |

**Resultado:** **Todos são GAPS**. O modelo atual possui apenas uma quantidade e uma unidade por movimento.

---

## 6. AUDITORIA DA ENTRADA DIRETA

### 6.1 Endpoint e Fluxo

| Item | Detalhe |
|------|---------|
| Endpoint | `POST /api/MovimentoEstoque/entrada-direta` |
| Controller | `MovimentoEstoqueController.cs:113-131` |
| Service | `EntradaDiretaSincronizacaoServices.SincronizarEntradaDiretaAsync` |
| Tela Frontend | `/operacao/entradaestoque` (`EntradaestoqueComponent`) |

### 6.2 Fluxo Atual Documentado

```
Produto (UnidadeMedidaId = KG)
    ↓
Frontend carrega TODAS as UnidadeMedida (unidadesMedida = todas ativas)
    ↓
Usuário seleciona UnidadeMedida no dropdown (livre escolha)
    ↓
Prepara payload: { quantidade: 2.5, unidademedidaid: TON_ID }
    ↓
POST /entrada-direta → EntradaDiretaSincronizacaoServices
    ↓
VALIDAÇÃO (linha 128-129): if (movimento.unidademedidaid != produto.UnidadeMedidaId) → EXCEPTION
    ↓
Se passa: cria MovimentoEstoque com quantidade=2.5, unidademedidaid=TON_ID
    ↓
Cria/atualiza SaldoEstoque con unidademedidaid=TON_ID, qtdfisica += 2.5
```

### 6.3 Respostas dos Questionamentos

| Pergunta | Resposta |
|----------|----------|
| De onde vem a unidade atualmente? | Dropdown carrega **todas** UnidadeMedida ativas do backend (`unidadeMedidaService.getUnidadeMedida()`) |
| Frontend permite escolher qualquer UnidadeMedida? | **SIM** — lista todas sem filtro |
| Backend exige que a unidade seja igual à do Produto? | **SIM** — validação em `EntradaDiretaSincronizacaoServices.cs:128-129` lança exceção se diferente |
| Qual erro ocorre em unidade incompatível? | `"Unidade de medida do movimento não corresponde à do produto."` |
| Como a quantidade chega ao SaldoEstoque? | Direta: `saldo.qtdfisica += movimento.quantidade` (sem conversão), na mesma unidade do movimento |

---

## 7. AUDITORIA DE CONVERSÕES EXISTENTES

### 7.1 Busca no Projeto Inteiro

Pesquisados: `ConversaoUnidade`, `ConversaoUnidadeMedida`, `FatorConversao`, `ProdutoUnidade`, `UnidadeAlternativa`, `UnidadeConversao`, `UnidadeOrigem`, `UnidadeDestino`, `Densidade`, `PesoEspecifico` (e variações)

### 7.2 Resultado

**CONVERSÃO JÁ EXISTE: NÃO**

Não existe nenhuma entidade, tabela, serviço, DTO ou conceito de conversão de unidades no código atual.

---

## 8. DEFINIÇÃO DE CONCEITOS PARA ESTOQUE

| Conceito | Definição |
|----------|-----------|
| **UNIDADE DE ESTOQUE** | Unidade oficial em que `SaldoEstoque` é mantido. Definida no cadastro do Produto (novo campo `UnidadeEstoqueId` ou uso do `UnidadeMedidaId` existente como oficial). |
| **UNIDADE RECEBIDA** | Unidade informada na operação/documento de entrada (pode ser diferente da Unidade de Estoque). |
| **QUANTIDADE RECEBIDA** | Quantidade na Unidade Recebida (valor digitado pelo usuário). |
| **QUANTIDADE DE ESTOQUE** | Quantidade convertida para Unidade de Estoque (valor que efetivamente entra no saldo). |
| **FATOR DE CONVERSÃO** | Fator aplicado na operação: `QuantidadeEstoque = QuantidadeRecebida × FatorConversao`. |
| **ORIGEM DA CONVERSÃO** | Classificação da regra usada:<br>- `GLOBAL` — conversão dimensional fixa (ex: 1 t = 1000 kg)<br>- `PRODUTO` — conversão específica do produto (ex: 1 CX = 24 UN para PROD-A)<br>- `LOTE` — conversão dependente de lote (densidade, umidade) — **futuro** |

---

## 9. PROPOSTA DE MODELO MÍNIMO

### 9.1 Comparação de Opções

| Critério | OPÇÃO A: Conversões só em UnidadeMedida | OPÇÃO B: Conversões por Produto | OPÇÃO C: Global + Override por Produto/Lote |
|----------|------------------------------------------|----------------------------------|---------------------------------------------|
| **Aderência ao código atual** | Baixa (requer nova entidade UnidadeMedidaConversao) | Média (requer nova entidade ProdutoUnidadeConversao) | **Alta** (reaproveita UnidadeMedida + nova entidade para overrides) |
| **Complexidade** | Baixa | Média | Média-Alta |
| **Auditabilidade** | Média (conversão global difícil de rastrear por produto) | Alta (explícita por produto) | **Muito Alta** (fator histórico preservado no movimento) |
| **Risco** | Baixo | Médio | Médio |
| **Evolução futura (Produção/UL)** | Limitada | Boa | **Melhor** (suporta lote no futuro) |
| **Impacto em Entrada** | Médio (dropdown filtrado por conversões globais) | Médio (dropdown filtrado por produto) | **Baixo** (dropdown unificado: global + produto) |
| **Impacto em SaldoEstoque** | Baixo | Baixo | Baixo |
| **Impacto em MovimentoEstoque** | Alto (novos campos) | Alto (novos campos) | Alto (novos campos) |

### 9.2 Recomendação: **OPÇÃO C — Conversão Global + Override por Produto (+ futuro Lote)**

**Justificativa:**
- Conversões dimensionais (kg↔g, t↔kg, m↔cm, L↔mL) são universais → **Global**
- Conversões de embalagem (cx↔un, rolo↔m, fardo↔kg) são por produto → **Override Produto**
- Arquitetura extensível para Lote sem refatoração → **Campo `OrigemConversao` no Movimento**
- Preserva fator histórico no movimento → **Auditoria total**

---

## 10. CONVERSÕES FIXAS (GLOBAIS / DIMENSIONAIS)

### 10.1 Exemplos Canônicos

| Dimensão | Unidade Base | Conversões Fixas |
|----------|--------------|------------------|
| Massa | kg | 1 t = 1000 kg, 1 kg = 1000 g, 1 g = 1000 mg |
| Comprimento | m | 1 km = 1000 m, 1 m = 100 cm = 1000 mm |
| Volume | L | 1 m³ = 1000 L, 1 L = 1000 mL |
| Área | m² | 1 ha = 10000 m², 1 m² = 10000 cm² |
| Tempo | s | 1 h = 3600 s, 1 min = 60 s |

### 10.2 Modelo Proposto

**Entidade:** `ConversaoUnidadeGlobal` (nova)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `Id` | int | PK |
| `UnidadeOrigemId` | int | FK UnidadeMedida (ex: TON) |
| `UnidadeDestinoId` | int | FK UnidadeMedida (ex: KG) |
| `Fator` | decimal(18,10) | Fator multiplicativo (ex: 1000) |
| `Dimensao` | string(50) | "MASSA", "COMPRIMENTO", "VOLUME", etc. |
| `Ativo` | bool | Default true |
| `DataVigenciaInicio` | DateTime | |
| `DataVigenciaFim` | DateTime? | Null = vigente indefinidamente |

**Regras:**
- Só permite conversão entre unidades da **mesma dimensão**
- `Dimensao` impede conversões impossíveis (ex: kg → m³)
- Fator sempre **> 0**
- Unidade base da dimensão = fator 1 (ex: KG para MASSA)

---

## 11. CONVERSÕES POR PRODUTO

### 11.1 Cenários

| Produto | Unidade Estoque | Unidade Recebida | Conversão |
|---------|-----------------|------------------|-----------|
| PROD-A | UN | CX | 1 CX = 24 UN |
| PROD-B | M | ROLO | 1 ROLO = 50 M |
| PROD-C | KG | FARDO | 1 FARDO = 12 KG |

### 11.2 Entidade Proposta: `ProdutoUnidadeConversao` (nova)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `Id` | int | PK |
| `ProdutoId` | int | FK Produto |
| `UnidadeOrigemId` | int | FK UnidadeMedida (ex: CX) |
| `UnidadeDestinoId` | int | FK UnidadeMedida (ex: UN) — deve ser a Unidade de Estoque do produto |
| `Fator` | decimal(18,10) | Fator (ex: 24) |
| `Ativo` | bool | Default true |
| `DataVigenciaInicio` | DateTime | |
| `DataVigenciaFim` | DateTime? | |

**Regra:** `UnidadeDestinoId` **deve ser igual** à Unidade de Estoque do produto (validar no backend).

---

## 12. CONVERSÕES POR LOTE (FUTURO — SOMENTE ESPECIFICAÇÃO)

### 12.1 Cenário Exemplo

> Tonelada de madeira → m³ dependente de: densidade, umidade, espécie, lote.

### 12.2 Modelo Extensível (Preparação)

**Entidade futura:** `LoteUnidadeConversao` (não criar agora)

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `Id` | int | PK |
| `LoteMaterialId` | int | FK LoteMaterial |
| `UnidadeOrigemId` | int | FK UnidadeMedida |
| `UnidadeDestinoId` | int | FK UnidadeMedida |
| `Fator` | decimal(18,10) | Calculado com base em densidade/umidade |
| `Densidade` | decimal(18,4) | kg/m³ |
| `Umidade` | decimal(5,2) | % |
| `OrigemCalculo` | string(50) | "MANUAL", "FORMULA", "LABORATORIO" |
| `DataCalculo` | DateTime | |

### 12.3 Extensibilidade sem Refatoração

O campo `OrigemConversao` no `MovimentoEstoque` (ver seção 15) já suporta valor `"LOTE"`. Quando implementado:
1. Busca conversão na ordem: **Lote → Produto → Global**
2. Nenhuma mudança no `MovimentoEstoque` ou `SaldoEstoque`
3. Apenas novo serviço de resolução de conversão

---

## 13. REGRAS DA ENTRADA (COMPORTAMENTO FINAL DA TELA)

### 13.1 Fluxo Proposto

```
Ao selecionar Produto:
┌─────────────────────────────────────────────┐
│ Unidade de Estoque: [ KG ]        (readonly)│
│ Quantidade Recebida:  [ 2,5 ]               │
│ Unidade Recebida:     [ TON ▼ ]             │
└─────────────────────────────────────────────┘

Se Unidade Recebida = Unidade Estoque:
    Quantidade Estoque = Quantidade Recebida
    Fator = 1
    OrigemConversao = "NENHUMA"

Se Diferente:
    Buscar conversão válida (ordem: Lote → Produto → Global)
    Se NÃO encontrar → BLOQUEAR / não exibir no dropdown
    Se encontrar:
        Exibir ANTES da confirmação:
        ┌─────────────────────────────────────┐
        │ Conversão aplicada:                 │
        │ 1 TON = 1000 KG                     │
        │ Quantidade que entrará no estoque:  │
        │ 2500 KG                             │
        └─────────────────────────────────────┘
```

### 13.2 Regras de Dropdown (Seção 14)

---

## 14. DROPDOWN UNIDADE RECEBIDA

### 14.1 Regra de Exibição

O dropdown **NÃO** lista indiscriminadamente todas as unidades.

**Deve apresentar apenas:**
1. **Unidade de Estoque** do produto (sempre disponível, fator = 1)
2. **Unidades alternativas com conversão válida** para o produto/contexto:
   - Conversões Globais ativas onde `UnidadeDestinoId = UnidadeEstoqueId` do produto
   - Conversões por Produto ativas onde `UnidadeDestinoId = UnidadeEstoqueId` do produto
   - (Futuro) Conversões por Lote ativas

### 14.2 Comportamento Sem Conversão

- Se não houver conversão válida para uma unidade: **não exibir** ou **exibir desabilitada**
- Frontend **não deve inventar** conversão
- Backend **deve validar novamente** no `ValidarEntradaDiretaAsync`

---

## 15. AUDITORIA DO MOVIMENTO (CAMPOS A ADICIONAR)

### 15.1 Novos Campos em `MovimentoEstoque`

| Campo | Tipo | Descrição |
|-------|------|-----------|
| `QuantidadeOriginal` | decimal(18,4) | Quantidade digitada na Unidade Recebida |
| `UnidadeOriginalId` | int | FK UnidadeMedida (Unidade Recebida) |
| `QuantidadeConvertida` | decimal(18,4) | Quantidade na Unidade de Estoque (após conversão) |
| `UnidadeEstoqueId` | int | FK UnidadeMedida (Unidade de Estoque do produto no momento) |
| `FatorConversaoAplicado` | decimal(18,10) | Fator usado na operação (ex: 1000) |
| `OrigemConversao` | string(20) | "GLOBAL" \| "PRODUTO" \| "LOTE" \| "NENHUMA" |
| `ConversaoId` | int? | ID da conversão usada (Global/Produto/Lote) — para rastreabilidade |

### 15.2 Regra de Ouro

> A movimentação confirmada deve **preservar o fator histórico**, mesmo que a regra de conversão mude no futuro.

---

## 16. ESTORNO (REGRA FUTURA)

> **Estorno deve usar as quantidades/fator do movimento original.**
> 
> - Não recalcular usando fator atual
> - Usar `QuantidadeConvertida` e `UnidadeEstoqueId` do movimento original
> - `MovimentoEstoque.estorno = true` referencia `movimentoorigemid`

---

## 17. PRECISÃO E ARREDONDAMENTO

### 17.1 Tipos Atuais

| Entidade | Campo | Tipo Banco | Tipo C# |
|----------|-------|------------|---------|
| `SaldoEstoque` | `qtdfisica`, `qtdreservada`, `qtdbloqueada`, `qtddisponivel` | `decimal(18,4)` | `decimal` |
| `MovimentoEstoque` | `quantidade` | `decimal(18,4)` | `decimal` |
| `UnidadeMedida` | `CasasDecimais` | `int` | `int` |

### 17.2 Proposta

| Item | Especificação |
|------|---------------|
| **Precisão (precision)** | 18 dígitos totais |
| **Escala (scale)** | 4 casas decimais para saldos e movimentos (compatível com atual) |
| **Casas decimais de exibição** | Conforme `UnidadeMedida.CasasDecimais` (UI) |
| **Regra de arredondamento** | **Banker's Rounding** (MidpointRounding.ToEven) — padrão do `decimal` .NET |
| **Onde arredondar** | **Apenas na exibição (UI)**. **Nunca** no cálculo interno. Saldo e movimento guardam precisão total (18,4). |
| **Evitar perda acumulada** | Cálculos internos com `decimal` full precision; arredondamento só no último passo de exibição ou impressão. |
| **Confirmação uso de `decimal`** | ✅ Confirmado — tanto C# quanto banco usam `decimal` (não float/double) |

---

## 18. REGRAS DE INTEGRIDADE (MÍNIMAS)

1. **Produto deve ter Unidade de Estoque** — `UnidadeMedidaId` obrigatório e ativo
2. **SaldoEstoque sempre usa Unidade de Estoque** — `SaldoEstoque.unidademedidaid` = Produto.UnidadeEstoqueId
3. **Movimento pode possuir Unidade Recebida diferente** — campos `UnidadeOriginalId` + `QuantidadeOriginal`
4. **Conversão deve estar ativa e vigente** — `Ativo=true` e `DataVigenciaInicio <= hoje <= DataVigenciaFim` (ou null)
5. **Dimensões incompatíveis não podem usar conversão global** — validar `Dimensao` igual
6. **Quantidade convertida deve ser > 0**
7. **Fator aplicado deve ser > 0**
8. **Backend valida tudo** — frontend apenas antecipa UX

---

## 19. IMPACTO FUTURO EM UL (UNIDADE LOGÍSTICA)

### 19.1 Cenário

```
UL-BB-001
Quantidade recebida: 1 TON
Quantidade estoque: 1000 KG
```

### 19.2 Recomendação

A UL deve trabalhar com **AMBAS** as quantidades:

| Campo na UL | Valor | Origem |
|-------------|-------|--------|
| `QuantidadeEstoque` | 1000 | `Movimento.QuantidadeConvertida` |
| `UnidadeEstoqueId` | KG | `Movimento.UnidadeEstoqueId` |
| `QuantidadeOriginal` | 1 | `Movimento.QuantidadeOriginal` |
| `UnidadeOriginalId` | TON | `Movimento.UnidadeOriginalId` |
| `FatorConversao` | 1000 | `Movimento.FatorConversaoAplicado` |

**Por que ambas?**
- Operações de expedição/transferência podem precisar da unidade original (ex: "expedir 1 TON")
- Consumo interno e saldos usam unidade de estoque (KG)
- Rastreabilidade total sem recálculo

**Evita retrabalho em EST-OP-02C.2:** campos já existem no Movimento; UL apenas replica/referencia.

---

## 20. IMPACTO FUTURO EM PRODUÇÃO

### 20.1 Garantias de Compatibilidade

A arquitetura **não deve impedir** futuramente:

| Cenário | Suporte |
|---------|---------|
| Consumo em unidade diferente da BOM | ✅ Via `UnidadeOriginalId` + conversão no apontamento |
| Produto acabado em outra unidade | ✅ Produto acabado tem sua `UnidadeEstoque`; conversão no movimento de produção |
| BOM/Estrutura convertendo consumo técnico | ✅ Separado: conversão de unidade ≠ relação de consumo (BOM) |

### 20.2 Registro Explícito

> **Conversão de unidade NÃO é a mesma coisa que relação de consumo da BOM.**
> 
> - Conversão de unidade: 1 KG = 1000 G (mesma substância, mudança de escala)
> - Relação BOM: 1 UN produto acabado consome 2.5 KG matéria-prima (transformação, perda, rendimento)
> 
> Devem ser modelados e armazenados **separadamente**.

---

## 21. PLANO DE IMPLEMENTAÇÃO (FASES PEQUENAS)

| Fase | Objetivo | Arquivos Prováveis | Migration | Teste | Critério de Aceite | Risco |
|------|----------|-------------------|-----------|-------|-------------------|-------|
| **EST-OP-02C.1B** | Unidade de Estoque automática na Entrada | `Produto` (novo campo ou usar existente), `EntradaDiretaSincronizacaoServices`, Frontend dropdown | Sim (UnidadeEstoqueId em Produto ou flag) | Unit + Integração | Ao selecionar produto, Unidade de Estoque aparece readonly; Entrada só aceita unidade do produto | Baixo |
| **EST-OP-02C.1C** | Unidade Recebida + Conversões Fixas (Globais) | Nova entidade `ConversaoUnidadeGlobal`, `MovimentoEstoque` (+6 campos), `EntradaDiretaSincronizacaoServices` (resolver conversão), Frontend dropdown filtrado | Sim (tabela + campos movimento) | Unit + Integração + UI | Entrada 2 TON → Saldo +2000 KG; conversão global kg↔g↔t funciona | Médio |
| **EST-OP-02C.1D** | Conversões Específicas por Produto | Nova entidade `ProdutoUnidadeConversao`, Service de resolução (ordem: Produto → Global), Frontend carrega conversões do produto | Sim (tabela) | Unit + Integração + UI | Produto PROD-A: 10 CX → Saldo +240 UN (conversão 1 CX = 24 UN) | Médio |
| **EST-OP-02C.1E** | Conversões por Lote — *somente se necessário* | Nova entidade `LoteUnidadeConversao`, Service estendido (ordem: Lote → Produto → Global) | Sim (tabela) | Unit + Integração | Lote madeira: 1 TON → 1.2 m³ (densidade 0.83) | Alto (complexidade) — **avaliar necessidade real antes** |

---

## 22. CENÁRIOS DE ACEITE (MÍNIMOS)

| Cenário | Descrição | Resultado Esperado |
|---------|-----------|-------------------|
| **1** | Produto estoque = KG, Entrada = 10 KG | Saldo += 10 KG |
| **2** | Produto estoque = KG, Entrada = 2 TON, Conversão ativa = 1000 | Saldo += 2000 KG |
| **3** | Produto estoque = KG, Entrada = TON, **Sem conversão** | → **Rejeitar** (erro validação) |
| **4** | Produto **sem** Unidade de Estoque | → **Rejeitar** (erro validação) |
| **5** | Produto estoque = UN, Entrada = CX, Conversão produto: 1 CX = 24 UN, Entrada 10 CX | Saldo += 240 UN |
| **6** | Movimento antigo com fator 1000; fator cadastrado muda depois | → Histórico original **permanece 1000** |
| **7** | Estorno de movimento | → Usa **fator original** do movimento estornado |
| **8** | Conversão dimensional incompatível (KG → L) | → **Rejeitar** sem regra específica |

---

## 23. NÃO IMPLEMENTAR NESTA ETAPA

Não alterar:
- `UnidadeMedida` (entidade, tabela, service, controller, frontend)
- `Produto` (entidade, tabela, service, controller, frontend)
- `MovimentoEstoque` (entidade, tabela, service, controller, frontend)
- `SaldoEstoque` (entidade, tabela, service)
- Entrada frontend (tela `/operacao/entradaestoque`)
- Banco de dados / Migrations
- UL, Quarentena, Produção

**Apenas especificar.**

---

## 24. DOCUMENTAÇÃO

**Documento único criado:**
- `MES-ProjectBook/EST-OP-02C.1A_ESPECIFICACAO_UNIDADES_MEDIDA_ESTOQUE.md` (este arquivo)

---

## 25. GATE FINAL — RESPOSTA EXATA

```
EST-OP-02C.1A — RESULTADO

MODELO ATUAL DE UNIDADE:
Produto possui uma única UnidadeMedidaId (obrigatória). UnidadeMedida tem Código, Descrição, CasasDecimais, Ativo. Sem símbolo, dimensão, conversão ou fator. SaldoEstoque e MovimentoEstoque têm unidademedidaid próprio; Entrada Direta valida que movimento.unidademedidaid == produto.UnidadeMedidaId (bloqueia conversão).

UNIDADE DE ESTOQUE EXISTE EXPLICITAMENTE:
NÃO

CONVERSÕES EXISTENTES:
NÃO

MOVIMENTO PRESERVA UNIDADE ORIGINAL:
NÃO (apenas uma quantidade e uma unidade)

SALDO USA UNIDADE DO PRODUTO:
NÃO GARANTIDO (usa unidade do movimento; validação força igualdade na entrada direta, mas modelo permite divergência)

MODELO RECOMENDADO:
C

UNIDADE RECEBIDA:
DEFINIDA

CONVERSÃO GLOBAL:
DEFINIDA

CONVERSÃO POR PRODUTO:
DEFINIDA

CONVERSÃO POR LOTE:
DEFINIDA (preparação para futuro)

AUDITORIA DO FATOR:
DEFINIDA (campos no MovimentoEstoque)

IMPACTO FUTURO EM UL:
DEFINIDO (UL guarda ambas quantidades)

IMPACTO FUTURO EM PRODUÇÃO:
COMPATÍVEL

FASES 1B–1E:
DEFINIDAS

BLOQUEADORES DE ARQUITETURA:
- Produto não tem campo explícito "UnidadeEstoqueId" (usa UnidadeMedidaId) — precisa decisão: novo campo ou reuso com semântica clara
- UnidadeMedida não tem Dimensão — necessário para conversões globais seguras
- Índice único em SaldoEstoque (chave lógica) não existe — risco de duplicidade em concorrência
- Frontend lista todas unidades sem filtro — precisa integração com API de conversões válidas por produto

READY PARA EST-OP-02C.1B:
SIM
```

---

**Fim do Documento — EST-OP-02C.1A**