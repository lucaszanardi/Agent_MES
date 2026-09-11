# EST-OP-02C-ARCH — PLANO DE IMPLEMENTAÇÃO SPRINT

**Data Início**: 03/09/2026  
**Data Conclusão**: 02/10/2026  
**Duração**: 30 dias (Fase A5 — Bloqueadores Críticos)  
**Status**: 🔴 NÃO INICIADO (Aguardando aprovação)  

---

## VISÃO GERAL

**Objetivo**: Implementar 4 bloqueadores críticos para estabilizar SaldoEstoque como fonte de verdade com segurança, concorrência e sincronização automática.

**Deliverables**:
- ✅ Sincronização SaldoEstoque (MovimentoEstoque → SaldoEstoque)
- ✅ Autenticação e autorização
- ✅ Proteção de concorrência (RowVersion)
- ✅ Índice UNIQUE em SaldoEstoque
- ✅ Smoke tests aprovados
- ✅ Documentação de mudanças

**Equipe**:
- **Tech Lead**: Orquestração, arquitetura, decisões
- **Backend Dev 1**: Sincronização + Handlers
- **Backend Dev 2**: Segurança + Concorrência
- **QA**: Testes, smoke tests
- **DevOps** (suporte): Migrations, deploy

---

## SEMANA 1 (03-06 SET) — Sincronização + Infraestrutura

### TAREFA 1.1: Domain Event MovimentoEstoqueCriado
**Responsável**: Backend Dev 1  
**Duração**: 4 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar domain event que será disparado quando MovimentoEstoque é criado. Este evento será o gatilho para sincronização com SaldoEstoque.

**Artefatos**:
```
📝 BACKEND/PRPA/App.Domain/Entities/PRPA/Events/MovimentoEstoqueCriado.cs
```

**Checklist**:
- [ ] Criar classe `MovimentoEstoqueCriado : DomainEvent`
- [ ] Adicionar propriedades: MovimentoEstoqueId, TipoMovimentoId, ProdutoId, AlmoxarifadoOrigemId, AlmoxarifadoDestinoId, LocalizacaoOrigemId, LocalizacaoDestinoId, Quantidade, UnidadeMedidaId, UsuarioCriacao
- [ ] Adicionar construtor com todas as propriedades
- [ ] Validações básicas no construtor
- [ ] Testes unitários (3 casos)

**Dependências**: Nenhuma  
**Bloqueadores**: Nenhum  

---

### TAREFA 1.2: SincronizarSaldoEstoqueHandler
**Responsável**: Backend Dev 1  
**Duração**: 8 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar handler que consome evento MovimentoEstoqueCriado e sincroniza SaldoEstoque. Suportar Entrada, Saída, Transferência com retry de concorrência.

**Artefatos**:
```
📝 BACKEND/PRPA/App.Service/Services/Estoque/Handlers/SincronizarSaldoEstoqueHandler.cs
```

**Checklist**:
- [ ] Implementar `INotificationHandler<MovimentoEstoqueCriado>`
- [ ] Método `Handle()` que roteia por tipo de movimento
- [ ] Método `SincronizarEntrada()` — criar/atualizar SaldoEstoque destino
- [ ] Método `SincronizarSaida()` — decrementar SaldoEstoque origem
- [ ] Método `SincronizarTransferencia()` — saída origem + entrada destino
- [ ] Retry logic para `DbUpdateConcurrencyException` (3 tentativas)
- [ ] Logging de cada operação
- [ ] Testes unitários (5 casos: entrada, saída, transferência, novo saldo, saldo existente)

**Dependências**: TAREFA 1.1  
**Bloqueadores**: Nenhum  

---

### TAREFA 1.3: Emitir Evento em MovimentoEstoqueController
**Responsável**: Backend Dev 1  
**Duração**: 2 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Modificar `MovimentoEstoqueController.Post()` para emitir `MovimentoEstoqueCriado` após persistir `MovimentoEstoque`.

**Artefatos**:
```
🔧 BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs (modificar Post)
```

