# EST-OP-02C-ARCH — DECISÃO ARQUITETURAL FINAL

**Data**: 02/09/2026  
**Hora**: 23:04 UTC  
**Status**: DECISÃO RECOMENDADA  
**Validade**: Até aprovação do PO + CTO  

---

## RESUMO EXECUTIVO

### Pergunta Crítica
**Qual arquitetura é recomendada para o domínio de estoque: OPTION A, B ou C?**

### Resposta
**✅ OPÇÃO B: SaldoEstoque como Fonte de Verdade + UnidadeLogistica Opcional**

```
┌─────────────────────────────────────────────────────────────┐
│ SALDOESTOQUE (Fonte de Verdade)                             │
├─────────────────────────────────────────────────────────────┤
│ Suporta:                                                     │
│ ✅ Grão a granel (qtdfisica)                               │
│ ✅ Reserva (qtdreservada)                                  │
│ ✅ Bloqueio (qtdbloqueada)                                 │
│ ✅ Disponibilidade (qtddisponivel)                         │
│ ✅ Entrada/Saída/Transferência                             │
│ ✅ Compatibilidade legado 100%                             │
└─────────────────────────────────────────────────────────────┘
         ↓ (opcional para casos novos)
┌─────────────────────────────────────────────────────────────┐
│ UNIDADELOGISTICA (Unidades Físicas Distintas)               │
├─────────────────────────────────────────────────────────────┤
│ Suporta:                                                     │
│ ✅ Paletes                                                 │
│ ✅ Big bags                                                │
│ ✅ Bobinas                                                 │
│ ✅ Movimentação com rastreamento                           │
│ ✅ Versionamento otimista + idempotência                   │
└─────────────────────────────────────────────────────────────┘
```

---

## MATRIZ DE COMPARAÇÃO

| Critério | OPTION A | OPTION B | OPTION C |
|----------|----------|----------|----------|
| **Aderência Atual** | 20% | **85%** | 5% |
| **Grão a Granel** | ✅ | **✅** | ❌ |
| **Paletes/Big Bags** | ✅ | **✅** | ✅ |
| **Reserva** | ⚠️ | **✅** | ❌ |
| **Bloqueio** | ⚠️ | **✅** | ❌ |
| **Inventário** | ❌ | **✅** | ⚠️ |
| **Compatibilidade Legado** | ❌ | **✅** | ❌ |
| **Complexidade Migração** | 🔴 ALTÍSSIMA | **🟢 MÍNIMA** | 🔴 CRÍTICA |
| **Risco** | ALTÍSSIMO | **BAIXO** | ALTÍSSIMO |
| **Timeline** | 90+ dias | **30 dias** | 120+ dias |

---

## JUSTIFICATIVA TÉCNICA

### Por que não OPTION A (Tudo em UL)?

1. **Incompatibilidade com legado**: Sistema atual usa `SaldoEstoque` para 100% das operações
2. **Falta de suporte a grão a granel**: UnidadeLogistica é unidade física, não quantidade contínua
3. **Sem campos de reserva/bloqueio**: UnidadeLogistica não tem `qtdreservada`, `qtdbloqueada`
4. **Complexidade extrema**: Refatorar entrada, handlers, testes, UI, dados históricos
5. **Risco inaceitável**: Quebra compatibilidade com 10+ anos de operação

**Conclusão**: OPTION A é viável apenas como evolução FUTURA (2027+) após OPTION B estabilizada.

---

### Por que não OPTION C (UL como Fonte)?

1. **Aderência 5%**: Sistema atual não cria UnidadeLogistica em entrada
2. **Sem migração de dados**: 100% de dados históricos em SaldoEstoque
3. **Sem projeção explícita**: SaldoEstoque não é projeção em código
4. **Perda de funcionalidade**: Reserva e bloqueio não suportados em UL
5. **Custo proibitivo**: Reengenharia total, nova UI, novos handlers

**Conclusão**: OPTION C é inviável para produção imediata.

---

### Por que SIM para OPTION B?

1. **✅ Alinhamento Arquitetural**: 85% do sistema já segue este padrão
2. **✅ Suporta Todos os Casos**: Grão (SaldoEstoque) + Unidades (UL) = Flexibilidade máxima
3. **✅ Mínima Complexidade**: Apenas sincronizar handlers, sem refatoração estrutural
4. **✅ Risco Controlado**: Sistema legado continua operacional, melhorias incrementais
5. **✅ Compatibilidade 100%**: Zero quebra com operações atuais
6. **✅ Gradação Possível**: Adotar UnidadeLogistica gradualmente para novos casos
7. **✅ Timeline Realista**: 30 dias para bloqueadores críticos, 60 para completo

---

## ESCOPO OPTION B

### Fase A5 (30 dias) — Bloqueadores Críticos

