# MAPA DE ESTOQUE — Visão Geral e Requisitos

## 1. Objetivo
O **Mapa de Estoque** é a próxima prioridade funcional aprovada (fase `EST-OP-02C.4` e seguintes) para o domínio de Estoque do MES. Ele tem como objetivo fornecer visibilidade operacional completa e em tempo real dos materiais existentes no estoque industrial.

## 2. Decisão Arquitetural: Layout Visual Opcional
O cadastro operacional de estoque permanece independente da representação gráfica do armazém.
- `LocalizacaoEstoque`, `AreaEstoque`, `TipoAreaEstoque` e `Almoxarifado` continuam representando a estrutura operacional oficial.
- Será planejado um domínio/cadastro paralelo e **OPCIONAL** de **Layout Visual do Armazém** (`LayoutArmazem` / `LayoutElemento`).
- O Layout Visual **não** substitui `LocalizacaoEstoque`, **não** controla saldo, UL ou movimentação, servindo exclusivamente para representação gráfica física/visual.

## 3. Modos do Mapa de Estoque
- **Modo A (Automático / V1)**: Funciona sem layout visual configurado, gerando uma visualização automática baseada na hierarquia `Almoxarifado` → `Área` → `Localização` → `Produto` / `Saldo` / `UL` / `StatusQualidade`. **IMPLEMENTADO (Backend + Frontend)**.
- **Modo B (Visual Personalizado / Opcional)**: Utiliza a representação física personalizada do armazém sobrepondo os dados operacionais reais.
- A evolução visual prevê progressão do Nível 1 (grid automático) até Nível 4 (3D interativo).

## 4. Hierarquia Conceitual
A visualização hierárquica do mapa estrutura-se do macro para o micro:
- Planta (`PlantId`)
- Almoxarifado (`WarehouseId`)
- Área de Estoque (`AreaEstoque`)
- Localização de Estoque (`LocalizacaoEstoque`) com sua `FinalidadeOperacional`
- Produtos, Saldos (`SaldoEstoque`) e Unidades Logísticas (`UnidadeLogistica`)

## 5. Informações Candidatas Exibidas
O mapa deve permitir compreender:
- Onde o material está armazenado (Caminho completo da localização)
- Qual material está armazenado (Código e Descrição do Produto)
- Quantidade física existente
- Quantidade disponível
- Quantidade bloqueada
- Quantidade em quarentena (`StatusQualidade = EmQuarentena`)
- Unidade de medida associada
- Relação de Unidades Logísticas (ULs) associadas e seus respectivos códigos e status de qualidade
- Lotes de material, quando aplicável
- Data e hora da última movimentação

## 6. Filtros Candidatos
- Planta
- Almoxarifado
- Área
- Finalidade Operacional
- Localização específica
- Produto (Código / Descrição)
- Status de Qualidade (Liberado / Em Quarentena)
- Unidade Logística (UL)
- Lote

## 7. Princípio para Ocupação e Capacidade
- **Ocupação**: Não será persistida uma flag booleana redundante "Ocupado" na localização. A ocupação será derivada diretamente da existência de saldos físicos positivos ou de unidades logísticas ativas na localização.
- **Capacidade**: A gestão de capacidade física (peso máximo, volume máximo, cubagem, restrições dimensionais e percentual de ocupação por capacidade) permanece estritamente planejada para o roadmap futuro e **NÃO** faz parte desta fase do Mapa de Estoque.

## 8. Implementação Realizada

### Backend (EST-OP-02C.4-D1)
- **Endpoint**: `GET /api/estoque/mapa?almoxarifadoId={id}`
- **DTO Raiz**: `MapaEstoqueDto`
- **Queries**: 5 queries otimizadas (Almoxarifado, Áreas, Localizações, Saldos, ULs) — sem N+1
- **Recursos**: Seleção obrigatória de Almoxarifado, KPIs, Áreas, Localizações, Ocupação derivada, Saldos, Qualidade, ULs, Última movimentação

### Frontend (EST-OP-02C.4-D2)
- **Rota Operacional**: `/operacao/locais-estoque` (Atualizada)
- **Componentes**: `LocaisEstoqueComponent` refatorado para consumir API consolidada
- **Visualização**: Grid de KPIs, Áreas expansíveis, Cards de localização com status visual
- **Filtros**: Área, Busca por termo, Seleção de Almoxarifado obrigatória
- **Estados Visuais**: Vazia (cinza), Ocupada (verde), Quarentena (amarelo), Bloqueada/Reprovada (vermelho)
- **Detalhe**: Painel lateral com Saldos e ULs
- **Integração Movimentação**: Botão "Usar como Destino" mantido

## 9. Status Atual
- **Modo Automático (V1)**: **IMPLEMENTADO** ✅
- **Layout Visual Personalizado**: **DECIDIDO / NÃO IMPLEMENTADO** 📋
- **Capacidade Avançada**: **BACKLOG** 📋

## 10. Próximas Fases
- `EST-OP-02C.4-D3` — Testes automatizados e validação de performance
- `EST-OP-02C.4-D4` — Layout Visual Personalizado (Opcional)