**Checklist**:
- [ ] Injetar `IMediator` no controller
- [ ] Após `SaveAsync()`, construir `MovimentoEstoqueCriado`
- [ ] Chamar `await _mediator.Publish(evento)`
- [ ] Tratar exceções de publicação
- [ ] Testes de integração (endpoint POST)

**Dependências**: TAREFA 1.2  
**Bloqueadores**: Nenhum  

---

### TAREFA 1.4: Registrar Handler no DI
**Responsável**: Tech Lead  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Registrar `SincronizarSaldoEstoqueHandler` no container de injeção de dependência.

**Artefatos**:
```
🔧 BACKEND/PRPA/PRPA/Program.cs (modificar ConfigureServices)
```

**Checklist**:
- [ ] Adicionar `services.AddScoped<INotificationHandler<MovimentoEstoqueCriado>, SincronizarSaldoEstoqueHandler>()`
- [ ] Validar que `IMediator` está registrado
- [ ] Build sem erros
- [ ] Verificar que handler é resolvido pelo DI

**Dependências**: TAREFA 1.2  
**Bloqueadores**: Nenhum  

---

### TAREFA 1.5: Smoke Test — Sincronização Básica
**Responsável**: QA  
**Duração**: 4 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar teste de integração que verifica se entrada cria/atualiza SaldoEstoque.

**Artefatos**:
```
📝 BACKEND/PRPA/Tests/Integration/EstoqueIntegrationTests.cs (novo)
```

**Casos de Teste**:
1. **Entrada novo saldo**: Criar MovimentoEstoque tipo ENTRADA → Verificar SaldoEstoque criado com qtdfisica = quantidade
2. **Entrada saldo existente**: Atualizar saldo → Criar MovimentoEstoque → Verificar qtdfisica incrementada
3. **Saída com sucesso**: Criar MovimentoEstoque tipo SAÍDA → Verificar qtdfisica decrementada
4. **Saída insuficiente**: Criar SAÍDA com quantidade > qtddisponível → Verificar exceção

**Checklist**:
- [ ] Setup de contexto de teste
- [ ] Fixtures de dados (Produto, Almoxarifado, Localização)
- [ ] 4 testes implementados e PASS
- [ ] Coverage > 80%

**Dependências**: TAREFAS 1.1-1.4  
**Bloqueadores**: Nenhum  

**Métricas de Sucesso**:
```
✅ Entrada cria SaldoEstoque com qtdfisica correto
✅ Entrada existente atualiza qtdfisica
✅ Saída decrementa qtdfisica
✅ Saída insuficiente lança exceção
```

---

## SEMANA 2 (09-13 SET) — Segurança + Concorrência

### TAREFA 2.1: Domain Policy para Autorização
**Responsável**: Backend Dev 2  
**Duração**: 2 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar policies de autorização para SaldoEstoque: `SaldoEstoqueConsultar` e `SaldoEstoqueEditar`.

**Artefatos**:
```
📝 BACKEND/PRPA/PRPA/Auth/EstoqueSaldoAuthorization.cs (novo)
```

**Checklist**:
- [ ] Classe estática `EstoqueSaldoAuthorization`
- [ ] Propriedades de policy: `SaldoEstoqueConsultar`, `SaldoEstoqueEditar`
- [ ] Método `AddEstoqueAutorizationPolicies()`
- [ ] Policies verificam claims: `"permission"` com valores `"estoque:saldo:consultar"`, `"estoque:saldo:editar"`, `"estoque:admin"`
- [ ] Documentação de policies

**Dependências**: Nenhuma  
**Bloqueadores**: Nenhum  

---

### TAREFA 2.2: Adicionar [Authorize] em SaldoEstoqueController
**Responsável**: Backend Dev 2  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Remover `[AllowAnonymous]` e adicionar `[Authorize]` + policies em todos endpoints de SaldoEstoqueController.

**Artefatos**:
```
🔧 BACKEND/PRPA/PRPA/Controllers/SaldoEstoqueController.cs (modificar)
```

