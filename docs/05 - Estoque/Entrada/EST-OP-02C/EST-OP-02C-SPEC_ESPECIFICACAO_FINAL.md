# EST-OP-02C-SPEC — ESPECIFICAÇÃO FINAL DA ENTRADA DE ESTOQUE

**Data**: 03/09/2026  
**Status**: 🟡 ESPECIFICAÇÃO EM CONSOLIDAÇÃO  
**Objetivo**: Consolidar a especificação funcional e técnica definitiva da tela Entrada de Estoque antes de continuar a implementação.

**Restrições**:
- ❌ NÃO alterar código
- ❌ NÃO alterar banco
- ❌ NÃO criar migration
- ❌ NÃO implementar nenhuma regra
- ❌ NÃO alterar outras telas

**Objetivo**: Apenas documentar e fechar decisões.

---

## 1. DECISÕES ARQUITETURAIS JÁ APROVADAS

### Decisão Oficial (EST-OP-02C-ARCH)

✅ **OPTION B aprovada**: SaldoEstoque como Fonte de Verdade + UnidadeLogistica Opcional

**Registrar como decisão oficial:**

1. ✅ **SaldoEstoque** = fonte de verdade quantitativa do estoque
   - Campos: `qtdfisica`, `qtdreservada`, `qtdbloqueada`, `qtddisponivel`
   - Chave lógica: `(produtoid, almoxarifadoid, localizacaoestoqueid, lotematerialid)`
   - Status: Implementado no domínio (App.Domain.Entities.PRPA.SaldoEstoque)

2. ✅ **UnidadeLogistica** = opcional
   - Representa unidade física identificável (pallet, big bag, caixa, bobina, tambor, container)
   - Status: Implementado no domínio (App.Domain.Entities.Estoque.UnidadesLogisticas)

3. ✅ **Estoque pode existir sem UL**
   - Entrada direta (sem unidade logística vinculada)
   - Status: Modelo suporta (UnidadeLogisticaId nullable)

4. ✅ **MovimentoEstoque** = histórico/evento da movimentação
   - Imutável após criação
   - Status: Implementado (App.Domain.Entities.Estoque.Movimentacoes.MovimentacaoDeEstoque)

5. ✅ **LocalizacaoEstoque** = posição física
   - Deve ser ativa, não bloqueada, permite entrada
   - Status: Implementado (modelo estruturado)

6. ✅ **Mapa do Estoque** deverá futuramente mostrar:
   - Estoque direto (de SaldoEstoque)
   - Estoque em UL (de UnidadeLogistica)
   - Status: Futuro (Fase C1 ou depois)

7. ✅ **Destino da Entrada** = somente localização elegível para armazenagem
   - Classificação efetiva: ARMAZENA
   - Status: Validado no frontend (getLocalizacaoElegiveisEntrada())

8. ✅ **Produto com controle de lote** exige lote
   - Produto sem lote = lote opcional
   - Produto com lote = lote obrigatório
   - Status: Lógica de negócio (não implementada em regras ainda)

9. ✅ **Produto/lote que exige inspeção** → quarentena
   - Se `exigeinspecao = true` → destino = quarentena
   - Se `exigeinspecao = false` → destino = normal
   - Status: Campo existe no modelo (RecebimentoEstoqueItem.exigeinspecao), lógica não implementada

---

## 2. MODELOS DE ENTRADA

### A) ENTRADA DIRETA (Sem Unidade Logística)

**Campos obrigatórios**:
- Produto ✅
- Versão (se aplicável) ✅
- Lote (se obrigatório) ✅
- Quantidade ✅
- Unidade ✅
- Almoxarifado ✅
- Localização ✅

**Resultado**:
- MovimentoEstoque criado ✅
- SaldoEstoque criado/atualizado ⏳ (sincronização pendente)
- SEM UnidadeLogistica ✅

**Status atual**:
- Frontend: Suporta (entradaestoque.component.ts)
- Backend: Suporta criação de MovimentoEstoque, mas SEM sincronização automática com SaldoEstoque
- Bloqueador: Falta handler que sincroniza MovimentoEstoque → SaldoEstoque

