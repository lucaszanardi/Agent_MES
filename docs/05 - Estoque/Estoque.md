# Estoque

Fonte oficial de dominio: BACKEND. Fonte oficial de interface: FRONTEND. Este documento registra somente itens encontrados no codigo.

## Auditoria de Estoque / Operações — 2026-10-02

**Continuação: Entrada de Estoque — Escopo de Warehouse e Validação de Contexto**

### Problema identificado e corrigido

**Hardcode descoberto:** `EntradaDiretaSincronizacaoServices.cs:97` — `int warehouseId = 1` (agora corrigido).

A criação de Unidades Logísticas utilizava warehouseId fixado em 1, independentemente do almoxarifado real da localização selecionada. Consequência:

- 16 ULs criadas no banco
- Apenas 1 com WarehouseId correto
- 15 com WarehouseId divergente → não aparecem no Mapa do Estoque
- Afeta filtros e relatórios que usam WarehouseId como chave

### Solução implementada

**Arquivo:** `App.Service/Services/EntradaDiretaSincronizacaoServices.cs:97`

```csharp
// ANTES:
int warehouseId = 1;

// DEPOIS:
int warehouseId = localDestino.almoxarifadoid;
```

WarehouseId agora é derivado da localização real, garantindo coerência:
- Localização → Área → Almoxarifado → WarehouseId

### Validações adicionadas

**Arquivo:** `EntradaDiretaSincronizacaoServices.cs:316-325`

Garante que todas as relações estejam no mesmo contexto antes da gravação:

1. Localização pertence ao Almoxarifado selecionado (linha 316)
2. Área da Localização pertence ao Almoxarifado selecionado (linha 323)

Se houver divergência, a entrada é rejeitada antes de qualquer alteração.

### Testes implementados

**Arquivo:** `App.Domain.Tests/EntradaEstoqueEscopoTests.cs`

Cobertura completa com 20 testes xUnit (todos aprovados):

#### Testes de escopo (4):
- Entrada preserva contexto produto/quantidade/unidade/movimento
- Localização de outro almoxarifado → rejeitada
- Área de outro almoxarifado → rejeitada
- Contexto inexistente (local/área/almoxarifado/produto) → rejeitado

#### Testes de quantidade (1):
- Quantidade não positiva → rejeitada

#### Testes de lote (4):
- Produto sem controle de lote → criado sem lote
- Produto com controle de lote + lote válido → vínculo preservado
- Produto com controle de lote + sem lote → rejeitado
- Lote inválido (inexistente/outro produto/inativo) → rejeitado

#### Testes de UL (3):
- Modo Estoque Direto (sem UL) → SaldoEstoque criado
- Modo UL → SaldoEstoque criado + UL criada com WarehouseId correto
- WarehouseId deve igualar almoxarifado da localização

#### Testes de regressão (8):
- Entradas com warehouseId diferente de 1
- Validação de coerência Almoxarifado/Área/Localização
- Rejeição prévia a qualquer gravação

### Status dos dados existentes

**Auditoria (não executada automaticamente):**

- Total de ULs: 16
- ULs coerentes (WarehouseId = Almoxarifado da Localização): 1
- ULs incoerentes: 15

**Por Almoxarifado:**
- Almoxarifado 11: ULs com WarehouseId divergente
- Almoxarifado 12: ULs com WarehouseId divergente
- Almoxarifado 14: 1 UL coerente (apenas esta aparece no Mapa)

**Nota:** Saneamento dos dados existentes não foi executado. Novas entradas seguem a regra corrigida. Saneamento requer validação de negócio e aprovação separada.

### Impacto no Mapa do Estoque

Novas ULs criadas via Entrada agora aparecem corretamente no Mapa, filtradas pelo almoxarifado real. As 15 ULs históricas com WarehouseId incorreto continuam no banco (visível em consultas diretas, não no Mapa).

### Verificação de compilação e testes

```
BUILD BACKEND: OK (0 erros, 6 avisos)
TESTES: 20/20 aprovados (EntradaEstoqueEscopoTests.cs)
```

---

**Nenhuma tela declarada homologada. Estoque NÃO PRONTO para Gestão da Produção e Apontamento.** Esta seção complementa os registros históricos, preservados abaixo. Nenhuma alteração funcional em BACKEND/FRONTEND, migration ou operação de estoque foi realizada nesta rodada além da correção do hardcode em Entrada de Estoque. Alterações anteriores no workspace foram preservadas.

### Complemento autenticado — 2026-10-02

O login por e-mail informado pelo usuário foi executado com **HTTP200**. A limitação de autenticação da primeira passagem foi resolvida; os401 históricos abaixo não representam os resultados atuais. Credenciais e tokens não foram persistidos. Evidência atual: `.codex/estoque-auditoria-20261002/network-authenticated.json`, com16 GETs autenticados e corpos originais em rawBody.

| Tela/fluxo | Resultado autenticado | Situação atual |
|---|---|---|
| UL: listagem e busca | HTTP200;16 ULs; ul16, acacc1, código completo1, termo16=2, inexistente0 | Consulta API confirmada, sem500 nas chamadas testadas |
| UL16: detalhes | HTTP200 | Detalhes consultáveis |
| UL16: histórico | HTTP200; items[], totalCount0 | Histórico vazio legítimo, sem erro HTTP |
| UL inexistente | HTTP404 | Caso negativo esperado |
| Movimentações: local25 | HTTP500 | Falha EF agora também confirmada por HTTP |
| Movimentações: busca de locais | HTTP500 | Falha EF agora também confirmada por HTTP |
| Movimentações: ULs do local25 | HTTP500 | Falha EF agora também confirmada por HTTP |
| Movimentação moderna1 | HTTP404 | Não existe registro moderno; banco tem0 movimentações modernas |
| Mapa almoxarifado11 | HTTP200;0 ULs | Persistem ULs excluídas por WarehouseId incorreto |
| Mapa almoxarifado12 | HTTP200;0 ULs | Persistem ULs excluídas por WarehouseId incorreto |
| Mapa almoxarifado14 | HTTP200;1 UL | Somente1/16 ULs aparece nos três mapas |

**Matriz atual:** Entrada e UL continuam PARCIAIS; demais telas continuam com FALHA pelos defeitos descritos. Na coluna HTTP da matriz histórica, substituir a evidência401 por **200 nas consultas UL**, **500 nas três consultas de locais usadas por Movimentações** e **200 nos mapas**. Não foi observado403 nos GETs testados.

**Limitações remanescentes:** Browser indisponível, sem interação/console verificados; não indicados dados/fixtures destinados a gravações de estoque; nenhum POST/PUT/DELETE de estoque realizado. Nenhuma alteração funcional aplicada neste complemento; builds anteriores permanecem válidos para o mesmo código. Prontidão continua **NÃO PRONTO**. A conta válida resolveu acesso aos GETs, mas não corrigiu os defeitos operacionais.

As seções seguintes mantêm a evidência da primeira passagem, anterior ao login por e-mail, como histórico. O presente complemento prevalece para autenticação e resultados HTTP atuais.

### Alcance da primeira passagem, antes do login por e-mail