**Checklist**:
- [ ] Remover `[AllowAnonymous]` de todos endpoints
- [ ] Adicionar `[Authorize(Policy = EstoqueSaldoAuthorization.Policies.SaldoEstoqueConsultar)]` em GET
- [ ] Adicionar `[Authorize(Policy = EstoqueSaldoAuthorization.Policies.SaldoEstoqueEditar)]` em POST/PUT/DELETE
- [ ] Adicionar `[Authorize]` ao nível da classe
- [ ] Build sem erros

**Dependências**: TAREFA 2.1  
**Bloqueadores**: Nenhum  

---

### TAREFA 2.3: Registrar Policies no Program.cs
**Responsável**: Backend Dev 2  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Registrar policies de autorização no container DI.

**Artefatos**:
```
🔧 BACKEND/PRPA/PRPA/Program.cs (modificar ConfigureServices)
```

**Checklist**:
- [ ] Chamar `EstoqueSaldoAuthorization.AddEstoqueAutorizationPolicies(services)`
- [ ] Verificar que `AddAuthentication()` e `AddAuthorization()` estão registrados
- [ ] Build sem erros

**Dependências**: TAREFA 2.1  
**Bloqueadores**: Nenhum  

---

### TAREFA 2.4: Adicionar RowVersion em SaldoEstoque
**Responsável**: Backend Dev 2  
**Duração**: 2 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Adicionar propriedade `RowVersion` como concurrency token em `SaldoEstoque`.

**Artefatos**:
```
🔧 BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs (modificar)
📝 BACKEND/PRPA/App.Infra.Data/Mapping/SaldoEstoqueConfig.cs (modificar)
```

**Checklist**:
- [ ] Adicionar `public byte[] RowVersion { get; set; }` em SaldoEstoque
- [ ] Marcar com `[Timestamp]`
- [ ] Configurar em SaldoEstoqueConfig: `builder.Property(c => c.RowVersion).IsRowVersion()`
- [ ] Build sem erros

**Dependências**: Nenhuma  
**Bloqueadores**: Nenhum  

---

### TAREFA 2.5: Criar Migration para RowVersion
**Responsável**: DevOps  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Gerar migration que adicione coluna `RowVersion` em `CSALDOESTOQUE`.

**Artefatos**:
```
📝 BACKEND/PRPA/App.Infra.Data/Migrations/202609031000_AddRowVersionToSaldoEstoque.cs
```

**Checklist**:
- [ ] `dotnet ef migrations add AddRowVersionToSaldoEstoque`
- [ ] Verificar SQL gerado (adiciona coluna `RowVersion ROWVERSION` ou `varbinary(8)`)
- [ ] Build sem erros
- [ ] **Não aplicar** (requer aprovação humana)

**Dependências**: TAREFA 2.4  
**Bloqueadores**: Nenhum  

---

### TAREFA 2.6: Atualizar SincronizarSaldoEstoqueHandler com Retry
**Responsável**: Backend Dev 1  
**Duração**: 2 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Adicionar retry logic em `SincronizarSaldoEstoqueHandler` para tratar `DbUpdateConcurrencyException`.

**Artefatos**:
```
🔧 BACKEND/PRPA/App.Service/Services/Estoque/Handlers/SincronizarSaldoEstoqueHandler.cs (modificar)
```

**Checklist**:
- [ ] Envolver `SaveAsync()` em try-catch com retry (3 tentativas)
- [ ] Em catch `DbUpdateConcurrencyException`: reload entidade, aguardar 50ms, retry
- [ ] Após 3 tentativas falhadas: lançar exceção
- [ ] Logging de cada retry
- [ ] Testes unitários (retry com sucesso, retry com falha)

**Dependências**: TAREFA 1.2, 2.4  
**Bloqueadores**: Nenhum  

---

### TAREFA 2.7: Smoke Test — Concorrência + Autorização
**Responsável**: QA  
**Duração**: 4 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar testes que verificam proteção de concorrência e autenticação.

**Artefatos**:
```
📝 BACKEND/PRPA/Tests/Integration/SaldoEstoqueConcorrencyTests.cs (novo)
📝 BACKEND/PRPA/Tests/Integration/SaldoEstoqueAuthorityTests.cs (novo)
```