---

### B) ENTRADA COM UNIDADE LOGÍSTICA

**Campos obrigatórios**:
- Produto ✅
- Versão (se aplicável) ✅
- Lote (se obrigatório) ✅
- Quantidade ✅
- Unidade ✅
- Almoxarifado ✅
- Localização ✅
- UnidadeLogistica (nova ou existente) ⏳

**Resultado**:
- MovimentoEstoque criado ✅
- SaldoEstoque criado/atualizado ⏳
- UnidadeLogistica vinculada ⏳
- UnidadeLogistica posicionada ⏳

**Status atual**:
- Frontend: NÃO suporta (formulário não inclui campos de UL)
- Backend: Modelo existe, mas entrada não integra UL
- Bloqueador: Formulário não foi desenvolvido

---

## 3. TELA — ESTRUTURA FINAL

### Blocos de Formulário

#### BLOCO 1: MATERIAL
- **Produto** (obrigatório, select)
- **Versão** (se aplicável, select dinamicamente filtrado)
- **Lote** (se obrigatório para o produto, select ou criar novo)
- **Quantidade** (obrigatório, numérico, > 0)
- **Unidade de Medida** (obrigatório, select)

**Status atual**: ✅ Implementado no frontend

---

#### BLOCO 2: FORMA DE RECEBIMENTO
- **Estoque direto** (radio button)
- **Unidade Logística** (radio button)

**Status atual**: ❌ NÃO IMPLEMENTADO

**Comportamento esperado**:
- Ao selecionar "Estoque direto" → Bloco 3 desaparece
- Ao selecionar "Unidade Logística" → Bloco 3 aparece

---

#### BLOCO 3: UNIDADE LOGÍSTICA (Condicional)
*Aparece apenas quando "Forma de Recebimento" = "Unidade Logística"*

- **Opção 1**: Criar nova UL
- **Opção 2**: Informar/ler UL existente
- **Código/Etiqueta** (obrigatório se UL)
- **Tipo UL** (select, se houver modelo existente)
- **Quantidade na UL** (numérico, > 0)

**Status atual**: ❌ NÃO IMPLEMENTADO

---

#### BLOCO 4: DESTINO
- **Almoxarifado** (obrigatório, select)
- **Área** (condicional, se necessário)
- **Localização** (obrigatório, select filtrado por almoxarifado)
  - Apenas localizações elegíveis para armazenagem (ARMAZENA)
  - Backend filtra via `getLocalizacaoElegiveisEntrada()`

**Status atual**: ✅ Parcialmente implementado (almoxarifado + localização OK)

---

#### BLOCO 5: QUALIDADE
- **Exige inspeção?** (checkbox, derivado do cadastro do produto/lote)
- **Destino de quarentena** (se inspeção exigida)
  - Apenas localizações pertencentes à área de quarentena
- **Status inicial** (se modelo existente)

**Status atual**: ❌ NÃO IMPLEMENTADO

**Lógica esperada**:
```
se Produto/Lote.exigeinspecao = true:
  → Mostrar aviso: "Este material exige inspeção"
  → Filtrar localizações apenas para quarentena
  → Status = Quarentena (pendente liberação)
senão:
  → Localização normal (ARMAZENA não-quarentena)
  → Status = Liberado
```

---

#### BLOCO 6: DOCUMENTO
- **Motivo da entrada** (obrigatório, select)
- **Tipo de documento** (obrigatório, select)
- **Número do documento** (opcional, texto)
- **Data do movimento** (obrigatório, datetime, padrão = agora)
- **Observação** (opcional, textarea)

**Status atual**: ✅ Implementado no frontend

---

#### BLOCO 7: AÇÃO
- **Confirmar Entrada** (botão)
- **Limpar** (botão)

**Status atual**: ✅ Implementado (confirmarEntrada + limparFormulario)

---

#### BLOCO 8: HISTÓRICO
- **Últimas entradas** (tabela, últimas 5-10)

