# EST-OP-02B.7 — INVESTIGAÇÃO DO AUTOMAPPER
## Correção do Mapeamento de Usuário na Entrada de Estoque

**Data:** 02/09/2026 18:31 UTC  
**Status:** Investigação Completa  
**Erro AutoMapper:** `MovimentoEstoqueCreateDto -> MovimentoEstoque (Destination Member: usuarioid)`

---

## 1. MAPEAMENTO DE TIPOS REAIS

### MovimentoEstoqueCreateDto.usuarioid
**Tipo:** `string?`  
**Origem:** EST-OP-02B.6 (alterado de `int?` para `string?`)  
**Valor Típico:** `"abc-123-def-456"` (JWT claim "id" do ASP.NET Identity)

### MovimentoEstoqueUpdateDto.usuarioid
**Tipo:** `string?`  
**Origem:** EST-OP-02B.6 (alterado de `int?` para `string?`)  
**Valor Típico:** `"abc-123-def-456"` (JWT claim "id" do ASP.NET Identity)

### MovimentoEstoque (Entidade Domain)
**Tipo de usuarioid:** `int?`  
**Localização:** `App.Domain.Entities.PRPA.MovimentoEstoque` (linha 45)  
**Definição:**
```csharp
public int? usuarioid { get; set; }
```

### Coluna CMOVIMENTOESTOQUE.usuarioid
**Tipo SQL:** `int`  
**Nullable:** SIM (int?)  
**Localização no Schema:** ProjetoContextModelSnapshot.cs, linhas 1364-1365  
**Definição EF Core:**
```csharp
b.Property<int?>("usuarioid")
    .HasColumnType("int");
```

### Foreign Key
**FK existe?** NÃO  
**Nenhuma FK para ApplicationUser ou outra tabela de usuários**  
**Comentário:** Campo legado sem constraint de integridade

---

## 2. MODELO DE USUÁRIO

### ApplicationUser (ASP.NET Identity)
**Localização:** `App.Infra.CrossCutting.Identity.Models.ApplicationUser`  
**Tipo PK:** `string` (herda de `IdentityUser`)  
**Exemplo ID:** `"550e8400-e29b-41d4-a716-446655440000"` (GUID string)

```csharp
public class ApplicationUser : IdentityUser
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime DateCreated { get; set; }
    public DateTime DateUpdated { get; set; }
}
```

### BaseEntity
**Localização:** `App.Domain.Entities.BaseEntity`  
**Campos de Auditoria:**
```csharp
public virtual string? UsuarioCriacao { get; set; }    // ← STRING
public virtual string? UsuarioEdicao { get; set; }      // ← STRING
```

### MovimentoEstoque
**Herança:** `BaseEntity`  
**Campo de Usuário Operacional:**
```csharp
public int? usuarioid { get; set; }  // ← INT? (LEGADO)
```

### Análise de Pertencimento

**MovimentoEstoque.usuarioid pertence a:**
- ❌ **NÃO ao ASP.NET Identity atual** (que é string)
- ❌ **NÃO a entidade legada visível** (nenhuma tabela Usuário com int PK encontrada)
- ✅ **SIM a campo histórico/legado sem FK** (apenas int? nullable, sem constraints)
- **Contexto:** Provavelmente representava um ID de usuário em sistema anterior (antes do Identity)

---

## 3. O PROBLEMA: STRING → INT?

**Situação:**
- Frontend envia: `usuarioid: "abc-123-def-456"` (string do JWT Identity)
- DTO após EST-OP-02B.6: `public string? usuarioid`
- AutoMapper tenta mapear: `string? → int?`
- **AutoMapper não consegue converter automaticamente string → int?**

**Por quê AutoMapper falha:**
```csharp
// Registro no MappingProfile (linha 217):
CreateMap<MovimentoEstoqueCreateDto, MovimentoEstoque>().ReverseMap();

// AutoMapper tenta:
// destino.usuarioid (int?) = source.usuarioid (string?)
// ❌ Falha: Não existe conversão mapeada
```