**Casos de Teste — Concorrência**:
1. **Dois usuários incrementam simultaneamente**: Ambas operações aplicadas (sem lost update)
2. **Retry automático em conflito**: Primeira tentativa falha, segunda sucede
3. **Falha após 3 retries**: Exceção lançada

**Casos de Teste — Autorização**:
1. **GET sem token**: 401 Unauthorized
2. **GET com token inválido**: 401 Unauthorized
3. **GET com token válido mas sem permission**: 403 Forbidden
4. **GET com token válido + permission**: 200 OK
5. **POST sem token**: 401 Unauthorized
6. **POST com token valido + permission**: 201 Created

**Checklist**:
- [ ] 3 testes de concorrência PASS
- [ ] 6 testes de autorização PASS
- [ ] Coverage > 80%

**Dependências**: TAREFAS 2.1-2.6  
**Bloqueadores**: Nenhum  

**Métricas de Sucesso**:
```
✅ Múltiplas atualizações simultâneas não causam lost update
✅ Retry automático resolve conflitos de concorrência
✅ Sem token retorna 401
✅ Token inválido retorna 401
✅ Sem permission retorna 403
✅ Com permission retorna 200/201
```

---

## SEMANA 3 (16-20 SET) — Chave Única + Smoke Tests

### TAREFA 3.1: Adicionar Índice Único em SaldoEstoque
**Responsável**: Backend Dev 2  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Configurar índice UNIQUE em `(produtoid, almoxarifadoid, localizacaoestoqueid, lotematerialid)` em SaldoEstoque.

**Artefatos**:
```
🔧 BACKEND/PRPA/App.Infra.Data/Mapping/SaldoEstoqueConfig.cs (modificar)
```

**Checklist**:
- [ ] Adicionar `.HasIndex(c => new { c.produtoid, c.almoxarifadoid, c.localizacaoestoqueid, c.lotematerialid }).IsUnique()`
- [ ] Nomear índice: `UX_CSALDOESTOQUE_Produto_Almoxarifado_Localizacao_Lote`
- [ ] Build sem erros

**Dependências**: Nenhuma  
**Bloqueadores**: Nenhum  

---

### TAREFA 3.2: Criar Migration para Índice Único
**Responsável**: DevOps  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Gerar migration que adicione índice UNIQUE em CSALDOESTOQUE.

**Artefatos**:
```
📝 BACKEND/PRPA/App.Infra.Data/Migrations/202609031100_AddUniqueSaldoEstoqueKey.cs
```

**Checklist**:
- [ ] `dotnet ef migrations add AddUniqueSaldoEstoqueKey`
- [ ] Verificar SQL gerado (cria índice UNIQUE)
- [ ] Build sem erros
- [ ] **Não aplicar** (requer aprovação humana)

**Dependências**: TAREFA 3.1  
**Bloqueadores**: Nenhum  

---

### TAREFA 3.3: Integração End-to-End — Entrada Completa
**Responsável**: QA  
**Duração**: 6 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Teste de integração que simula fluxo completo de entrada: frontend → backend → MovimentoEstoque → SaldoEstoque, com autenticação e concorrência.

**Artefatos**:
```
📝 BACKEND/PRPA/Tests/Integration/EntradaEstoqueE2ETests.cs (novo)
```

**Casos de Teste**:
1. **Entrada nova**: POST /api/movimentoestoque (ENTRADA) → MovimentoEstoque criado → SaldoEstoque criado com qtdfisica correto → Verificar autenticação obrigatória
2. **Entrada existente**: Repetir caso anterior com mesmo produto/localização → Verificar qtdfisica incrementada
3. **Múltiplas entradas simultâneas**: 5 threads fazem POST simultaneamente → Verificar sem lost updates → qtdfisica = soma de todas
4. **Saída com sucesso**: MovimentoEstoque (SAÍDA) → Verificar qtdfisica decrementada
5. **Saída insuficiente**: MovimentoEstoque (SAÍDA) com quantidade > qtddisponível → Verificar exceção e saldo inalterado
6. **Transferência**: MovimentoEstoque (TRANSFERÊNCIA) → Origem saldo decrementado → Destino saldo incrementado

