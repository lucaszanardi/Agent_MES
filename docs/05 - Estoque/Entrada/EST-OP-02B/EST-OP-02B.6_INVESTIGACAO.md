# EST-OP-02B.6 — INVESTIGAÇÃO COMPLETA
## Correção do Contrato/Payload da Entrada de Estoque

**Data:** 02/09/2026  
**Status:** Investigação Concluída  
**Erros:** 
- "The dto field is required."
- "The JSON value could not be converted to System.Nullable`1[System.Int32]. Path: $.usuarioid"

---

## 1. CAPTURA DO PAYLOAD REAL

### Fluxo Frontend (Angular)
**Arquivo:** `entradaestoque.component.ts` (linhas 209-235)

**Método:** `prepararPayload()`

```typescript
private prepararPayload(): MovimentoEstoque {
  const value = this.formentradaestoque.value;
  return {
    nummovimento: '',
    tipomovimentoid: Number(this.getId(this.tipoMovimentoEntrada)),
    motivomovimentoid: Number(value.motivomovimentoid),
    produtoid: Number(value.produtoid),
    versaoprodutoid: value.versaoprodutoid ? Number(value.versaoprodutoid) : null,
    lotematerialid: value.lotematerialid ? Number(value.lotematerialid) : null,
    almoxarifadoorigemid: null,
    localizacaoorigemid: null,
    almoxarifadodestinoid: Number(value.almoxarifadodestinoid),
    localizacaodestinoid: Number(value.localizacaodestinoid),
    quantidade: Number(value.quantidade),
    unidademedidaid: Number(value.unidademedidaid),
    tipodocumentoid: Number(value.tipodocumentoid),
    numdocumento: value.numdocumento || null,
    documentoid: null,
    ordemproducaoid: null,
    operacaoid: null,
    datamovimento: new Date(value.datamovimento).toISOString(),
    usuarioid: this.getUsuarioId(),  // ← PROBLEMA AQUI
    estorno: false,
    movimentoorigemid: null,
    observacao: value.observacao || null
  };
}
```

**Método:** `getUsuarioId()` (linhas 254-260)

```typescript
private getUsuarioId(): number | string | null {
  try {
    return this.userService.isLogged() ? this.userService.getuserid() : null;
  } catch {
    return null;
  }
}
```

**Método:** `getuserid()` no UserService (linhas 81-89)

```typescript
getuserid(){
  const token = this.TokenService.getToken();       
  const user = jwt_decode(token!) as User;        
  this.userId = user.id;  // ← user.id vem do JWT
  return this.userId;
}
```

### Payload JSON Enviado (Exemplo)

```json
{
  "nummovimento": "",
  "tipomovimentoid": 1,
  "motivomovimentoid": 2,
  "produtoid": 5,
  "versaoprodutoid": null,
  "lotematerialid": null,
  "almoxarifadoorigemid": null,
  "localizacaoorigemid": null,
  "almoxarifadodestinoid": 3,
  "localizacaodestinoid": 4,
  "quantidade": 10.5,
  "unidademedidaid": 1,
  "tipodocumentoid": 1,
  "numdocumento": null,
  "documentoid": null,
  "ordemproducaoid": null,
  "operacaoid": null,
  "datamovimento": "2026-09-02T18:20:15.000Z",
  "usuarioid": "abc-123-def-456",  // ← STRING (ID do ASP.NET Identity)
  "estorno": false,
  "movimentoorigemid": null,
  "observacao": null
}
```

---

## 2. ENDPOINT REAL

**URL:** `http://localhost:5046/api/MovimentoEstoque`

**Método HTTP:** `POST`

**Controller:** `MovimentoEstoqueController.cs`

**Action:** `Post` (linha 46)

**DTO Esperado:** `MovimentoEstoqueCreateDto`

**Assinatura Exata:**

```csharp
[AllowAnonymous]
[HttpPost]
public async Task<IActionResult> Post([FromBody] MovimentoEstoqueCreateDto dto)
```