**Objetivo**: Estabilizar SaldoEstoque como fonte de verdade + segurança + concorrência

```
1. Sincronização SaldoEstoque
   └─ Handler atualiza qtdfisica quando MovimentoEstoque criado
   └─ Suporta: Entrada, Saída, Transferência
   └─ Impacto: Crítico — sem isso, saldo não é atualizado

2. Segurança SaldoEstoque
   └─ Remover [AllowAnonymous] de SaldoEstoqueController
   └─ Adicionar autorização via policy (SaldoEstoqueConsultar, SaldoEstoqueEditar)
   └─ Impacto: Alta — risco de segurança em produção

3. Concorrência SaldoEstoque
   └─ Adicionar RowVersion como concurrency token
   └─ Retry logic em handler de sincronização
   └─ Impacto: Alta — múltiplos usuários causam lost updates

4. Chave Única SaldoEstoque
   └─ Índice UNIQUE em (produtoid, almoxarifadoid, localizacaoestoqueid, lotematerialid)
   └─ Impacto: Média — evita duplicação de saldo

5. Smoke Tests
   └─ Entrada → MovimentoEstoque → SaldoEstoque ✓
   └─ Saída → Saldo decrementado ✓
   └─ Transferência → Origem/Destino sincronizados ✓
   └─ Concorrência (múltiplas entradas simultâneas) ✓
   └─ Autorização (sem/com/inválido token) ✓
```

**Resultado**: Sistema pronto para UAT com saldo sincronizado automaticamente

---

### Fase B1 (60 dias) — Operações Completas

**Objetivo**: Implementar reserva, bloqueio, inventário automatizados

```
6. Reserva Automática
   └─ ReservaEstoque cria → atualiza qtdreservada em SaldoEstoque
   └─ Liberar reserva → decrementa qtdreservada

7. Bloqueio Automático
   └─ BloqueioEstoque cria → atualiza qtdbloqueada em SaldoEstoque
   └─ Desbloquear → decrementa qtdbloqueada

8. Inventário
   └─ Ajuste manual de qtdfisica via InventarioEstoque
   └─ Atualiza SaldoEstoque com diferença encontrada

9. Validações de Disponibilidade
   └─ Saída verifica qtddisponivel >= quantidade solicitada
   └─ Rejeita saída se insuficiente
```

**Resultado**: Operações de estoque 100% sincronizadas em tempo real

---

### Fase C1 (90+ dias) — Futuro (Análise)

**Objetivo**: Avaliar evolução para OPTION A conforme negócio cresça

```
10. Análise OPTION A
    └─ Performance com UnidadeLogistica em larga escala
    └─ Necessidade de rastreamento individual de unidades
    └─ ROI de migração de SaldoEstoque → UL para grão a granel
    └─ Decisão: Continuar OPTION B ou evoluir para OPTION A em 2027
```

**Resultado**: Arquitetura futura definida com base em dados reais

---

## IMPACTO IMEDIATO

### SaldoEstoque
```
Antes (Legado):
├─ Sem sincronização automática
├─ Sem autenticação ([AllowAnonymous])
├─ Sem proteção de concorrência (lost updates)
├─ Sem chave única (duplicação possível)
└─ Usuário atualiza manualmente via CRUD

Depois (OPTION B):
├─ ✅ Sincroniza automaticamente quando MovimentoEstoque criado
├─ ✅ Autenticação obrigatória
├─ ✅ Versionamento otimista (retry automático)
├─ ✅ Índice UNIQUE previne duplicação
└─ ✅ Usuário vê saldo em tempo real
```

### UnidadeLogistica
```
Continua:
├─ ✅ Operacional com MovimentacaoDeEstoque
├─ ✅ Versionamento otimista
├─ ✅ Idempotência
├─ ✅ Domain Events
└─ ✅ Rastreabilidade completa

Agora opcionalmente associada a:
├─ SaldoEstoque quando necessário
└─ Casos que exigem unidades físicas distintas (paletes, big bags)
```

---

## ROADMAP VISUAL

```
HOJE (02/09/2026)
├─ Entrada cria MovimentoEstoque (legado)
├─ ⚠️ Sem sincronização com SaldoEstoque
├─ ⚠️ Sem autenticação
├─ ⚠️ Sem proteção de concorrência
└─ ⚠️ Sem chave única

        ↓ Fase A5 (30 dias)

SETEMBRO 30 (UAT PRONTO)
├─ ✅ Entrada → MovimentoEstoque → SaldoEstoque (automático)
├─ ✅ Autenticação obrigatória
├─ ✅ Proteção de concorrência (RowVersion)
├─ ✅ Índice UNIQUE em SaldoEstoque
└─ ✅ Smoke tests aprovados

        ↓ Fase B1 (60 dias)

NOVEMBRO 02 (OPERAÇÕES COMPLETAS)
├─ ✅ Reserva automática
├─ ✅ Bloqueio automático
├─ ✅ Inventário automático
└─ ✅ Validações de disponibilidade

        ↓ Fase C1 (análise)

2027+ (POSSÍVEL EVOLUÇÃO)
├─ Avaliar OPTION A (UnidadeLogistica obrigatória)
├─ Análise de performance
├─ Feedback de usuário
└─ Decisão arquitetural futura
```