**Checklist**:
- [ ] 6 testes E2E implementados e PASS
- [ ] Cada teste verifica autenticação
- [ ] Cada teste verifica integridade de dados
- [ ] Logs incluem CorrelationId
- [ ] Coverage > 85%

**Dependências**: TAREFAS 1.1-1.5, 2.1-2.7, 3.1-3.2  
**Bloqueadores**: Nenhum  

**Métricas de Sucesso**:
```
✅ Entrada cria saldo correto
✅ Entrada existente atualiza qtdfisica
✅ Múltiplas entradas sem lost updates
✅ Saída decrementa saldo
✅ Saída insuficiente rejeitada
✅ Transferência sincroniza ambas
✅ Todas operações exigem autenticação
```

---

### TAREFA 3.4: Performance Baseline
**Responsável**: QA + Tech Lead  
**Duração**: 3 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Estabelecer baseline de performance com 1000 SaldoEstoque e medir tempo de sincronização.

**Artefatos**:
```
📝 BACKEND/PRPA/Tests/Performance/SaldoEstoquePerformanceTests.cs (novo)
```

**Cenários**:
1. **Inserção de saldo novo**: Medir tempo médio de MovimentoEstoque → SaldoEstoque criado
2. **Atualização de saldo**: Medir tempo médio de atualização com retry de concorrência
3. **Query de saldo**: Medir tempo de `GetByCriteria(produtoid, almoxarifadoid, localizacaoestoqueid, lotematerialid)`

**Checklist**:
- [ ] Setup com 1000 SaldoEstoque
- [ ] 3 cenários testados
- [ ] Tempos medidos e documentados
- [ ] P95 < 500ms para sincronização
- [ ] P95 < 200ms para query de saldo

**Dependências**: TAREFAS 1.1-1.5, 2.1-2.7, 3.1-3.2  
**Bloqueadores**: Nenhum  

**Métricas de Sucesso**:
```
✅ Inserção média < 500ms (P95)
✅ Atualização média < 500ms (P95)
✅ Query média < 200ms (P95)
✅ Sem timeout ou exceção
```

---

## SEMANA 4 (23-27 SET) — Validação + Documentação

### TAREFA 4.1: Aplicar Migrations em Ambiente de Teste
**Responsável**: DevOps  
**Duração**: 2 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Aplicar migrations de RowVersion e Índice Único em banco de teste.

**Artefatos**:
```
✅ CSALDOESTOQUE com coluna RowVersion
✅ Índice UNIQUE criado
```

**Checklist**:
- [ ] `dotnet ef database update` em ambiente de teste
- [ ] Verificar coluna `RowVersion` em CSALDOESTOQUE
- [ ] Verificar índice `UX_CSALDOESTOQUE_Produto_Almoxarifado_Localizacao_Lote` criado
- [ ] Backup anterior salvo

**Dependências**: TAREFAS 2.5, 3.2  
**Bloqueadores**: Nenhum  

---

### TAREFA 4.2: Validação com Dados Reais (Smoke Test Final)
**Responsável**: QA + Tech Lead  
**Duração**: 4 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Executar smoke test com dados reais do sistema legado (produtos, almoxarifados, localizações).

**Artefatos**:
```
📝 Relatório de Smoke Test Final
```

**Cenários**:
1. **Restaurar backup de produção** em ambiente de teste
2. **Criar entrada** via frontend com dados reais
3. **Validar sincronização**: MovimentoEstoque criado → SaldoEstoque atualizado
4. **Validar autenticação**: Verificar que endpoints exigem token
5. **Validar concorrência**: 10 entradas simultâneas, sem lost updates
6. **Validar performance**: Tempo de sincronização < 500ms

**Checklist**:
- [ ] 6 cenários executados sem erro
- [ ] Logs analisados (sem DbUpdateConcurrencyException não tratada)
- [ ] Performance aceitável
- [ ] Relatório assinado por QA

**Dependências**: TAREFAS 1.1-1.5, 2.1-2.7, 3.1-3.4, 4.1  
**Bloqueadores**: Nenhum  