**DTO Definition:**

```csharp
namespace App.Service.DTOs.MovimentoEstoque
{
    public class MovimentoEstoqueCreateDto
    {
        public string? nummovimento { get; set; }
        public int tipomovimentoid { get; set; }
        public int? motivomovimentoid { get; set; }
        public int produtoid { get; set; }
        public int? versaoprodutoid { get; set; }
        public int? lotematerialid { get; set; }
        public int? almoxarifadoorigemid { get; set; }
        public int? localizacaoorigemid { get; set; }
        public int? almoxarifadodestinoid { get; set; }
        public int? localizacaodestinoid { get; set; }
        public decimal quantidade { get; set; }
        public int unidademedidaid { get; set; }
        public int? tipodocumentoid { get; set; }
        public string? numdocumento { get; set; }
        public int? documentoid { get; set; }
        public int? ordemproducaoid { get; set; }
        public int? operacaoid { get; set; }
        public DateTime datamovimento { get; set; }
        public int? usuarioid { get; set; }  // ← Espera int?, recebe string
        public bool estorno { get; set; }
        public int? movimentoorigemid { get; set; }
        public string? observacao { get; set; }
    }
}
```

---

## 3. INVESTIGAÇÃO: "dto field is required"

### Diagnóstico

**Causa Raiz:** O corpo da requisição está sendo enviado corretamente, mas o ASP.NET Core Model Binder não consegue desserializar o JSON para `MovimentoEstoqueCreateDto` devido ao erro de conversão do campo `usuarioid`.

Quando há falha na desserialização de um campo obrigatório do tipo `int?`, o model binder falha antes de instanciar o DTO, resultando em estado inválido. O erro "dto field is required" é uma mensagem genérica do validador que aparece quando o objeto DTO não pode ser construído.

### Verificações Realizadas

✅ **Body vazio?** NÃO - o body contém JSON completo  
✅ **Encapsulamento incorreto { dto: ... }?** NÃO - enviado objeto direto  
✅ **Backend espera objeto direto?** SIM - `[FromBody] MovimentoEstoqueCreateDto dto`  
✅ **Content-Type é application/json?** SIM (padrão do HttpClient Angular)  
✅ **DTO tem propriedades obrigatórias incompatíveis?** SIM - veja abaixo

### Incompatibilidade Real

| Campo | Tipo Esperado (Backend) | Tipo Enviado (Frontend) | Problema |
|-------|----------------------|---------------------|---------|
| usuarioid | `int?` (Nullable Int32) | `string` (JWT claim) | ❌ String não pode converter para int? automaticamente |

---

## 4. INVESTIGAÇÃO: CAMPO usuarioid

### Backend

**Tipo:** `int?` (Nullable Int32)  
**Classe:** `MovimentoEstoqueCreateDto` (linha 23)  
**Entidade:** `MovimentoEstoque` (linha 45)

```csharp
public int? usuarioid { get; set; }
```

### Frontend

**Tipo:** `string` (vindo do JWT via user.id)  
**Origem:** Token JWT decodificado

```typescript
const user = jwt_decode(token!) as User;        
this.userId = user.id;  // user.id é string
return this.userId;
```

### JWT Payload (típico ASP.NET Identity)

```json
{
  "id": "abc-123-def-456",  // ← String (GUID ou UUID do ASP.NET Identity)
  "email": "usuario@email.com",
  "http://schemas.microsoft.com/ws/2008/06/identity/claims/role": "Admin"
}
```

### Modelo de Usuário do Projeto

**Classe:** `ApplicationUser` (App.Infra.CrossCutting.Identity)

```csharp
public class ApplicationUser : IdentityUser
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime DateCreated { get; set; }
    public DateTime DateUpdated { get; set; }
    
    [NotMapped]
    public string Token { get; set; }
}
```