**Colunas mínimas**:
| Coluna | Status |
|--------|--------|
| Número do Movimento | ❌ |
| Produto | ✅ |
| Versão | ⏳ |
| Lote | ✅ |
| Quantidade | ✅ |
| Unidade | ✅ |
| Almoxarifado | ❌ |
| Localização | ❌ |
| Forma de Recebimento | ❌ |
| UL (se houver) | ❌ |
| Documento | ⏳ |
| Data | ⏳ |
| Usuário | ✅ |

**Status atual**: ⏳ Parcialmente implementado (carregarUltimasEntradas filtra apenas as últimas 5, mas sem todas as colunas)

---

## 4. PRODUTO / VERSÃO / LOTE

### Regras de Versão

| Cenário | Regra | Status |
|---------|-------|--------|
| Produto sem versão | Versão é opcional | ✅ Modelo suporta (versaoprodutoid nullable) |
| Produto com versão | Selecionar versão válida | ✅ Frontend filtra (filtrarVersoesELotes) |

**Status atual**: ✅ Implementado

---

### Regras de Lote

| Cenário | Regra | Status |
|---------|-------|--------|
| Produto sem controle de lote | Lote é opcional/não controlado | ✅ Modelo suporta (lotematerialid nullable) |
| Produto com controle de lote | Lote é obrigatório | ❌ Validação não implementada |

**Status atual**: ⏳ Modelo suporta, mas validação de obrigatoriedade não existe

---

### Opções de Lote na Entrada

**Recomendação**: Permitir ambos (selecionar lote existente + criar novo lote)

| Opção | Status | Esforço |
|-------|--------|--------|
| Selecionar lote existente | ✅ Implementado (formulário mostra lotesFiltrados) | 0h |
| Criar novo lote durante recebimento | ❌ NÃO IMPLEMENTADO | 8-10h |

**Campos mínimos para criação de lote na Entrada** (documentar, não implementar):
- Código/lote interno (obrigatório)
- Lote fornecedor (obrigatório)
- Fabricação (obrigatório, date)
- Validade (obrigatório, date)
- Certificado (opcional, texto)
- Fornecedor (referência)
- Exige inspeção (checkbox)

---

## 5. UNIDADE LOGÍSTICA

### Definição Semântica

**UnidadeLogistica** representa unidade física identificável/manuseável, como:
- Pallet ✅
- Big bag ✅
- Caixa ✅
- Bobina ✅
- Tambor ✅
- Container ✅

**Status**: ✅ Definição clara

---

### Regras de Entrada

| Tipo de Entrada | UnidadeLogisticaId | Comportamento | Status |
|-----------------|-------------------|---------------|--------|
| Entrada direta | NULL / ausência | Sem vínculo | ✅ Suportado |
| Entrada com UL | Obrigatório | Vinculada + posicionada | ⏳ Modelo existe, UI não |

**Status atual**: ⏳ Modelo suporta, mas entrada com UL não foi desenvolvida no frontend

---

### Vínculo entre SaldoEstoque e UL

**Modelo esperado**:
- SaldoEstoque pode estar vinculado a UL (se modelo atual permitir)
- OU manter vínculo equivalente definido pelo domínio
- Não criar nova tabela, usar estrutura existente

**Status atual**: ✅ Arquitetura suporta (SaldoEstoque + UnidadeLogistica independentes)

---

## 6. QUARENTENA

### Modelo Operacional

```
Se Produto/Lote.exigeinspecao = true:
  ├─ Entrada → Localização na área de quarentena
  ├─ Localização ARMAZENA dentro da quarentena
  ├─ Status de qualidade = Pendente/Quarentena
  └─ NÃO permitir entrada direta em estoque liberado

Se Produto/Lote.exigeinspecao = false:
  └─ Permitir destino normal de armazenagem
```

**Status atual**: ❌ Lógica não implementada

---

### Pré-requisito Cadastral

Para quarentena funcionar, deve existir:
- ✅ Área de Estoque classificada como Quarentena (cadast possível)
- ✅ Pelo menos uma Localização ARMAZENA válida dentro dela