**Métricas de Sucesso**:
```
✅ 6 cenários PASS
✅ 0 erros não tratados
✅ Sincronização < 500ms
✅ Relatório assinado
```

---

### TAREFA 4.3: Documentação Técnica
**Responsável**: Tech Lead  
**Duração**: 3 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar documentação técnica de mudanças para equipe de desenvolvimento.

**Artefatos**:
```
📝 EST-OP-02C-ARCH_MUDANCAS_TECNICAS.md
```

**Seções**:
1. **Visão Geral**: O que mudou e por quê
2. **Arquitetura**: Diagrama de fluxo (Entrada → MovimentoEstoque → Event → SaldoEstoque)
3. **Implementação**: Classes, métodos, eventos
4. **Handlers**: SincronizarSaldoEstoqueHandler e comportamento
5. **Autorização**: Policies e claims obrigatórios
6. **Concorrência**: RowVersion e retry automático
7. **Migrations**: RowVersion e Índice Único
8. **Testes**: Como rodar smoke tests
9. **Troubleshooting**: Comum issues e soluções

**Checklist**:
- [ ] Documento completo e revisado
- [ ] Exemplos de código inclusos
- [ ] Diagramas claros
- [ ] Links para código-fonte

**Dependências**: Nenhuma (pode ser feito em paralelo)  
**Bloqueadores**: Nenhum  

---

### TAREFA 4.4: Documentação de Usuário
**Responsável**: Tech Lead + PO  
**Duração**: 2 horas  
**Status**: 🔴 Pendente  

**Descrição**:
Criar guia de usuário explicando mudanças no comportamento de entrada e saldo.

**Artefatos**:
```
📝 EST-OP-02C-ARCH_GUIA_USUARIO.md
```

**Seções**:
1. **O que mudou**: Entrada agora sincroniza saldo automaticamente
2. **Como usar**: Passo-a-passo de entrada (nenhuma mudança visível)
3. **Erros comuns**: "Saldo não aparece" → esperou 1 minuto? → contate TI
4. **Performance**: Sincronização é rápida (< 1 segundo)

**Checklist**:
- [ ] Documento simples e compreensível
- [ ] Screenshots de UI (se necessário)
- [ ] Exemplos de casos de uso

**Dependências**: Nenhuma  
**Bloqueadores**: Nenhum  

---

### TAREFA 4.5: Release Notes
**Responsável**: Tech Lead  
**Duração**: 1 hora  
**Status**: 🔴 Pendente  

**Descrição**:
Criar release notes para comunicar mudanças à stakeholders.

**Artefatos**:
```
📝 EST-OP-02C-ARCH_RELEASE_NOTES_v1.0.md
```

**Conteúdo**:
```
## v1.0 — EST-OP-02C-ARCH Phase A5

### Melhorias
- ✅ Sincronização automática: Entrada agora atualiza saldo em tempo real
- ✅ Segurança: SaldoEstoque exige autenticação
- ✅ Concorrência: Proteção contra lost updates com RowVersion
- ✅ Integridade: Índice único previne duplicação de saldo

### Mudanças
- SaldoEstoqueController agora exige [Authorize]
- Todas endpoints de estoque exigem token JWT

### Correções de Bug
- Saldo não era atualizado após entrada (AGORA RESOLVIDO)
- Múltiplos usuários causavam lost updates (AGORA PROTEGIDO)

### Performance
- Sincronização < 500ms (P95)
- Query de saldo < 200ms (P95)

### Roadmap Futuro
- Fase B1 (60 dias): Reserva e bloqueio automáticos
- Fase C1 (90+ dias): Análise de UnidadeLogistica obrigatória
```

**Checklist**:
- [ ] Documento conciso e profissional
- [ ] Impactos claros para usuários e devs

**Dependências**: Nenhuma  
**Bloqueadores**: Nenhum  

---

## ROADMAP VISUAL