**Erro Runtime:**
```
AutoMapper.MappingException:
Error mapping MovimentoEstoqueCreateDto to MovimentoEstoque
Destination Member: usuarioid
```

---

## 4. DECISÃO ARQUITETURAL

### Princípio
Para operações autenticadas, o usuário deve ser obtido no backend via Claims/JWT, **não confiado do frontend**.

### Observação do Controller

Linhas 51-52 do MovimentoEstoqueController.cs:
```csharp
movimentoEstoque.UsuarioCriacao = User?.FindFirst(c => c.Type == "id")?.Value;
movimentoEstoque.UsuarioEdicao = User?.FindFirst(c => c.Type == "id")?.Value;
```

**Já está fazendo:** Capturar o usuário autenticado do Claims (não do DTO).

### Problema Existente

O campo `usuarioid` (legado, int?) não está sendo preenchido pelo controller, apenas pelos campos auditoria (UsuarioCriacao/UsuarioEdicao como string).

### Solução Recomendada

**Opção 1 (Recomendada - Minimal):** 
- Ignorar `usuarioid` no mapeamento do AutoMapper
- Deixar como NULL no banco
- Usar apenas `UsuarioCriacao` (string do Identity) para rastreabilidade
- **Impacto:** Zero - campo já é nullable, sem FK

**Opção 2 (Refatoração - Não Fazer Agora):**
- Adicionar FK para ApplicationUser
- Migração de usuarioid legado → ApplicationUserId (string)
- Bloqueado por: "NÃO iniciar EST-OP-02C, NÃO criar migrations adicionais"

---

## 5. CORREÇÃO DO AUTOMAPPER

### Estratégia
Ignorar campo `usuarioid` do DTO no mapeamento, deixar null na entidade.

### Implementação

**Arquivo:** `App.Infra.CrossCutting.IoC/MappingProfile.cs`

**Antes (linha 217-218):**
```csharp
CreateMap<MovimentoEstoqueCreateDto, MovimentoEstoque>().ReverseMap();
CreateMap<MovimentoEstoqueUpdateDto, MovimentoEstoque>().ReverseMap();
```

**Depois:**
```csharp
CreateMap<MovimentoEstoqueCreateDto, MovimentoEstoque>()
    .ForMember(d => d.usuarioid, o => o.Ignore())
    .ReverseMap();

CreateMap<MovimentoEstoqueUpdateDto, MovimentoEstoque>()
    .ForMember(d => d.usuarioid, o => o.Ignore())
    .ReverseMap();
```

**Justificativa:**
- ✅ Elimina erro de type mismatch
- ✅ Campo fica NULL (permitido pelo schema)
- ✅ Sem impacto em FK ou constraints (não existem)
- ✅ Sem refatoração do modelo
- ✅ Backend continua capturando usuário autenticado via UsuarioCriacao

---

## 6. TESTE DO POST (Após Correção)

### Cenário
Mesmo payload da Entrada de Estoque:
```json
{
  "produtoid": 5,
  "almoxarifadodestinoid": 3,
  "localizacaodestinoid": 4,
  "quantidade": 10.5,
  "unidademedidaid": 1,
  "tipodocumentoid": 1,
  "motivomovimentoid": 2,
  "datamovimento": "2026-09-02T18:20:15.000Z",
  "usuarioid": "abc-123-def-456",
  "estorno": false,
  "observacao": null
}
```

### Resultado Esperado

✅ HTTP 201 Created  
✅ Erro AutoMapper **ELIMINADO**  
✅ Registro criado  
✅ Campos preenchidos:
- `usuarioid`: NULL (ignorado pelo mapeamento)
- `UsuarioCriacao`: "abc-123-def-456" (do Claims do controller)
- `UsuarioEdicao`: "abc-123-def-456" (do Claims do controller)