**Status atual**: ✅ Modelo existe, mas sem validação em entrada

---

### Distinções Importantes

| Conceito | Função | Status |
|----------|--------|--------|
| Área de Quarentena | Classificação de espaço | ✅ Existe |
| Localização física | Posição dentro da área | ✅ Existe |
| Status do lote | Estado de qualidade | ⏳ Parcial |
| Bloqueio de estoque | Restrição de acesso | ✅ Modelo existe |

---

## 7. SALDOESTOQUE

### Comportamento ao Confirmar Entrada

```
1. Buscar SaldoEstoque pela chave lógica
   (produtoid, almoxarifadoid, localizacaoestoqueid, lotematerialid)

2. Se não existe:
   → Criar novo com qtdfisica = quantidade
   
3. Se existe:
   → Incrementar qtdfisica += quantidade
   
4. Recalcular qtddisponivel:
   QtdDisponivel = QtdFisica - QtdReservada - QtdBloqueada
```

**Status atual**: ❌ Sincronização não implementada

**Bloqueador crítico**: Handler que executa passo 1-4 quando MovimentoEstoque é criado

---

### Chave Lógica

**Definição**: `(produtoid, almoxarifadoid, localizacaoestoqueid, lotematerialid)`

**Status**: ✅ Modelo suporta todos os campos

**Falta**: Índice UNIQUE para evitar duplicação

---

## 8. MOVIMENTOESTOQUE

### Natureza

- MovimentoEstoque = histórico/razão da operação
- Permanece imutável após criação
- Cada entrada gera um movimento

### Estado vs. História

```
Exemplo:

Movimento 1: +100 (criado em 01/09 10:00)
Movimento 2: +50  (criado em 01/09 11:00)
─────────────────────────
Saldo atual: 150
```

**Status atual**: ✅ Implementado (MovimentacaoDeEstoque é imutável)

---

## 9. LOCALIZAÇÃO DESTINO

### Regra Única

Destino deve ser:
- ✅ Ativo
- ✅ Não bloqueado
- ✅ Permite entrada (flag)
- ✅ Classificação efetiva = ARMAZENA
- ✅ Pertencente ao almoxarifado/contexto selecionado

**Se quarentena**: Além disso, deve pertencer à área/configuração de quarentena

**Responsabilidade**: Backend decide elegibilidade, frontend apenas exibe

**Status atual**: ✅ Backend filtra via `getLocalizacaoElegiveisEntrada()`

---

## 10. ÚLTIMAS ENTRADAS — HISTÓRICO

### Colunas Mínimas

| # | Coluna | Necessário | Status |
|---|--------|-----------|--------|
| 1 | Número do Movimento | SIM | ⏳ |
| 2 | Produto | SIM | ✅ |
| 3 | Versão | SIM | ⏳ |
| 4 | Lote | SIM | ✅ |
| 5 | Quantidade | SIM | ✅ |
| 6 | Unidade | SIM | ✅ |
| 7 | Almoxarifado | SIM | ❌ |
| 8 | Localização | SIM | ❌ |
| 9 | Forma de Recebimento | SIM | ❌ |
| 10 | UL (se houver) | CONDICIONAL | ❌ |
| 11 | Documento | SIM | ⏳ |
| 12 | Data | SIM | ⏳ |
| 13 | Usuário | SIM | ✅ |

**Status atual**: Histó​rico carregado, mas sem todas as colunas

---

## 11. FLUXO PONTA A PONTA — ENTRADA DIRETA

### Cenário de Teste

```
1. Selecionar produto sem necessidade de UL
2. Informar lote (se obrigatório)
3. Informar quantidade
4. Selecionar destino ARMAZENA
5. Confirmar

Resultado esperado:
├─ MovimentoEstoque criado ✅
├─ SaldoEstoque criado/atualizado ⏳
├─ Nenhuma UL criada ✅
├─ Entrada aparece no histórico ⏳
└─ Saldo aparece futuramente no Mapa ❌ (Futuro)
```

**Status atual**: ⏳ Falta sincronização SaldoEstoque

---