---

## RISCOS MITIGADOS

| Risco | OPTION A | OPTION B | Ação |
|-------|----------|----------|------|
| Perda de dados históricos | 🔴 ALTO | 🟢 ZERO | N/A |
| Quebra de legado | 🔴 ALTO | 🟢 ZERO | N/A |
| Concorrência (lost updates) | 🟡 MÉDIO | 🟢 MITIGADO | RowVersion + retry |
| Segurança (acesso anônimo) | 🟡 MÉDIO | 🟢 MITIGADO | [Authorize] policy |
| Duplicação de saldo | 🟡 MÉDIO | 🟢 MITIGADO | Índice UNIQUE |
| Timeline de entrega | 🔴 ALTO | 🟢 30 dias | Planejamento ágil |
| Custo de refatoração | 🔴 ALTO | 🟢 BAIXO | Incremental |

---

## ASSINATURA TÉCNICA

| Papel | Responsável | Status |
|-------|-------------|--------|
| **Arquitetura** | CTO | ⏳ Aguardando aprovação |
| **Auditoria** | Tech Lead Estoque | ✅ Concluído |
| **Implementação** | Dev Team | ⏳ Aguardando aprovação |
| **QA** | QA Lead | ⏳ Pronto para validação |
| **Negócio** | PO | ⏳ Aguardando aprovação |

---

## PRÓXIMAS AÇÕES

### 1️⃣ Aprovação (Hoje — 02/09)
- [ ] CTO aprova OPTION B
- [ ] PO confirma negócio alinhado
- [ ] Tech Lead autoriza implementação

### 2️⃣ Kickoff (Amanhã — 03/09)
- [ ] Team reunião: explicar OPTION B
- [ ] Distribuir tarefas Fase A5
- [ ] Setup ambiente de teste

### 3️⃣ Implementação (Próximos 30 dias)
- [ ] Sincronização SaldoEstoque (Dia 1-2)
- [ ] Segurança (Dia 3)
- [ ] Concorrência (Dia 4)
- [ ] Chave única (Dia 5)
- [ ] Smoke tests (Dia 6-7)
- [ ] UAT (Dia 8+)

### 4️⃣ UAT (Condicional em 5-7 dias)
- [ ] Teste com dados reais
- [ ] Validação de sincronização
- [ ] Performance sob carga
- [ ] Aprovação final

---

## PERGUNTAS FREQUENTES

### P: Por que não OPTION A agora?
**R**: Aderência atual é apenas 20%. Refatoração completa levaria 90+ dias com risco altíssimo. OPTION B oferece 85% de aderência em 30 dias.

### P: Será necessário migrar dados históricos?
**R**: Não. SaldoEstoque permanece intacto. Apenas sincronizamos novos MovimentoEstoque com SaldoEstoque.

### P: E se depois precisarmos de OPTION A?
**R**: Arquitetura foi desenhada como stepping stone. Fase C1 (90+ dias) avaliará viabilidade de migração para OPTION A sem quebra de sistema atual.

### P: Qual é o risco de segurança de manter [AllowAnonymous]?
**R**: Crítico. Qualquer pessoa pode ler/modificar saldos. OPTION B resolve em Dia 3.

### P: Multiple users causando lost updates é um risco real?
**R**: Sim. Sem RowVersion, dois usuários salvando SaldoEstoque simultâneos causam lost update. OPTION B resolve com retry automático.

### P: Quando UnidadeLogistica será obrigatória?
**R**: Provavelmente 2027+, após análise de performance e feedback de usuário em Fase C1.

---

## CONCLUSÃO

**OPTION B é a recomendação arquitetural para EST-OP-02C porque:**

1. ✅ Alinha com realidade atual (85% aderência)
2. ✅ Suporta todos os casos de uso (grão + unidades físicas)
3. ✅ Mínimo risco (sistema legado continua operacional)
4. ✅ Timeline realista (30 dias vs 90+ para OPTION A)
5. ✅ Caminho claro para evolução futura (Fase C1)
6. ✅ Custo controlado (incremental vs refatoração)

**Próximo passo**: Aprovação de CTO + PO para iniciar Fase A5.

---

**Documento**: EST-OP-02C-ARCH_DECISAO_FINAL.md  
**Data**: 02/09/2026 23:04 UTC  
**Status**: 🟡 AGUARDANDO APROVAÇÃO  
**Validade**: Até aprovação do PO + CTO  