- Contexto mantido: frontend `FRONTEND`:4200; backend `BACKEND/PRPA`:5046. Não foi reinvestigada hipótese de workspace errado.
- As dez URLs do frontend responderam HTTP200 com o shell HTML. Isso não comprova carregamento Angular, sessão autenticada ou interação.
- Browser falhou ao iniciar: `trusted Node process exited unexpectedly; kernel reset, rerun your request`. Navegação, cliques, renderização, console, Reactive Forms, PrimeNG e subscriptions em execução: **não identificados** para as dez telas.
- POST `http://localhost:5046/api/Auth/login` retornou401; log da API: identificação fornecida não encontrada. Senha/token não foram gravados nos artefatos. Foi solicitada identificação cadastrada no ambiente local.
- GETs públicos executados; GETs protegidos responderam401 sem sessão válida. Esses401 são esperados, não demonstram defeito funcional. Não houve HTTP500 observado, mas ausência de500 autenticado não foi demonstrada.
- Serviços reais de consulta UL/histórico/locais/mapa foram executados com o contexto MySQL configurado, somente leitura. Isso confirma tradução/persistência, sem substituir HTTP autenticado ou navegador.
- Não foram enviados POST/PUT/DELETE de estoque: não foram indicados registros/ambiente de homologação para os testes persistentes. Entrada, movimentação, transferência, reserva, bloqueio, inventário e ajuste não tiveram efeitos antes/depois testados.
- Evidências: `.codex/estoque-auditoria-20261002/network-readonly.json`, `network-samples.json`, `route-shell-results.json`, `selects.sql`, `db-select-results.json`, `service-read-results.json`, `db-read.log`, `backend-build.log`, `frontend-build.log`. Corpos HTTP originais estão em `rawBody` de `network-*.json`; os arquivos exploratórios `api-*.json` não são fonte de contrato de resposta, pois PowerShell adaptou arrays ao serializar. A sondagem `/api/Fornecedores` não representa a tela: o endpoint real é `/api/Fornecedor`, também404.

### Matriz de diagnóstico da primeira passagem

NV = **não verificado em navegador**, apesar de shell HTTP200. FALHA identifica defeito confirmado em serviço/contrato necessário ao fluxo; PARCIAL registra comprovação limitada. Nenhuma tela OK/OK COM PENDÊNCIA. Não classificar telas inteiras como NÃO IMPLEMENTADO, pois existem componentes, embora partes essenciais estejam ausentes.

| Tela | Rota real | Carrega | Função principal | HTTP relevante observado | Status |
|---|---|---|---|---|---|
| Entrada | `/home/operacao/entradaestoque` | NV | Direto/UL implementados; gravação não testada; escopo de UL incorreto | Cargas200; POST não executado | PARCIAL |
| Movimentações | `/home/operacao/movimentacaoestoque-nova` | NV | Por UL; consultas de locais falham na tradução EF | Protegidos401 | FALHA |
| Unidades Logísticas | `/home/operacao/unidades-logisticas` | NV | Serviços de consulta funcionam; botão usa rota incorreta | Protegidos401 | PARCIAL |
| Mapa do Estoque | `/home/operacao/mapa-estoque` | NV | Saldo e UL previstos;15/16 ULs fora do resultado | Protegidos401 | FALHA |
| Transferência | `/home/operacao/transferenciaestoque` | NV | Documento/itens; efetivação no saldo não identificada | ParametroValor404 | FALHA |
| Reserva | `/home/operacao/reservaestoque` | NV | Cadastro/consulta; saldo/liberação incompletos | ParametroValor404 | FALHA |
| Bloqueio | `/home/operacao/bloqueioestoque` | NV | Cadastro; PUT incompatível; efeito operacional não identificado | ParametroValor404 | FALHA |
| Inventário | `/home/operacao/inventarioestoque` | NV | Cabeçalho existe; API de itens ausente | InventarioEstoqueItem e ParametroValor404 | FALHA |
| Ajuste | `/home/operacao/ajusteestoque` | NV | Documento/itens; PUT incompatível; movimento não identificado | ParametroValor404 | FALHA |
| Lotes | `/home/cadastro/listlotematerial` | NV | Banco vazio; carga depende de Fornecedor inexistente | LoteMaterial200[]; Fornecedor404 | FALHA |

### Convenção dos caminhos e campos comuns por tela

Nos registros abaixo, `F` = `FRONTEND/src/app/application/operacao`; `B` = `BACKEND/PRPA`. Os caminhos indicados expandem esses prefixos. Carregamento e console de **cada tela**: não identificados em navegador; somente shell200 confirmado. Endpoints sem prefixo são relativos a `http://localhost:5046/api/`.

Rotas confirmadas em `FRONTEND/src/app/app-routing.module.ts`, `FRONTEND/src/app/application/operacao/operacao-routing.module.ts`, `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` e SELECT em `CMENU`. Cargas comuns das operações legadas: GET Produto, ProdutoVersao, LoteMaterial, Almoxarifado, localizacao-estoque, UnidadeMedida e ParametroValor. Os services ficam nas respectivas features de cadastro; cada componente mostra os imports reais. As seis primeiras cargas retornaram200; ParametroValor404.

### TELA: Entrada

- **ROTA:** `/home/operacao/entradaestoque`.
- **COMPONENTE:** `F/entradaestoque/components/entradaestoque/entradaestoque.component.ts` e `.html`.
- **SERVICE FRONTEND:** `F/services/movimentoestoque.service.ts` e services de cadastros referenciados no componente.
- **ENDPOINTS:** GET Produto, ProdutoVersao, LoteMaterial, Almoxarifado, localizacao-estoque/elegives-entrada, UnidadeMedida, UnidadeMedida/unidades-recebiveis-produto/{id}, MotivoMovimento/ativos, TipoDocumento/ativos, TipoMovimento/ativos, MovimentoEstoque, MovimentoEstoque/{id}; POST MovimentoEstoque/entrada-direta.
- **ERROS HTTP:** cargas amostradas200; POST não executado. Console/carregamento seguem limitação comum acima.
- **FUNCIONALIDADE / FUNCIONA:** PARCIALMENTE comprovada por código e dados existentes. Ambos os modos usam o POST com `criarUnidadeLogistica=false/true`. Serviço valida produto/lote/destino/conversão/quarentena, registra MovimentoEstoque e incrementa SaldoEstoque na unidade de estoque, usando UnitOfWork no controller.
- **LOTE:** selecionado em `lotematerialid`, de cadastro prévio. Frontend não o exige sempre; backend exige quando Produto.ControlaLote=true. Se informado, valida existência, vínculo com produto e ativo. Entrada não cria lote automaticamente, inclusive no modo UL.
- **UL:** cria código UL e entidade; vincula no movimento. **Defeito:** warehouseId fixado1, sem substituir pelo almoxarifado do destino; plantId admite fallback1. Banco confirma15 ULs com escopo divergente. Entrada com UL também incrementa saldo: saldo não prova estoque exclusivamente direto.
- **ESTADO VAZIO/ERRO:** cargas/histórico com catchError->[] podem mascarar erros; submissão usa Swal. Catch de Entrada no controller pode expor full/innerFull/stack trace, divergindo da afirmação histórica abaixo sobre não exposição de detalhes técnicos.
- **DADOS REAIS:**25 produtos,2 versões,3 almoxarifados,10 locais elegíveis,12 unidades,31 movimentos,14 saldos,0 lotes.
- **PENDÊNCIAS:** gravar/testar dois modos, conversões, saldo e rastreabilidade com fixtures; corrigir escopo após aprovação; links pós-entrada usam `/operacao/unidades-logisticas` sem `/home`.
- **RISCO:** ALTO. **AÇÃO:** priorizar consistência UL/local/almoxarifado antes de novas entradas de teste.
- **EVIDÊNCIA BACKEND:** `B/PRPA/Controllers/MovimentoEstoqueController.cs`, `B/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`, `B/App.Domain/Entities/PRPA/Produto.cs`, `MovimentoEstoque.cs`, `SaldoEstoque.cs`.

### TELA: Movimentações

