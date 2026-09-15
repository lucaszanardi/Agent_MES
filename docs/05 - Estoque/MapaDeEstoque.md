# MAPA DE ESTOQUE — Visão Geral e Requisitos

## 1. Objetivo
O **Mapa de Estoque** é a próxima prioridade funcional aprovada (fase `EST-OP-02C.4` e seguintes) para o domínio de Estoque do MES. Ele tem como objetivo fornecer visibilidade operacional completa e em tempo real dos materiais existentes no estoque industrial.

## 2. Hierarquia Conceitual
A visualização hierárquica do mapa estrutura-se do macro para o micro:
- Planta (`PlantId`)
- Almoxarifado (`WarehouseId`)
- Área de Estoque (`AreaEstoque`)
- Localização de Estoque (`LocalizacaoEstoque`) com sua `FinalidadeOperacional`
- Produtos, Saldos (`SaldoEstoque`) e Unidades Logísticas (`UnidadeLogistica`)

## 3. Informações Candidatas Exibidas
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

## 4. Filtros Candidatos
- Planta
- Almoxarifado
- Área
- Finalidade Operacional
- Localização específica
- Produto (Código / Descrição)
- Status de Qualidade (Liberado / Em Quarentena)
- Unidade Logística (UL)
- Lote

## 5. Princípio para Ocupação e Capacidade
- **Ocupação**: Não será persistida uma flag booleana redundante "Ocupado" na localização. A ocupação será derivada diretamente da existência de saldos físicos positivos ou de unidades logísticas ativas na localização.
- **Capacidade**: A gestão de capacidade física (peso máximo, volume máximo, cubagem, restrições dimensionais e percentual de ocupação por capacidade) permanece estritamente planejada para o roadmap futuro e **NÃO** faz parte desta fase do Mapa de Estoque.