## 12. FLUXO PONTA A PONTA — ENTRADA COM UL

### Cenário de Teste

```
1. Selecionar produto
2. Escolher Forma = Unidade Logística
3. Criar/selecionar UL
4. Informar lote
5. Informar quantidade
6. Selecionar destino
7. Confirmar

Resultado esperado:
├─ MovimentoEstoque criado ❌
├─ SaldoEstoque criado/atualizado ❌
├─ UL criada/vinculada ❌
├─ UL posicionada ❌
└─ Histórico atualizado ❌
```

**Status atual**: ❌ Fluxo não implementado (Fase 2)

---

## 13. FLUXO QUARENTENA

### Cenário de Teste

```
1. Produto/lote exige inspeção
2. Sistema identifica necessidade
3. Destinos normais NÃO permitidos
4. Destinos de quarentena oferecidos
5. Entrada confirmada em localização de quarentena

Resultado esperado:
├─ Entrada confirmada ❌
├─ SaldoEstoque criado/atualizado ❌
├─ Status de qualidade = Quarentena ❌
└─ Material NÃO disponível até liberação ❌
```

**Status atual**: ❌ Fluxo não implementado (Fase 3)

---

## 14. FASES DE IMPLEMENTAÇÃO

### EST-OP-02C.1: Entrada Direta → SaldoEstoque

**Objetivo**: Sincronizar MovimentoEstoque com SaldoEstoque

**Arquivos prováveis**:
- Handler: `CriarMovimentacaoDeEstoqueHandler.cs`
- Event: `MovimentacaoDeEstoqueCriada.cs` (já existe)
- Service: `MovimentoEstoqueServices.cs`
- Controller: `MovimentoEstoqueController.cs`

**Banco/Migration**: Possível adicionar RowVersion a SaldoEstoque (não agora)

**Testes**:
- E2E: Entrada → MovimentoEstoque → SaldoEstoque atualizado

**Critério de Aceite**:
- ✅ Entrada criada, SaldoEstoque sincronizado automaticamente
- ✅ Quantidade disponível reflete entrada

---

### EST-OP-02C.2: Entrada com UL Opcional

**Objetivo**: Permitir entrada com UnidadeLogistica

**Arquivos prováveis**:
- Frontend: `entradaestoque.component.html/ts` (novo bloco Forma de Recebimento + Bloco 3 UL)
- Backend: Novo handler ou extensão do existente
- Contracts: Novo DTO para entrada com UL

**Banco/Migration**: Possível vínculo SaldoEstoque ↔ UL

**Testes**:
- E2E: Entrada com UL → UL criada/vinculada → SaldoEstoque atualizado

**Critério de Aceite**:
- ✅ Usuário consegue criar entrada com UL
- ✅ UL posicionada na localização
- ✅ SaldoEstoque vinculado à UL (se modelo permitir)

---

### EST-OP-02C.3: Quarentena / Inspeção

**Objetivo**: Rotear entradas com inspeção para quarentena

**Arquivos prováveis**:
- Frontend: Novo bloco 5 (Qualidade)
- Backend: Validação de quarentena + filtro de localizações
- Service: Lógica de detecção de necessidade de inspeção

**Banco/Migration**: Possível campos de status de qualidade

**Testes**:
- E2E: Produto com inspeção → Localização quarentena oferecida
- E2E: Entrada em quarentena → Status = Pendente até liberação

**Critério de Aceite**:
- ✅ Produtos que exigem inspeção não podem ir direto para estoque
- ✅ Sistema oferece apenas localizações de quarentena
- ✅ Material fica bloqueado até liberação

---

### EST-OP-02C.4: Últimas Entradas / Histórico Ampliado

**Objetivo**: Mostrar histórico completo com todas as colunas

**Arquivos prováveis**:
- Frontend: Tabela ampliada em `entradaestoque.component.html`
- Backend: Query expandida em `MovimentoEstoqueService`

**Banco/Migration**: Nenhuma

**Testes**:
- E2E: Entrada criada → Aparece no histórico com todos os dados