- **ROTA:** `/home/operacao/movimentacaoestoque-nova`.
- **COMPONENTE:** `F/movimentacaoestoque-nova/components/movimentacaoestoque-nova/movimentacaoestoque-nova.component.ts`/`.html`; árvore em `components/estoque-localizacao-tree/estoque-localizacao-tree.component.ts` da mesma feature.
- **SERVICES:** `F/movimentacaoestoque-nova/services/movimentacaoestoque-nova.service.ts`, `F/estoque-consultas/services/consulta-estoque.service.ts`, cadastros de Almoxarifado/AreaEstoque/LocalizacaoEstoque.
- **ENDPOINTS:** GET estoque/unidades-logisticas e /{id}, estoque/locais e /{id}, estoque/locais/{id}/unidades-logisticas, estoque/movimentacoes/{id}; GET Almoxarifado, AreaEstoque/por-almoxarifado/{id}, localizacao-estoque/arvore-por-area/{id}; POST estoque/movimentacoes e /{id}/confirmacao, com idempotência/correlação/versões.
- **ERROS HTTP:** protegidos401; cargas públicas amostradas200. Não houve confirmação enviada.
- **FUNCIONALIDADE / FUNCIONA:** fluxo incompleto na verificação. Busca UL/origem/destino, revisão, criação e confirmação existem. Handlers alteram status/posição/versão de UL e registram eventos/outbox. Não foi executada gravação.
- **ESTOQUE DIRETO:** NÃO SUPORTADO por esse contrato; UnidadeLogisticaId obrigatório. **POR UL:** SUPORTADO na implementação, PARCIAL na validação.
- **FALHA REPRODUZIDA:** serviços reais GetLocalDeEstoqueByIdAsync(25), SearchLocaisDeEstoqueAsync("",1,10), GetUnidadesLogisticasDoLocalAsync(25,null,1,10) lançaram InvalidOperationException de tradução EF. Primeira/terceira usam LocalAtualId.Value em Where; busca falha por suporte de coleções primitivas não habilitado. São fluxos alcançados pela tela/árvore.500 autenticado é provável pelo middleware, mas não observado nesta rodada; não confundir exceção do serviço com HTTP medido.
- **ESTADO VAZIO/ERRO:** mensagens UL não encontrada/movimentação não carregada; árvore com estados vazios/erro; ramo de erro silencioso. Tratamento principal mostra mensagem e correlação,409 como atenção.
- **DADOS REAIS:**16 ULs;0 movimentações modernas.31 movimentos legados não equivalem a movimentações modernas.
- **PENDÊNCIAS:** tradução dos locais; sincronização de saldo legado ao mover UL não identificada; consistência de históricos; link auxiliar de locais sem /home.
- **RISCO:** ALTO. **AÇÃO:** reparar consultas e decidir coexistência saldo/UL antes de confirmar movimentos.
- **EVIDÊNCIA:** `B/PRPA/Controllers/EstoqueMovimentacoesController.cs`, `EstoqueLocaisController.cs`; `B/App.Service/Services/Estoque/Movimentacoes/Criar/CriarMovimentacaoDeEstoqueHandler.cs`, `Confirmar/ConfirmarMovimentacaoDeEstoqueHandler.cs`; `B/App.Domain/Entities/Estoque/Movimentacoes/MovimentacaoDeEstoque.cs`; `B/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`.

### TELA: Unidades Logísticas

- **ROTA:** `/home/operacao/unidades-logisticas`.
- **COMPONENTE:** `F/unidades-logisticas/components/unidades-logisticas/unidades-logisticas.component.ts`/`.html`.
- **SERVICES:** `F/estoque-consultas/services/consulta-estoque.service.ts`, `F/movimentacaoestoque-nova/services/movimentacaoestoque-nova.service.ts`.
- **ENDPOINTS:** GET estoque/unidades-logisticas?termo=&page=&pageSize=; GET /{id}; GET /{id}/movimentacoes?page=&pageSize=.
- **ERROS HTTP:**401 sem autenticação;500 autenticado não testado.
- **FUNCIONALIDADE / FUNCIONA:** PARCIALMENTE. Serviço real retornou16 ULs; termo ul16, acacc1, código completo UL-ACACC768DE1, termo16=2 (ID OU trecho do código), termo inexistente0. UL16: EMB-002,11 UN, local25 QUARE01, ativa, sem movimentação ativa. Histórico UL16 válido: items[],total0.
- **AUTOCOMPLETE/SELEÇÃO/DETALHES:** correção anterior separa erros de lista/sugestões/histórico, distingue erro/vazio e cancela subscriptions substituídas/no destroy. Testes automatizados anteriores não equivalem à navegação autenticada nesta rodada; interação permanece pendente. Detalhes reais do serviço foram lidos.
- **ESTADO VAZIO:** mensagens para lista/sugestões/histórico; campo Lote do DTO é null fixo atualmente, não prova de lote rastreado em UL.
- **BOTÃO MOVIMENTAR:** rota estática `/operacao/movimentacaoestoque-nova` omite /home; wildcard raiz redireciona sign-in. Clique real não executado.
- **PENDÊNCIAS:** sessão válida, GETs autenticados, console/interação e rota do botão; lote/escopo UL.
- **RISCO:** MÉDIO em consulta; ALTO na integração operacional. **AÇÃO:** homologar consulta autenticada e corrigir navegação no escopo aprovado.
- **EVIDÊNCIA BACKEND:** `B/PRPA/Controllers/EstoqueUnidadesLogisticasController.cs`, `B/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`; rota raiz em `FRONTEND/src/app/app-routing.module.ts`.

### TELA: Mapa do Estoque

- **ROTA:** `/home/operacao/mapa-estoque`.
- **COMPONENTE/SERVICE:** `F/mapa-estoque/components/mapa-estoque/mapa-estoque.component.ts`/`.html`; `F/mapa-estoque/services/mapa-estoque.service.ts`.
- **ENDPOINTS/HTTP:** GET Almoxarifado200; GET estoque/mapa?almoxarifadoId=11/12/14 retornou401 sem autenticação.
- **FUNCIONALIDADE / FUNCIONA:** serviço real carregou almoxarifado11:2 áreas,13 locais,11 saldos,0 ULs;12:2 áreas,6 locais,2 saldos,0 ULs;14:1 área,5 locais,1 saldo,1 UL. FALHA na cobertura das ULs atuais.
- **ESTOQUE DIRETO:** VISÍVEL na estrutura de dados/template por saldos, sem renderização comprovada. **UL:** VISÍVEL na estrutura/template, mas PARCIAL:1/16 aparece;15/16 excluídas pelo WarehouseId divergente do local. Não afirmar visibilidade completa dos estoques.
- **CONCEITO:** query lê SaldoEstoque e UnidadeLogistica separadamente; ocupado = saldo físico positivo OU UL ativa. Como Entrada em UL também alimenta saldo, saldo não significa exclusivamente direto. Sincronização do saldo ao mover UL e reconciliação de representação duplicada precisam de decisão funcional.
- **FILTROS/DETALHES:** área/finalidade/produto/qualidade/local/UL implementados no frontend, não testados interativamente. Detalhes mostram saldos/ULs; clique UL só mostra toast.
- **OCUPAÇÃO/STATUS:** frontend espera capacidadeMaxima/ocupacaoPercentual, ausentes de MapaLocalizacaoDto. Backend calcula valores sem entregá-los. Percentual não deve ser declarado funcional. Resumo rotulado “quantidade física” em quarentena recebe número de linhas de saldo, não soma física.
- **ESTADO VAZIO/ERRO:** sem áreas, sem correspondência de filtros e local sem saldo; erro com mensagem/toast.
- **PENDÊNCIAS:**15 ULs incoerentes/fora do mapa; saneamento de dados requer aprovação; totais/ocupação/coexistência.
- **RISCO:** ALTO. **AÇÃO:** corrigir origem do escopo e planejar remediação aprovada antes de declarar mapa concluído.
- **EVIDÊNCIA:** `B/PRPA/Controllers/MapaEstoqueController.cs`, `B/App.Infra.Data/Persistence/Estoque/Consultas/MapaEstoqueQueryService.cs`, `B/App.Service/DTOs/Estoque/MapaEstoque/MapaEstoqueDtos.cs`, SELECTs. Divergência com histórico que declarou Mapa concluído: aquela declaração não comprova consistência deste dataset/escopo; conteúdo histórico foi preservado.