---

## 7. VALIDAÇÃO DE FUNCIONALIDADE

⚠️ **NESTA ETAPA, NÃO VALIDAR:**
- ❌ Criação de UL
- ❌ Atualização de saldo
- ❌ Alocação em localização
- ❌ Repercussão em lote

**Validar apenas:**
✅ POST retorna 201  
✅ Registro persiste no banco  
✅ Sem erro AutoMapper

---

## 8. BUILD

### Backend
```bash
cd "D:\PROJETOS OFICIAIS\PROJETOS\INDUSTRIA40\BACKEND\PRPA"
dotnet clean
dotnet build
```

**Esperado:** ✅ SUCCESS (0 erros)

### Frontend
```bash
cd "D:\PROJETOS OFICIAIS\PROJETOS\INDUSTRIA40\FRONTEND"
npm run build
```

**Esperado:** ✅ SUCCESS

---

## 9. RESULTADO FINAL — EST-OP-02B.7

| Critério | Status | Detalhe |
|----------|--------|---------|
| **DTO usuarioid TIPO** | `string?` | Alterado em EST-OP-02B.6 |
| **ENTIDADE usuarioid TIPO** | `int?` | Legado, sem FK |
| **BANCO usuarioid TIPO** | `int` (nullable) | Schema EF Core |
| **IDENTITY ID TIPO** | `string` | ASP.NET Identity (GUID) |
| **CAUSA DO AUTOMAPPER** | Type mismatch: string? → int? | Sem conversão mapeada |
| **CORREÇÃO APLICADA** | `.ForMember(d => d.usuarioid, o => o.Ignore())` | Ignorar campo no DTO |
| **FRONTEND ENVIA usuarioid** | SIM | `"abc-123-def-456"` (JWT) |
| **BACKEND OBTÉM USUÁRIO VIA CLAIMS** | SIM | `User.FindFirst("id").Value` em UsuarioCriacao |
| **AUTOMAPPER** | ✅ PASS (após correção) | Sem erro de mapeamento |
| **POST ENTRADA HTTP** | 201 Created | Esperado após correção |
| **ERRO usuarioid** | ❌ ELIMINADO | Campo ignorado, NULL no banco |
| **NOVO ERRO** | Nenhum esperado | Apenas funcional de negócio |
| **BACKEND BUILD** | ⏳ PENDING* | Aguardando aplicação da correção |
| **FRONTEND BUILD** | ⏳ PENDING* | Sem alterações necessárias |
| **READY PARA NOVO TESTE VISUAL** | 🎯 SIM | Após builds |

*⏳ Aguardando servidor ser parado e correção aplicada

---

## 10. FLUXO FINAL (APÓS EST-OP-02B.7)

```
Frontend (Angular)
    ↓
POST /api/MovimentoEstoque
    {"usuarioid": "abc-123-def-456", ...}
    ↓
Backend - Model Binder
    ✅ Desserializa string? corretamente
    ↓
AutoMapper.Map<MovimentoEstoqueCreateDto → MovimentoEstoque>
    ✅ Ignora usuarioid (DTO não afeta entidade)
    ↓
Controller (POST)
    movimentoEstoque.UsuarioCriacao = "abc-123-def-456" (do JWT)
    movimentoEstoque.UsuarioEdicao = "abc-123-def-456" (do JWT)
    movimentoEstoque.usuarioid = NULL (ignorado, sem impacto)
    ↓
SaveAsync()
    ✅ Persiste no banco
    ↓
HTTP 201 Created
```

---

**Próximas Etapas:**
1. Aplicar correção no MappingProfile.cs
2. Executar builds (backend clean + build, frontend build)
3. Testar POST da Entrada
4. Após validação: documentar EST-OP-02B.7_RESULTADO_FINAL.md

---

**Data:** 02/09/2026 18:31 UTC  
**Status:** Pronto para Implementação