**Critério de Aceite**:
- ✅ Histórico mostra todas as 13 colunas
- ✅ Dados são precisos e atualizados

---

### EST-OP-02C.5: E2E e Homologação Final

**Objetivo**: Validar fluxo completo com dados reais

**Testes**:
- Entrada direta completa
- Entrada com UL completa
- Entrada com quarentena completa
- Performance sob carga

**Critério de Aceite**:
- ✅ Todos os fluxos funcionam
- ✅ Sem erros em produção-like
- ✅ Performance aceitável

---

## 15. NÃO EXPANDIR ESCOPO

### Fora do Escopo de EST-OP-02C

| Funcionalidade | Motivo | Fase Futura |
|---|---|---|
| Transferência | Outro domínio | Depois |
| Reserva | Outra operação | EST-OP-02C-ARCH Fase B1 |
| Bloqueio | Outra operação | EST-OP-02C-ARCH Fase B1 |
| Inventário | Outra operação | EST-OP-02C-ARCH Fase B1 |
| Ajuste | Outra operação | Depois |
| Saída | Outra operação | Depois |
| Qualidade completa | Domínio específico | Depois |
| WMS avançado | Fora de escopo | Depois |
| Put-away automático | Fora de escopo | Depois |
| FIFO/FEFO | Fora de escopo | Depois |
| Impressão de etiquetas avançada | Fora de escopo | Depois |

---

## 16. GATE FINAL — EST-OP-02C-SPEC

### Checklist de Aprovação

| # | Item | Status | Responsável |
|---|------|--------|-------------|
| 1 | ARQUITETURA SALDOESTOQUE | ✅ APROVADA | CTO |
| 2 | UL OPCIONAL | ✅ SIM | CTO |
| 3 | ENTRADA DIRETA | ✅ DEFINIDA | Tech Lead |
| 4 | ENTRADA COM UL | ⏳ DEFINIDA (Fase 2) | Tech Lead |
| 5 | QUARENTENA | ⏳ DEFINIDA (Fase 3) | Tech Lead |
| 6 | LOTE NO RECEBIMENTO | ⏳ DEFINIDO (selecionar + criar) | Tech Lead |
| 7 | DESTINO SOMENTE ARMAZENA | ✅ SIM | Tech Lead |
| 8 | ÚLTIMAS ENTRADAS | ⏳ DEFINIDAS (Fase 4) | Tech Lead |
| 9 | FASES 02C.1 A 02C.5 | ✅ DEFINIDAS | Tech Lead |

---

### Bloqueadores de Especificação

| # | Bloqueador | Impacto | Ação |
|---|-----------|--------|------|
| B1 | Handler sincronização SaldoEstoque | 🔴 CRÍTICO | Desenvolver em Fase 1 |
| B2 | Modelo de criação de lote na entrada | 🟡 MÉDIO | Documentar, não implementar agora |
| B3 | Lógica de detecção de quarentena | 🟡 MÉDIO | Implementar em Fase 3 |
| B4 | Formulário entrada com UL | 🟡 MÉDIO | Implementar em Fase 2 |

---

### Status de Prontidão

**READY PARA EST-OP-02C.1?** ✅ **SIM**

**Condições**:
- ✅ Arquitetura OPTION B aprovada
- ✅ Modelo de domínio existe
- ✅ Frontend parcialmente implementado
- ✅ Especificação finalizada
- ⏳ Aguardando: Handler de sincronização SaldoEstoque

---

## 17. RESULTADO FINAL DA ESPECIFICAÇÃO