### TELA: Transferência

- **ROTA:** `/home/operacao/transferenciaestoque`.
- **COMPONENTE:** `F/transferenciaestoque/components/transferenciaestoque/transferenciaestoque.component.ts`/`.html`.
- **SERVICES:** `F/transferenciaestoque/services/transferenciaestoque.service.ts`, `transferenciaestoqueitem.service.ts` e cargas comuns.
- **ENDPOINTS:** POST TransferenciaEstoque/TransferenciaEstoqueItem; GETs de cargas comuns. GET ambos recursos também sondados, embora service frontend não liste documentos persistidos.
- **HTTP/DADOS:** ambos GET200[]; ParametroValor404; demais cargas200; POST não executado.
- **SIGNIFICADO:** cabeçalho almoxarifado origem/destino; itens local origem/destino, produto/lote/unidade/quantidades. Representa transferência entre almoxarifados e locais; área indireta por local; entre organizações não identificado.
- **COMPARAÇÃO:** documento legado com itens versus movimentação moderna de uma UL. Sobreposição conceitual registrada; integração entre ambos não identificada. Não unificar por iniciativa própria.
- **FUNCIONA:** não comprovada como deslocamento efetivado de estoque. Services/repositories são CRUD genérico; débito/crédito de saldo e geração de movimento não identificados. Frontend salva cabeçalho e itens em requisições separadas, com risco de gravação parcial.
- **ESTADO VAZIO/ERRO:** “Nenhum item adicionado.”; falhas das cargas viram[]; gravação usa Swal. Parâmetros obrigatórios indisponíveis podem impedir submissão.
- **PENDÊNCIAS:** parâmetro404, efetivação/saldo/rastreabilidade/atomicidade e definição frente à Movimentação.
- **RISCO:** ALTO. **AÇÃO:** decisão funcional e fluxo transacional antes de homologar.
- **EVIDÊNCIA:** `B/App.Domain/Entities/PRPA/TransferenciaEstoque.cs`, `TransferenciaEstoqueItem.cs`; `B/App.Service/Services/TransferenciaEstoqueServices.cs`, `TransferenciaEstoqueItemServices.cs`; controllers correspondentes em `B/PRPA/Controllers`.

### TELA: Reserva

- **ROTA:** `/home/operacao/reservaestoque`.
- **COMPONENTE/SERVICE:** `F/reservaestoque/components/reservaestoque/reservaestoque.component.ts`/`.html`; `F/reservaestoque/services/reservaestoque.service.ts`.
- **ENDPOINTS:** GET/POST ReservaEstoque e cargas comuns.
- **HTTP/DADOS:** Reserva200[]; ParametroValor404; demais cargas200. Zero reservas; qtdreservada dos saldos0.
- **FUNCIONALIDADE / FUNCIONA:** parcial como CRUD, não comprovada como reserva operacional. Produto/lote/local e quantidades existem. Backend herda CRUD; atualização de qtdreservada/qtddisponivel de SaldoEstoque não identificada; sem triggers. TODO frontend reconhece a pendência.
- **LIBERAÇÃO/CANCELAMENTO:** não há método no service frontend atual; novo payload fixa qtdcancelada0/dataliberacao null. PUT genérico backend não prova liberação sincronizada.
- **DISPONÍVEL:** SaldoEstoque existe; consulta de saldo disponível na carga desta tela não identificada. Nenhuma reserva criada.
- **ESTADO VAZIO/ERRO:** lista inicial[]; carga/listagem convertem falha em[], mascarando erro; submissão Swal; visual não verificado.
- **PENDÊNCIAS:** parâmetros, disponibilidade/concorrência e efeitos de reservar/liberar no saldo.
- **RISCO:** ALTO. **AÇÃO:** completar efeitos após aprovação funcional.
- **EVIDÊNCIA:** `B/PRPA/Controllers/ReservaEstoqueController.cs`, `B/App.Service/Services/ReservaEstoqueServices.cs`, `B/App.Infra.Data/Repository/ReservaEstoqueRepository.cs`, `B/App.Domain/Entities/PRPA/ReservaEstoque.cs`.

### TELA: Bloqueio

- **ROTA:** `/home/operacao/bloqueioestoque`.
- **COMPONENTE/SERVICE:** `F/bloqueioestoque/components/bloqueioestoque/bloqueioestoque.component.ts`/`.html`; `F/bloqueioestoque/services/bloqueioestoque.service.ts`.
- **ENDPOINTS:** GET/POST BloqueioEstoque; frontend PUT BloqueioEstoque/{id}, backend PUT na raiz sem ID; cargas comuns.
- **HTTP/DADOS:** Bloqueio200[]; ParametroValor404; demais cargas200.0 bloqueios; qtdbloqueada de saldo0. PUT não enviado:405 esperado por contrato, não medido.
- **SUPORTE REAL:** registro por produto/local/quantidade com lote opcional. Não há ULid nesse contrato: bloqueio por UL não suportado. Referência a lote/local não equivale a alterar status global do lote ou LocalizacaoEstoque.bloqueada.
- **IMPACTO/FUNCIONA:** efeito em disponibilidade/movimentação/reserva/mapa não identificado nos services genéricos; sem triggers. Mapa lê qtdbloqueada do saldo e flag local, não BloqueioEstoque. Desbloqueio usa atualização incompatível.
- **ESTADO VAZIO/ERRO:** “Bloqueios nao encontrados.”; GET com catchError->[]; erros de salvar/desbloquear Swal.
- **PENDÊNCIAS:** parâmetros, PUT, alvos/efeitos, saldo disponível e restrições operacionais.
- **RISCO:** ALTO. **AÇÃO:** definir efeitos por alvo antes de homologação.
- **EVIDÊNCIA:** `B/App.Domain/Entities/PRPA/BloqueioEstoque.cs`, `B/App.Service/Services/BloqueioEstoqueServices.cs`, `B/PRPA/Controllers/BloqueioEstoqueController.cs`, MapaEstoqueQueryService.

### TELA: Inventário