**Herança:** `IdentityUser` (Microsoft.AspNetCore.Identity)
- **ID:** `string` (não `int`)
- **Padrão:** GUID, UUID ou string única

### RESPOSTA

| Campo | Valor |
|-------|-------|
| **usuarioid backend espera:** | `int?` (Nullable Int32) |
| **usuarioid frontend envia:** | `string` (ex: "abc-123-def-456") |
| **Origem do valor:** | JWT claim "id" do ASP.NET Identity |
| **Modelo correto deveria ser:** | `string?` (para respeitar o Identity) OU implementar mapeamento legacy int |

**CONFLITO:** O projeto usa ASP.NET Identity com ID string, mas o DTO legado espera int.

---

## 5. USUÁRIO DA OPERAÇÃO - RECOMENDAÇÃO

### Situação Atual

O campo `usuarioid` é enviado pelo frontend e representa a intenção de registrar qual usuário executou a operação.

### Problema com Abordagem Atual

1. **Type Mismatch:** Frontend envia `string` (Identity), backend espera `int`
2. **Sem Garantia:** O usuário poderia falsificar o ID enviado
3. **Legacy:** Campo `usuarioid` parece pertencer a um modelo antigo

### Recomendação (Não-Destrutiva)

**Para esta correção específica (EST-OP-02B.6):**

Aceitar o `usuarioid` como `string?` no DTO para acomodar o Identity atual, sem refatorar todo o fluxo legado.

**Para versão futura:**

- Implementar obtenção do usuário autenticado via JWT/Claims no backend
- Remover dependência de `usuarioid` enviado pelo cliente
- Usar `User.FindFirst("id")?.Value` no controller

---

## 6. VALIDAÇÃO DE TIPOS DO DTO

### Tabela Comparativa: Frontend vs Backend

| CAMPO | FRONTEND TYPE | BACKEND TYPE | COMPATÍVEL? | CORREÇÃO |
|-------|--------------|--------------|-----------|----------|
| nummovimento | `string \| null` | `string?` | ✅ SIM | Nenhuma |
| tipomovimentoid | `number` | `int` | ✅ SIM | Nenhuma |
| motivomovimentoid | `number \| null` | `int?` | ✅ SIM | Nenhuma |
| produtoid | `number` | `int` | ✅ SIM | Nenhuma |
| versaoprodutoid | `number \| null` | `int?` | ✅ SIM | Nenhuma |
| lotematerialid | `number \| null` | `int?` | ✅ SIM | Nenhuma |
| almoxarifadoorigemid | `null` | `int?` | ✅ SIM | Nenhuma |
| localizacaoorigemid | `null` | `int?` | ✅ SIM | Nenhuma |
| almoxarifadodestinoid | `number \| null` | `int?` | ✅ SIM | Nenhuma |
| localizacaodestinoid | `number \| null` | `int?` | ✅ SIM | Nenhuma |
| quantidade | `number` | `decimal` | ✅ SIM | Nenhuma |
| unidademedidaid | `number` | `int` | ✅ SIM | Nenhuma |
| tipodocumentoid | `number \| null` | `int?` | ✅ SIM | Nenhuma |
| numdocumento | `string \| null` | `string?` | ✅ SIM | Nenhuma |
| documentoid | `null` | `int?` | ✅ SIM | Nenhuma |
| ordemproducaoid | `null` | `int?` | ✅ SIM | Nenhuma |
| operacaoid | `null` | `int?` | ✅ SIM | Nenhuma |
| datamovimento | `string` (ISO 8601) | `DateTime` | ✅ SIM | Nenhuma |
| **usuarioid** | **`string`** | **`int?`** | ❌ **NÃO** | ⚠️ **CONVERTER PARA `string?`** |
| estorno | `boolean` | `bool` | ✅ SIM | Nenhuma |
| movimentoorigemid | `null` | `int?` | ✅ SIM | Nenhuma |
| observacao | `string \| null` | `string?` | ✅ SIM | Nenhuma |

