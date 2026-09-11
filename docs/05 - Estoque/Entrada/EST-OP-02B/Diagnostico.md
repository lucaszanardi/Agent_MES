# EST-OP-02B — Diagnóstico consolidado

## Escopo e situação inicial

Consolidação documental em 08/09/2026. O diagnóstico 02B.1 descreve o estado de 01/09/2026, não o estado integral do código atual. Exemplos de produtos, lotes e endereços dos relatórios não comprovam cadastros existentes no banco atual.

## Problemas e evolução

| Problema | Evidência | Impacto | Decisão tomada / evolução |
|---|---|---|---|
| Parâmetros legados | 02B.1 registra chamada a ParametroValor sem endpoint e tabelas removidas; 02B.2 registra substituição por catálogos tipados. A tela atual importa TipoMovimentoService, MotivoMovimentoService e TipoDocumentoService. | Tipo de entrada, motivo e documento indisponíveis bloqueavam a confirmação. | A recomendação inicial de restaurar ParametroValor foi substituída pelo uso dos catálogos tipados. |
| Localização estrutural selecionável | 02B.1 cita RUA1; hoje a tela consulta getLocalizacaoElegiveisEntrada e o serviço usa ClassificacaoLocalizacaoHelper. | A árvore podia oferecer posição estrutural como destino físico. | Validar armazenagem efetiva: não bloqueada, sem filhos e finalidade Armazenagem. |
| Versão e lote sem opções | 02B.1 registra “Sem versão” e “Sem lote”, sem comprovar ausência de dados. A tela atual filtra pelo produto. | Lista vazia podia ser interpretada como erro da API. | Não presumir dados ausentes ou obrigatoriedade de versão. Validar lote conforme ControlaLote no backend. |
| Validação de lote inconsistente | 02B.2 mistura afirmação de implementação com pendência backend. O formulário atual deixa lotematerialid sem obrigatoriedade dinâmica. | A UI permite envio sem lote; o serviço rejeita se ControlaLote. | A evolução 02C implementa validação autoritativa de existência, produto e ativo do lote. |
| DTO/usuário | 02B.6 relata string Identity incompatível com int? no DTO; 02B.7 relata a falha posterior de AutoMapper contra a entidade int?. | Corrigir apenas o DTO não concluía a operação. | DTO string?, usuarioid ignorado no MappingProfile e auditoria preenchida pelas claims no controller. |
| Movimento sem saldo | POST genérico usa MovimentoEstoqueServices; entrada-direta usa EntradaDiretaSincronizacaoServices. | Movimento registrado não significava saldo incrementado. | Evoluir para Entrada → MovimentoEstoque → SaldoEstoque em 02C.1. |
| Estoque confundido com UL | SaldoEstoque não contém vínculo com UL; a arquitetura da Entrada prevê identidade logística opcional. | Exigir UL excluiria estoque direto; somar saldo e UL duplicaria quantidade. | Saldo quantitativo e UL opcional, ainda planejada na Entrada. |
| Leitura do mapa | 02C.1 e ConsultaOperacionalEstoqueService mostram leitura de UL, sem leitura de SaldoEstoque nesse serviço. | Estoque direto pode estar persistido sem aparecer no mapa. | Registrar a lacuna de leitura; sincronizar saldo não resolve automaticamente o mapa. |

## Conclusões técnicas

O 02B tratou dependências e contrato da tela. Sincronização quantitativa, unidades e conversões motivaram o 02C; integração com UL e quarentena continuam planejadas.

A análise atual identifica incompatibilidade adicional entre payload convertido e DTO, detalhada em Implementacao.md do 02C. Esse achado não comprova a causa do HTTP 500 informado pelo solicitante, cuja causa raiz permanece não identificada. Nenhuma correção funcional foi realizada.

## Evidências / Referências

- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.1_DIAGNOSTICO.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.2_RELATORIO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.6_RESULTADO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.7_RESULTADO_FINAL.md`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ClassificacaoLocalizacaoHelper.cs`
- `BACKEND/PRPA/App.Service/DTOs/MovimentoEstoque/MovimentoEstoqueCreateDto.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/MovimentoEstoque.cs`
- `BACKEND/PRPA/App.Infra.CrossCutting.IoC/MappingProfile.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/SaldoEstoque.cs`
- `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1_RESULTADO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C-ARCH_DECISAO_FINAL.md`