- **ROTA:** `/home/operacao/inventarioestoque`.
- **COMPONENTE/SERVICES:** `F/inventarioestoque/components/inventarioestoque/inventarioestoque.component.ts`/`.html`; `F/inventarioestoque/services/inventarioestoque.service.ts`, `inventarioestoqueitem.service.ts`, `saldoestoque.service.ts`.
- **ENDPOINTS:** GET/POST InventarioEstoque; frontend PUT /{id}; GET/POST/PUT/{id}/DELETE/{id} InventarioEstoqueItem; GET SaldoEstoque e cargas comuns.
- **HTTP/DADOS:** cabeçalhos200[]; Saldo200 com14 linhas; InventarioEstoqueItem404 e ParametroValor404.0 inventários.
- **FUNCIONALIDADE / FUNCIONA:** incompleta. Cabeçalho/escopo almoxarifado/local opcional existe. Frontend importa saldo, conta/calcula divergência, mas controller/service/entidade atual dos itens não foram identificados. Migration histórica de itens não prova implementação atual; information_schema não encontrou tabela de itens.
- **FECHAMENTO/AJUSTE:** frontend apenas tenta PUT de datas/usuário de fechamento/aprovação; PUT/{id} diverge do controller PUT raiz. Geração de ajuste e congelamento efetivo de saldo não identificados. Fechar sem contagem permite confirmação frontend, sem regra backend comprovada.
- **ESTADO VAZIO/ERRO:** “Nenhum item adicionado.”; catchError->[] na carga, Swal nas operações.
- **PENDÊNCIAS:** API/contrato de itens, parametrização, atualização, fechamento e ajuste; sem criação automática de endpoint/migration nesta auditoria.
- **RISCO:** ALTO. **AÇÃO:** submeter conclusão do fluxo à aprovação.
- **EVIDÊNCIA:** `B/PRPA/Controllers/InventarioEstoqueController.cs`, `B/App.Service/Services/InventarioEstoqueServices.cs`, `B/App.Domain/Entities/PRPA/InventarioEstoque.cs`, `B/App.Infra.Data/Migrations/20260622123105_InventarioItemsEstoque.cs` (histórico).

### TELA: Ajuste

- **ROTA:** `/home/operacao/ajusteestoque`.
- **COMPONENTE/SERVICES:** `F/ajusteestoque/components/ajusteestoque/ajusteestoque.component.ts`/`.html`; `F/ajusteestoque/services/ajusteestoque.service.ts`, `ajusteestoqueitem.service.ts`, `saldoestoque.service.ts`.
- **ENDPOINTS:** GET/POST AjusteEstoque/AjusteEstoqueItem; frontend PUT ambos/{id}; GET SaldoEstoque e cargas comuns.
- **HTTP/DADOS:** cabeçalho/itens200[]; Saldo200; ParametroValor404. PUT não enviado; backend aceita PUT raiz, incompatível.
- **FUNCIONALIDADE / FUNCIONA:** documento/itens existem, efetivação não identificada. Produto/lote/local/unidade/motivo, qtdatual/qtdajustada permitem diferença +/-; validator exige quantidades finais >=0. Não interpretar ajuste negativo como quantidade final negativa.
- **RASTREABILIDADE:** aprovação apenas envia datas/usuário por PUT incompatível; geração de MovimentoEstoque e atualização de saldo não identificadas no backend genérico; TODO frontend. Não executado ajuste.
- **ATOMICIDADE:** cabeçalho/itens em chamadas separadas. Sucesso de salvar limpa ajusteAtual; revisar como aprovar posteriormente.
- **ESTADO VAZIO/ERRO:** “Nenhum item adicionado.”, aviso de diferença0 e Swal nos erros; cargas convertem falha em[].
- **PENDÊNCIAS:** parâmetros, PUT, efetivação +/-, rastreabilidade, aprovação/atomicidade.
- **RISCO:** ALTO. **AÇÃO:** concluir transação com aprovação antes de testar alterações de saldo.
- **EVIDÊNCIA:** controllers AjusteEstoque/AjusteEstoqueItem em `B/PRPA/Controllers`; services correspondentes em `B/App.Service/Services`; `B/App.Service/Validators/AjusteEstoqueItemValidator.cs`; entidades correspondentes em `B/App.Domain/Entities/PRPA`.

### TELA: Lotes — entidade e ciclo de vida

- **ROTA:** `/home/cadastro/listlotematerial`; criação `/home/cadastro/listlotematerial/cadlotematerial`; edição acrescenta /{id}. SELECT CMENU confirmou link do menu Lotes.
- **COMPONENTES:** `FRONTEND/src/app/application/cadastro/lotematerial/components/listlotematerial/listlotematerial.component.ts`/`.html`, `components/cadlotematerial/cadlotematerial.component.ts`/`.html` da mesma feature.
- **SERVICE FRONTEND:** `FRONTEND/src/app/application/cadastro/lotematerial/services/lotematerial.service.ts`; ProdutoService/FornecedoresService.
- **ENTIDADE:** LoteMaterial, `B/App.Domain/Entities/PRPA/LoteMaterial.cs`.
- **TABELA/MAPPING:** CLOTEMATERIAL, `B/App.Infra.Data/Mapping/LoteMaterialConfig.cs`.
- **CONTROLLER:** `B/PRPA/Controllers/LoteMaterialController.cs`.
- **SERVICE:** `B/App.Service/Services/LoteMaterialServices.cs`.
- **REPOSITORY:** `B/App.Infra.Data/Repository/LoteMaterialRepository.cs`, herda BaseRepository.
- **ENDPOINTS:** GET/POST/PUT LoteMaterial; GET/DELETE /{id}; GET /por-produto/{produtoId}, /por-codigo/{codlote}, /consulta. Lista usa GET LoteMaterial (ativos) em forkJoin com Produto e Fornecedor.
- **HTTP:** LoteMaterial200[], Produto200, Fornecedor404. Controller Fornecedor não identificado no backend atual; tabela correspondente não encontrada no levantamento. Frontend exige fornecedor no cadastro, apesar de domínio/DTO nullable.
- **POR QUE VAZIA:** zero lotes confirmados por SELECT, portanto GET200[] real. Adicionalmente Fornecedor404 derruba forkJoin; componente apenas console.log e mantém lista vazia, sem erro operacional. Sem navegador não atribuir captura visual a causa única: ambas as condições são comprovadas.
- **LEITURA:** backend propriedades produtoid/codlote/lotefornecedor/statusloteid; lista frontend usa produtoId/codLote/loteFornecedor/statusLoteId sem normalizar, diferentes em JavaScript. Edição normaliza. Defeito latente para dados futuros, não causa de zero registros no banco. Binding .NET ignora caixa: não inferir falha de POST só por essas grafias.
- **ESTADO VAZIO/ERRO:** “Lotes de materiais nao encontrados!”; erro de carga indistinto de vazio na tela; cadastro Swal/log.
- **ORIGEM / ONDE CRIADO:** fluxo identificado é cadastro manual: LoteMaterialController.Post -> AutoMapper -> LoteMaterialServices.PostAsync<LoteMaterialValidator> -> repository -> UnitOfWork. Valida produto/código/status e duplicidade de código por produto ativo. Nenhuma criação automática pela Entrada foi identificada. Produção/ERP/automação criadores de lote não identificados no fluxo auditado.
- **ONDE INFORMADO:** Entrada em lotematerialid, além dos documentos legados de movimento/saldo/reserva/bloqueio/transferência/ajuste e formulário de itens de inventário.
- **RELACIONAMENTOS REAIS:** lote pertence a Produto via produtoid; SaldoEstoque/MovimentoEstoque referenciam lote opcionalmente. UL atual não possui LoteMaterialId; relação indireta possível pelo movimento legado com lote e unidadeLogisticaId. DTO de UL devolve Lote null. MovimentacaoDeEstoque moderna referencia UL e locais, sem lote explícito.
- **CICLO RECONSTRUÍDO:** Produto -> cadastro LoteMaterial -> Entrada seleciona lote -> MovimentoEstoque + SaldoEstoque na localização -> UL opcional vinculada no movimento -> MovimentacaoDeEstoque desloca UL. Propagação para detalhes de UL e sincronização de saldo ao mover UL incompletas/não identificadas.
- **POR QUE NÃO EXISTEM REGISTROS HISTORICAMENTE:**31 movimentos atuais têm lote null e Entrada não cria automaticamente. Motivo histórico de nenhum cadastro ter persistido: **não identificado**; não atribuir a migration/regra inventada.
- **FUNCIONA:** parcial como implementação cadastral; falha de carga por dependência404. **RISCO:** ALTO para rastreabilidade. **AÇÃO:** resolver Fornecedor/contrato de leitura e testar cadastro+Entrada com produto que controla lote mediante fixtures.