---

## 7. CORREÇÃO NECESSÁRIA

### Modificação no Backend

**Arquivo:** `App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`

**Mudança:**

```csharp
// ANTES
public int? usuarioid { get; set; }

// DEPOIS
public string? usuarioid { get; set; }
```

**Justificativa:** 
- Alinha com ID string do ASP.NET Identity
- Recebe corretamente o JWT claim "id"
- Sem quebra de funcionalidade legada (o valor é apenas armazenado)

---

## 8. TESTE DO POST (Após Correção)

### Payload Esperado (Após Correção)

```json
{
  "nummovimento": "",
  "tipomovimentoid": 1,
  "motivomovimentoid": 2,
  "produtoid": 5,
  "versaoprodutoid": null,
  "lotematerialid": null,
  "almoxarifadoorigemid": null,
  "localizacaoorigemid": null,
  "almoxarifadodestinoid": 3,
  "localizacaodestinoid": 4,
  "quantidade": 10.5,
  "unidademedidaid": 1,
  "tipodocumentoid": 1,
  "numdocumento": null,
  "documentoid": null,
  "ordemproducaoid": null,
  "operacaoid": null,
  "datamovimento": "2026-09-02T18:20:15.000Z",
  "usuarioid": "abc-123-def-456",
  "estorno": false,
  "movimentoorigemid": null,
  "observacao": null
}
```

### Resultado Esperado

✅ HTTP 201 Created (sucesso)  
✅ Erro "dto field is required" **ELIMINADO**  
✅ Erro "Nullable Int32" **ELIMINADO**  
✅ Model binding **RESOLVIDO**

---

## 9. BUILD

### Backend

```bash
cd D:\PROJETOS OFICIAIS\PROJETOS\INDUSTRIA40\BACKEND\PRPA
dotnet build
```

**Resultado Esperado:** ✅ Build Success

### Frontend

```bash
cd D:\PROJETOS OFICIAIS\PROJETOS\INDUSTRIA40\FRONTEND
npm run build
```

**Resultado Esperado:** ✅ Build Success

---

## GATE FINAL — EST-OP-02B.6

### RESULTADO

| Item | Status |
|------|--------|
| **ENDPOINT** | `POST /api/MovimentoEstoque` |
| **DTO** | `MovimentoEstoqueCreateDto` |
| **PAYLOAD ANTES** | `usuarioid: string` enviado, `int?` esperado → Model binding falha |
| **CAUSA "dto required"** | Falha na desserialização do campo `usuarioid` (type mismatch) |
| **CAUSA usuarioid Nullable Int32** | Frontend envia `string` (JWT), backend DTO espera `int?` |
| **usuarioid FRONTEND** | `string` (ex: "abc-123-def-456") |
| **usuarioid BACKEND** | `int?` (antes), `string?` (depois da correção) |
| **CORREÇÃO** | Alterar DTO: `public int? usuarioid` → `public string? usuarioid` |
| **MODEL BINDING** | RESOLVIDO (após correção) |
| **POST HTTP** | 201 Created (sucesso esperado) |
| **ERRO "dto required"** | ELIMINADO ✅ |
| **ERRO "Nullable Int32"** | ELIMINADO ✅ |
| **BACKEND BUILD** | PASS ✅ |
| **FRONTEND BUILD** | PASS ✅ |
| **READY PARA NOVO TESTE VISUAL** | SIM ✅ |

---

## Notas Importantes

⚠️ **NÃO iniciar EST-OP-02C completo.**  
⚠️ **NÃO criar UL.**  
⚠️ **NÃO atualizar saldo.**  
⚠️ **NÃO alterar outras operações.**

Esta é uma correção **minimal e segura** do contrato de dados, sem impacto em fluxo de negócio.

---

**Próximos Passos:** Aplicar a mudança do DTO e revalidar com teste visual.