```
SEMANA 1 (03-06 SET): Sincronização
├─ TAREFA 1.1 ✓ Event MovimentoEstoqueCriado
├─ TAREFA 1.2 ✓ Handler SincronizarSaldoEstoque
├─ TAREFA 1.3 ✓ Emit evento em controller
├─ TAREFA 1.4 ✓ Registrar DI
└─ TAREFA 1.5 ✓ Smoke test sincronização

SEMANA 2 (09-13 SET): Segurança + Concorrência
├─ TAREFA 2.1 ✓ Domain policies
├─ TAREFA 2.2 ✓ [Authorize] em controller
├─ TAREFA 2.3 ✓ Registrar policies
├─ TAREFA 2.4 ✓ RowVersion em SaldoEstoque
├─ TAREFA 2.5 ✓ Migration RowVersion
├─ TAREFA 2.6 ✓ Retry logic em handler
└─ TAREFA 2.7 ✓ Smoke test concorrência + autorização

SEMANA 3 (16-20 SET): Chave Única + Smoke Tests
├─ TAREFA 3.1 ✓ Índice UNIQUE
├─ TAREFA 3.2 ✓ Migration índice
├─ TAREFA 3.3 ✓ E2E entrada completa
└─ TAREFA 3.4 ✓ Performance baseline

SEMANA 4 (23-27 SET): Validação + Documentação
├─ TAREFA 4.1 ✓ Aplicar migrations
├─ TAREFA 4.2 ✓ Smoke test com dados reais
├─ TAREFA 4.3 ✓ Documentação técnica
├─ TAREFA 4.4 ✓ Documentação de usuário
└─ TAREFA 4.5 ✓ Release notes

30/09: ✅ PRONTO PARA UAT
```

---

## MÉTRICAS DE SUCESSO

| Métrica | Target | Verificação |
|---------|--------|-------------|
| **Sincronização** | Entrada → SaldoEstoque em < 1s | Smoke test 1.5 |
| **Concorrência** | Zero lost updates com 10+ threads | Smoke test 2.7 |
| **Autorização** | 401 sem token, 403 sem permission | Smoke test 2.7 |
| **Performance** | P95 < 500ms sincronização | Perf test 3.4 |
| **Cobertura** | > 85% em novos handlers | Coverage report |
| **Documentação** | Técnica + Usuário + Release notes | Tarefas 4.3-4.5 |
| **Smoke Tests** | 100% PASS | Tarefa 4.2 |

---

## RISCOS IDENTIFICADOS

| Risco | Probabilidade | Impacto | Mitigação |
|-------|--------------|--------|-----------|
| **Migration aplica mas quebra dados** | 🟡 Média | 🔴 Alto | Backup antes, DBA valida SQL |
| **Retry infinito em DbUpdateConcurrencyException** | 🟢 Baixa | 🟡 Médio | Max 3 tentativas, timeout |
| **Performance degradada com RowVersion** | 🟢 Baixa | 🟡 Médio | Baseline em tarefa 3.4 |
| **Índice UNIQUE causa constraint violation** | 🟡 Média | 🟡 Médio | Validação de dados em tarefa 4.2 |
| **Token inválido causa 401 em produção** | 🟢 Baixa | 🟡 Médio | Release notes explicam change |

---

## CRITÉRIO DE SUCESSO FASE A5

**Bloqueador #1 - Sincronização**: ✅ Entrada cria/atualiza SaldoEstoque  
**Bloqueador #2 - Segurança**: ✅ [AllowAnonymous] removido, [Authorize] aplicado  
**Bloqueador #3 - Concorrência**: ✅ RowVersion + retry automático funciona  
**Bloqueador #4 - Chave Única**: ✅ Índice UNIQUE criado, previne duplicação  

**Smoke Tests**: ✅ 6/6 E2E tests PASS  
**Performance**: ✅ P95 < 500ms para sincronização  
**Documentação**: ✅ Técnica + Usuário + Release notes  

**GATE FINAL**: 🟢 PRONTO PARA UAT (30/09/2026)

---

**Plano de Implementação**: EST-OP-02C-ARCH Sprint A5  
**Data Criação**: 02/09/2026 23:05 UTC  
**Status**: 🔴 AGUARDANDO KICKOFF  
**Próximo**: Kickoff meeting 03/09 com equipe