### Banco — somente SELECT

| Medida | Resultado |
|---|---:|
| Total lotes | 0 |
| Produtos associados a lote | 0 |
| Lotes com saldo / saldo positivo | 0 / 0 |
| Lotes sem saldo | 0 |
| Lotes com movimento | 0 |
| Lotes em UL via movimento | 0 |
| ULs | 16 |
| ULs com WarehouseId diferente do almoxarifado do local | 15 |
| Saldos | 14 |
| Movimentos legados / com lote | 31 / 0 |
| Movimentações modernas | 0 |
| Triggers encontrados no schema | 0 |

Soma técnica qtdfisica/qtddisponivel60505,9995; qtdreservada/qtdbloqueada0. Não é total operacional: mistura produtos/unidades. SQL/resultados completos em selects.sql/db-select-results.json; nenhum INSERT/UPDATE/DELETE direto no banco.

### Console e classificação de pendências

**CRÍTICO confirmado em API/serviço/contrato, não em console:**404 de Fornecedor/ParametroValor/InventarioEstoqueItem; tradução de locais;15 ULs fora do mapa; PUTs incompatíveis. Runtime exceptions de navegador: não identificado. Não registrar “console limpo”.

**ATENÇÃO:** catchError->[] mascara erro; lote sem normalização na lista; capacidade/ocupação e unidade do resumo de quarentena divergentes; gravação de cabeçalho/itens em HTTPs separados; coexistência saldo/UL; falta de dados para teste persistente. **INFORMATIVO:** console.log de conclusão no código, sem prova de emissão; warnings de build existentes. Angular warnings, PrimeNG e Reactive Forms em execução: não identificados para cada tela.

**BLOQUEADORAS:** escopo WarehouseId na Entrada/dataset; tradução EF nas consultas de locais; itens de Inventário ausentes; ParametroValor404 necessário a status/motivo/tipo; efeitos de Transferência/Reserva/Bloqueio/Ajuste e ajuste do Inventário no saldo não identificados; PUTs de desbloqueio/fechamento/aprovação incompatíveis; sessão válida/fixtures para concluir homologação.

**IMPORTANTES:** Fornecedor404 em Lotes; lote->UL incompleto; botões sem /home; exposição técnica no catch de Entrada; saldo legado sem sincronização identificada no movimento moderno; distinguir vazio/erro e normalizar resposta de lotes.

**MELHORIAS:** abrir detalhes de UL a partir do mapa; carga degradada com aviso; métricas com contrato/unidade coerentes após decisão funcional.

**DÚVIDA TÉCNICA:** origem oficial PlantId; saldo agregado/direto versus UL; política de bloqueio/reserva/liberação/congelamento/aprovação/efetivação; histórico do dataset. Nenhuma decisão marcada como aprovada.

Próximas correções recomendadas, sujeitas a decisão do usuário: (1) corrigir escopo na Entrada e aprovar saneamento de dados; (2) reparar consultas EF/rotas; (3) resolver contratos/dependências existentes; (4) definir/completar efeitos de saldo, rastreabilidade e atomicidade; (5) homologar com sessão e fixtures os dois modos e demais operações. Não criar automaticamente entidades/endpoints/migrations para contornar404.

### Builds, prontidão e encerramento

- Backend: dotnet build PRPA.sln --no-restore --verbosity quiet, em BACKEND/PRPA: exit0, **0 erros /2 avisos** (NU1903 AutoMapper; NU1902 MailKit).
- Frontend: npm.cmd run build, em FRONTEND: exit0, **0 erros**. Warnings NG8107 em EstruturaProduto, CommonJS sweetalert2/moment, bootstrap/main CSS não localizados e2 seletores descartados. Warnings antigos não corrigidos.
- Logs em .codex/estoque-auditoria-20261002/backend-build.log e frontend-build.log. Build não equivale à homologação funcional.
- **PRONTIDÃO: NÃO PRONTO.** Há falhas de consulta, escopo inconsistente, dependências essenciais ausentes e efeitos operacionais não comprovados.
- Produção/Apontamento não iniciado. Levantamento de seus artefatos não executado, pois o pedido o condicionou à estabilidade do Estoque.
- **STATUS: diagnóstico estático/API pública/banco concluído; homologação runtime/persistente incompleta — aguardando decisão do usuário.** Registros históricos e decisões preservados; telas não homologadas automaticamente.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Entidades de estoque

| Grupo | Entidades | Evidencia | Classificacao |
|---|---|---|---|
| Cadastros de estrutura fisica | `Almoxarifado`, `TipoAreaEstoque`, `AreaEstoque`, `TipoLocalizacao`, `LocalizacaoEstoque`, `LocalizacaoEstoqueTreeNode` | `BACKEND/PRPA/App.Domain/Entities/PRPA/Almoxarifado.cs`; `AreaEstoque.cs`; `LocalizacaoEstoque.cs`; `TipoLocalizacao.cs`; `TipoAreaEstoque.cs` | Confirmado |
| Lotes e saldos | `LoteMaterial`, `SaldoEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/LoteMaterial.cs`; `SaldoEstoque.cs` | Confirmado |
| Movimentacoes e controle | `MovimentoEstoque`, `ReservaEstoque`, `BloqueioEstoque`, `InventarioEstoque` | `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`; `ReservaEstoque.cs`; `BloqueioEstoque.cs`; `InventarioEstoque.cs` | Confirmado |
| Recebimento, transferencia e ajuste | `RecebimentoEstoque`, `RecebimentoEstoqueItem`, `TransferenciaEstoque`, `TransferenciaEstoqueItem`, `AjusteEstoque`, `AjusteEstoqueItem` | `BACKEND/PRPA/App.Domain/Entities/PRPA/RecebimentoEstoque.cs`; `TransferenciaEstoque.cs`; `AjusteEstoque.cs` | Confirmado |

## Cadastros de estoque na interface

| Tela/rota | Evidencia frontend | Evidencia backend | Classificacao |
|---|---|---|---|
| `listalmoxarifado` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AlmoxarifadoController.cs`; `Almoxarifado.cs` | Confirmado |
| `listtipoareaestoque` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/TipoAreaEstoqueController.cs`; `TipoAreaEstoque.cs` | Confirmado |
| `listareaestoque` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; `FRONTEND/src/app/application/cadastro/areaestoque/services/areaestoque.service.ts` | `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs`; `AreaEstoque.cs` | Confirmado |
| `listtipolocalizacao` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/TipoLocalizacaoController.cs`; `TipoLocalizacao.cs` | Confirmado |
| `listlocalizacaoestoque` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs`; `LocalizacaoEstoque.cs` | Confirmado |
| `listlotematerial` | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | `BACKEND/PRPA/PRPA/Controllers/LoteMaterialController.cs`; `LoteMaterial.cs` | Confirmado |

## Operacoes de estoque na interface

| Operacao | Rota frontend | Evidencia | Classificacao |
|---|---|---|---|
| Entrada de estoque | `entradaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Saida de estoque | `saidaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Transferencia de estoque | `transferenciaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Reserva de estoque | `reservaestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Bloqueio de estoque | `bloqueioestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Inventario de estoque | `inventarioestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Ajuste de estoque | `ajusteestoque` | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |

## Endpoints especificos observados