```
┌─────────────────────────────────────────────────┐
│  EST-OP-02C-SPEC — ESPECIFICAÇÃO FINAL         │
├─────────────────────────────────────────────────┤
│                                                 │
│  DECISÕES ARQUITETURAIS:     ✅ APROVADAS      │
│  MODELOS DE ENTRADA:         ✅ DEFINIDOS      │
│  ESTRUTURA DE TELA:          ✅ DEFINIDA       │
│  QUARENTENA:                 ✅ DEFINIDA       │
│  FASES:                      ✅ DEFINIDAS      │
│  BLOQUEADORES:               ✅ IDENTIFICADOS  │
│                                                 │
│  STATUS: 🟢 PRONTO PARA IMPLEMENTAÇÃO          │
│                                                 │
│  Próximo Passo: Kickoff EST-OP-02C.1           │
│  Data: 03/09/2026 09:00 UTC                    │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

## APÊNDICE A: MAPEAMENTO DE CAMPOS

### RecebimentoEstoque vs. Entrada de Estoque

| Campo da Entrada | BD (RecebimentoEstoque) | Status |
|-----------------|------------------------|--------|
| Produto | recebimentoestoque_item.produtoid | ✅ |
| Versão | recebimentoestoque_item.versaoprodutoid | ✅ |
| Lote | recebimentoestoque_item.lotematerialid | ✅ |
| Quantidade | recebimentoestoque_item.qtdrecebida | ✅ |
| Unidade | recebimentoestoque_item.unidademedidaid | ✅ |
| Almoxarifado | recebimentoestoque_item.almoxarifadoid | ✅ |
| Localização | recebimentoestoque_item.localizacaoestoqueid | ✅ |
| Exige Inspeção | recebimentoestoque_item.exigeinspecao | ✅ |
| Status Qualidade | recebimentoestoque_item.statusqualidadeid | ✅ |
| Motivo | recebimentoestoque.statusrecebimentoid | ⏳ |
| Tipo Documento | recebimentoestoque.tipodocumentoid | ✅ |
| Número Documento | recebimentoestoque.numdocumento | ✅ |
| Data Movimento | recebimentoestoque.datarecebimento | ✅ |
| Observação | recebimentoestoque_item.observacao | ✅ |

---

## APÊNDICE B: DECISÕES ABERTAS

### Decisões que Requerem PO/CTO

| # | Decisão | Opções | Recomendação |
|---|---------|--------|--------------|
| D1 | Criar novo lote durante entrada? | SIM / NÃO | SIM (permitir ambos) |
| D2 | UL é obrigatória para alguns produtos? | SIM / NÃO | NÃO (sempre opcional) |
| D3 | Quarentena bloqueia consumo? | SIM / NÃO | SIM (até liberação) |

**Status**: Aguardando aprovação

---

## APÊNDICE C: REFERÊNCIAS

| Documento | Link | Propósito |
|-----------|------|----------|
| EST-OP-02C-ARCH_DECISAO_FINAL.md | Codebase | Decisão de arquitetura |
| EST-OP-02C-ARCH_RELATORIO_FINAL.md | Codebase | Auditoria completa |
| entradaestoque.component.ts | Frontend | Componente atual |
| MovimentacaoDeEstoque.cs | Backend | Agregado de domínio |
| SaldoEstoque.cs | Backend | Entidade legado |

---

**Documento**: EST-OP-02C-SPEC_ESPECIFICACAO_FINAL.md  
**Data**: 03/09/2026 12:02 UTC  
**Status**: 🟡 ESPECIFICAÇÃO CONSOLIDADA - AGUARDANDO APROVAÇÃO FINAL  
**Validação**: Código analisado, decisões documentadas, bloqueadores identificados  

---

## PRÓXIMAS AÇÕES

### 🔴 HOJE (03/09 - 12:02 UTC)

**Ação**: Submeter especificação para aprovação final

- [ ] CTO revisa e aprova
- [ ] PO valida negócio
- [ ] Tech Lead autoriza implementação

### 🟡 AMANHÃ (04/09)

**Ação**: Kickoff EST-OP-02C.1 (Sincronização SaldoEstoque)

**Presentes**: CTO, PO, Tech Lead, Devs, QA

**Pauta**:
1. Revisão especificação
2. Explicação das 5 fases
3. Atribuição de tarefas Fase 1
4. Setup ambiente

### 🟢 PRÓXIMAS 2 SEMANAS

**Objetivo**: Fase 1 completa (Entrada Direta + Sincronização SaldoEstoque)

---

**FIM DA ESPECIFICAÇÃO EST-OP-02C-SPEC**