| Controller | Endpoints observados | Evidencia | Classificacao |
|---|---|---|---|
| `LocalizacaoEstoqueController` | `GET`, `GET {id}`, `GET por-area/{areaEstoqueId}`, `GET por-almoxarifado/{almoxarifadoId}`, `GET arvore-por-area/{areaEstoqueId}`, `POST`, `PUT {id}`, `DELETE {id}` | `BACKEND/PRPA/PRPA/Controllers/LocalizacaoEstoqueController.cs` | Confirmado |
| `TipoLocalizacaoController` | `GET`, `GET ativos`, `GET {id}`, `POST`, `PUT {id}`, `DELETE {id}` | `BACKEND/PRPA/PRPA/Controllers/TipoLocalizacaoController.cs` | Confirmado |
| `LoteMaterialController` | `GET por-produto/{produtoId}`, `GET por-codigo/{codlote}`, `GET consulta` alem de CRUD | `BACKEND/PRPA/PRPA/Controllers/LoteMaterialController.cs` | Confirmado |
| `AreaEstoqueController` | Endpoints de listagem ativa e por almoxarifado foram identificados. | `BACKEND/PRPA/PRPA/Controllers/AreaEstoqueController.cs` | Confirmado |

## Regras e relacionamentos de estoque confirmados

| Item | Evidencia | Classificacao |
|---|---|---|
| Areas de estoque pertencem a almoxarifado e tipo de area. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AreaEstoqueConfig.cs` | Confirmado |
| Localizacoes pertencem a almoxarifado, area, tipo e podem ter localizacao pai. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LocalizacaoEstoqueConfig.cs` | Confirmado |
| Lotes pertencem a produto. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/LoteMaterialConfig.cs` | Confirmado |
| Saldos vinculam produto, lote, almoxarifado, localizacao e unidade. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/SaldoEstoqueConfig.cs` | Confirmado |
| Movimentos registram origem/destino de almoxarifado e localizacao, produto, lote, unidade e movimento de origem. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/MovimentoEstoqueConfig.cs` | Confirmado |
| Transferencias possuem cabecalho com almoxarifado origem/destino e itens com localizacoes origem/destino. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/TransferenciaEstoqueConfig.cs`; `TransferenciaEstoqueItemConfig.cs` | Confirmado |
| Ajustes possuem cabecalho por almoxarifado e itens por produto/lote/localizacao/unidade. | `BACKEND/PRPA/App.Infra.Data/Map/PRPA/AjusteEstoqueConfig.cs`; `AjusteEstoqueItemConfig.cs` | Confirmado |

## Funcionalidades parciais e duvidas

| Item | Evidencia | Classificacao |
|---|---|---|
| Atualizacao automatica de saldo a partir de operacoes de estoque nao foi confirmada nesta leitura. | Entidades e telas existem, mas a regra transacional completa nao foi identificada nos arquivos analisados. | Nao identificado |
| Catalogos para status/tipo/motivo de movimentos, reservas, lotes e documentos nao foram identificados como entidades. | Campos aparecem em entidades como `MovimentoEstoque.cs`, `ReservaEstoque.cs`, `LoteMaterial.cs` e `RecebimentoEstoque.cs`. | Nao identificado |
| Integracao com ERP/WMS externo para estoque nao foi encontrada no codigo analisado. | Busca em controllers, services e environments nao confirmou endpoint externo de ERP/WMS. | Nao identificado |

## Entrada Direta (EST-OP-02C)

| Item | Evidencia | Classificacao |
|---|---|---|
| Entrada Direta — sincroniza MovimentoEstoque + SaldoEstoque atomicamente | `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs` | Confirmado |
| Conversão de unidade (mesma / produto / global) aplicada na entrada | `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs` | Confirmado |
| Rollback transacional via `IUnitOfWork.ExecuteAsync` | `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs` | Confirmado |
| Entrada com Unidade Logística (UL) opcional | `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs` (linha 78-135) | Confirmado |
| Contrato de resposta enxuto: `EntradaDiretaResponseDto` | `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/EntradaDiretaResponseDto.cs` | Confirmado |
| Contrato inclui: `movimentoId`, `unidadeLogisticaId`, `unidadeLogisticaCodigo`, `statusQualidade` | `EntradaDiretaResponseDto.cs` | Confirmado |
| Frontend recebe tipos primitivos; não recebe entidades de domínio completas | `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts` | Confirmado |
| Mensagem pós-entrada exibe: Movimento ID, UL ID, Código UL, Status Quarentena | `entradaestoque.component.ts` (showSuccessMessage) | Confirmado |
| Botões pós-entrada: "Ver Movimento" (usa movimentoId), "Ver UL" (usa unidadeLogisticaId) | `entradaestoque.component.ts` | Confirmado |
| Correção histórica de `[object Object]` causada por exposição de Value Objects no contrato | `EST-OP-02C.3-D1.20.6` e `EST-OP-02C.3-D1.20.7` | Confirmado |
| Dívida técnica: `MovimentoEstoqueService.cadastrarMovimentoEstoque` tipado como `any` temporariamente | `movimentoestoque.service.ts` | Confirmado |

## Identidade da Unidade Logística

| Item | Evidencia | Classificacao |
|---|---|---|
| `CUNIDADELOGISTICA.Id` gerado pelo banco (AUTO_INCREMENT) | `BACKEND/PRPA/App.Infra.Data/Mapping/Estoque/UnidadeLogisticaConfig.cs` | Confirmado |
| EF Core: `ValueGeneratedOnAdd()` | `UnidadeLogisticaConfig.cs` | Confirmado |
| `UnidadeLogisticaId.Create(0)` permanece INVÁLIDO (domain exception) | `BACKEND/PRPA/App.Domain/Entities/Estoque/Shared/ValueObjects.cs` | Confirmado |
| Estado transiente da entidade nova tratado separadamente do ID persistido | `EntradaDiretaSincronizacaoServices.cs` (linha 119-134) | Confirmado |
| Migration corretiva: `FixUnidadeLogisticaIdentity` | `BACKEND/PRPA/App.Infra.Data/Migrations/20260911112709_FixUnidadeLogisticaIdentity.cs` | Confirmado |
| Remediação: UL antiga Id 0 → Id positivo; referência em `CMOVIMENTOESTOQUE` preservada | Migration designer + SQL | Confirmado |

## ExigeInspecao e Qualidade

| Item | Evidencia | Classificacao |
|---|---|---|
| `Produto.ExigeInspecao` (bool) configurável no cadastro | `BACKEND/PRPA/App.Domain/Entities/PRPA/Produto.cs` | Confirmado |
| DTOs persistem: `ProdutoCreateDto`, `ProdutoUpdateDto` | `BACKEND/PRPA/App.Service/DTOs/Produto/` | Confirmado |
| Regra: ExigeInspecao=false → `StatusQualidade = Liberado` | `EntradaDiretaSincronizacaoServices.cs` (linha 72) | Confirmado |
| Regra: ExigeInspecao=true → `StatusQualidade = EmQuarentena` | `EntradaDiretaSincronizacaoServices.cs` (linha 72) | Confirmado |
| Saldo em Quarentena: físico AUMENTA; disponível NÃO AUMENTA | `EntradaDiretaSincronizacaoServices.cs` (linha 144-149) | Confirmado |
| Consumo não pode utilizar saldo em Quarentena | Regras de domínio / `SaldoEstoque` | Confirmado |

## Finalidade Operacional dos Locais de Estoque

| Item | Evidencia | Classificacao |
|---|---|---|
| Hierarquia: LocalizacaoEstoque → AreaEstoque → TipoAreaEstoque → FinalidadeOperacional | `BACKEND/PRPA/App.Domain/Entities/PRPA/` | Confirmado |
| `FinalidadeOperacional` é ENUM | `BACKEND/PRPA/App.Domain/Entities/Estoque/Shared/ValueObjects.cs` | Confirmado |
| Valores: Armazenagem, Quarentena, Refugo, Picking, Recebimento, Expedicao, Producao, Outro | ValueObjects.cs | Confirmado |
| Quarentena determinada APENAS por FinalidadeOperacional (não por nome/flag) | `LocalizacaoEstoqueServices.cs` / Domain | Confirmado |
| NÃO existe flag `EhQuarentena` em LocalizacaoEstoque | Confirmado por ausência | Confirmado |
| Migration: `AddFinalidadeOperacionalToTipoAreaEstoque` | `BACKEND/PRPA/App.Infra.Data/Migrations/20260914193821_AddFinalidadeOperacionalToTipoAreaEstoque.cs` | Confirmado |
| Remediação inicial: MP-01/PRA-01 → Armazenagem; REF-001 → Refugo; QUA-01 → Quarentena; PI-001 → Picking | Migration SQL | Confirmado |

## Validação de Quarentena na Entrada

| Cenário | Resultado | Evidencia |
|---|---|---|
| Produto ExigeInspecao=true + localização não Quarentena | BLOQUEAR | `EntradaDiretaSincronizacaoServices.cs` |
| Produto ExigeInspecao=true + localização Quarentena | PERMITIR | `EntradaDiretaSincronizacaoServices.cs` |
| Produto ExigeInspecao=false + localização Quarentena | BLOQUEAR | `EntradaDiretaSincronizacaoServices.cs` |
| Produto ExigeInspecao=false + localização normal | PERMITIR | `EntradaDiretaSincronizacaoServices.cs` |
| Erros de negócio lançam `DomainException` (mensagem amigável) | DomainException | Confirmado |

## Tratamento de Erros

| Item | Evidencia | Classificacao |
|---|---|---|
| Erros de negócio: mensagem operacional amigável | `ExceptionMiddleware` / Services | Confirmado |
| Frontend NÃO recebe: stack trace, inner exception, path físico, SQL, assembly | `ExceptionMiddleware` / Controllers | Confirmado |
| ExceptionMiddleware separa: erro de negócio vs erro técnico | `BACKEND/PRPA/PRPA/Middleware/ExceptionMiddleware.cs` | Confirmado |

## Próxima Fase

| Item | Status |
|---|---|
| Próxima prioridade funcional: **MAPA DE ESTOQUE** | Definido em `MES-PROJECTBOOK-UPDATE-01` |
| Fase: `EST-OP-02C.4-AUDIT` — Levantamento de arquitetura e dados existentes | Registrado |
| Gestão de capacidade/ocupação permanece no backlog (NÃO é próxima fase) | Confirmado |

## Network observado na auditoria de 2026-10-02

Chamadas diretas de diagnóstico, não captura da aba Network do navegador. Corpos originais em `.codex/estoque-auditoria-20261002/network-readonly.json` e `network-samples.json`. HTTP 401 dos recursos protegidos foi medido sem sessão válida; nenhuma operação de estoque foi enviada para gravação.

| Method | URL completa | HTTP | Observação |
|---|---|---:|---|
| POST | http://localhost:5046/api/Auth/login | 401 | Identificação fornecida não encontrada; credenciais não registradas |
| GET | http://localhost:5046/api/Produto | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/ProdutoVersao | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/Fornecedor | 404 | Dependência da tela ausente |
| GET | http://localhost:5046/api/LoteMaterial | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/Almoxarifado | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/localizacao-estoque | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/localizacao-estoque/elegives-entrada | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/UnidadeMedida | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/UnidadeMedida/unidades-recebiveis-produto/15 | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/MotivoMovimento/ativos | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/TipoDocumento/ativos | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/TipoMovimento/ativos | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/ParametroValor | 404 | Dependência da tela ausente |
| GET | http://localhost:5046/api/MovimentoEstoque | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/MovimentoEstoque/31 | 404 | ID sondado inexistente; ID1 retornou200 |
| GET | http://localhost:5046/api/SaldoEstoque | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/ReservaEstoque | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/BloqueioEstoque | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/InventarioEstoque | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/InventarioEstoqueItem | 404 | Dependência da tela ausente |
| GET | http://localhost:5046/api/AjusteEstoque | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/AjusteEstoqueItem | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/TransferenciaEstoque | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/TransferenciaEstoqueItem | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/AreaEstoque/por-almoxarifado/11 | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/localizacao-estoque/arvore-por-area/1 | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?termo=ul&page=1&pageSize=10 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/unidades-logisticas/16 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/unidades-logisticas/16/movimentacoes?page=1&pageSize=5 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/locais/25 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/locais/25/unidades-logisticas?page=1&pageSize=50 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/movimentacoes/1 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/mapa?almoxarifadoId=11 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/mapa?almoxarifadoId=12 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/estoque/mapa?almoxarifadoId=14 | 401 | Protegido; sessão válida pendente |
| GET | http://localhost:5046/api/LoteMaterial/por-produto/15 | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/LoteMaterial/por-codigo/UL | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/LoteMaterial/consulta | 200 | Coleção vazia real |
| GET | http://localhost:5046/api/MovimentoEstoque/1 | 200 | Resposta recebida; corpo no artefato |
| GET | http://localhost:5046/api/localizacao-estoque/arvore-por-area/1 | 200 | Resposta recebida; corpo no artefato |

Todos os demais POST/PUT/DELETE descritos por tela tiveram somente seus contratos inspecionados: status HTTP **não identificado**, porque não foram executados. Incompatibilidades PUT foram confirmadas no código;405 é esperado, não medido.

### URLs frontend — somente shell

| URL completa | HTTP | Carregamento Angular/console |
|---|---:|---|
| http://localhost:4200/home/operacao/entradaestoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/movimentacaoestoque-nova | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/unidades-logisticas | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/mapa-estoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/transferenciaestoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/reservaestoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/bloqueioestoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/inventarioestoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/operacao/ajusteestoque | 200 | Não identificado; Browser indisponível |
| http://localhost:4200/home/cadastro/listlotematerial | 200 | Não identificado; Browser indisponível |

## Network autenticado — complemento de 2026-10-02

POST de login200; credenciais/token omitidos. GETs abaixo feitos com sessão válida; chamadas diretas de diagnóstico, sem captura do navegador.

| Method | URL completa | HTTP |
|---|---|---:|
| POST | http://localhost:5046/api/Auth/login | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?page=1&pageSize=10 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?termo=ul&page=1&pageSize=10 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?termo=acacc&page=1&pageSize=10 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?termo=UL-ACACC768DE&page=1&pageSize=10 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?termo=16&page=1&pageSize=10 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas?termo=SEMRESULTADO_AUDIT_20261002&page=1&pageSize=10 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas/16 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas/16/movimentacoes?page=1&pageSize=5 | 200 |
| GET | http://localhost:5046/api/estoque/unidades-logisticas/999999999 | 404 |
| GET | http://localhost:5046/api/estoque/locais/25 | 500 |
| GET | http://localhost:5046/api/estoque/locais?termo=&page=1&pageSize=10 | 500 |
| GET | http://localhost:5046/api/estoque/locais/25/unidades-logisticas?page=1&pageSize=50 | 500 |
| GET | http://localhost:5046/api/estoque/movimentacoes/1 | 404 |
| GET | http://localhost:5046/api/estoque/mapa?almoxarifadoId=11 | 200 |
| GET | http://localhost:5046/api/estoque/mapa?almoxarifadoId=12 | 200 |
| GET | http://localhost:5046/api/estoque/mapa?almoxarifadoId=14 | 200 |
