# EST-OP-02B — Resultado consolidado

## Resultado sustentado pelas evidências

O 02B consolidou correções de parametrização, seleção de destino e contrato de usuário. Não encerrou toda a Entrada de Estoque.

| Item | Resultado e limite |
|---|---|
| Parâmetros | A tela usa catálogos tipados de tipo de movimento, motivo e documento, substituindo a dependência de ParametroValor relatada no diagnóstico. |
| Localização | A tela consulta destinos elegíveis e filtra por almoxarifado. A regra centralizada considera bloqueio, filhos e finalidade. |
| Versão e lote | Filtros por produto existem. A validação dinâmica de lote obrigatório na UI não foi identificada; o serviço do 02C faz a validação autoritativa. |
| DTO — 02B.6 | usuarioid string? corrige o contrato descrito, mas ainda existia incompatibilidade com int? na entidade. |
| Mapeamento — 02B.7 | MappingProfile ignora usuarioid nos mapas de criação e atualização. O controller usa claims para UsuarioCriacao/UsuarioEdicao. Não se deve afirmar que a correção do DTO isoladamente resolveu tudo. |

## Validações e limites históricos

A inspeção atual confirma os mecanismos acima no código. O relatório 02B.6 registra build bloqueado por arquivo em uso e teste visual pendente; sucesso esperado não equivale a execução comprovada.

02C.1 contém registros de builds, testes não executados e falha de compilação de testes em verificações distintas. O relatório posterior 1D informa builds PASS e 7/7 testes PASS, mas runtime não validado. Esta tarefa não executou builds, testes ou requisições.

## Pendências e decisões que levaram ao 02C

- Sincronização MovimentoEstoque → SaldoEstoque: implementada depois, em 02C.1; não atribuída ao 02B.
- Unidade de estoque automática e conversões: evolução 1B, 1C e 1D.
- Mapa: leitura operacional baseada em UL; exibição do saldo direto não identificada no serviço inspecionado.
- UL opcional e quarentena: planejadas na Entrada.
- Runtime atual: HTTP 500 ao confirmar entrada, informado pelo solicitante, com causa raiz não identificada; 1D sem homologação runtime.

A arquitetura consolidada usa SaldoEstoque como fonte de verdade quantitativa do MVP, MovimentoEstoque como histórico e UL como identidade opcional. A divergência com o DL-0022, que define saldo como projeção, está registrada na arquitetura do 02C, sem alterar o Decision Log.

## Evidências / Referências

- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.2_RELATORIO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.6_RESULTADO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02B/EST-OP-02B.7_RESULTADO_FINAL.md`
- `FRONTEND/src/app/application/operacao/entradaestoque/components/entradaestoque/entradaestoque.component.ts`
- `BACKEND/PRPA/App.Domain/Entities/PRPA/ClassificacaoLocalizacaoHelper.cs`
- `BACKEND/PRPA/App.Infra.CrossCutting.IoC/MappingProfile.cs`
- `BACKEND/PRPA/PRPA/Controllers/MovimentoEstoqueController.cs`
- `BACKEND/PRPA/App.Service/Services/EntradaDiretaSincronizacaoServices.cs`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1_RESULTADO_FINAL.md`
- `MES-ProjectBook/docs/05 - Estoque/Entrada/EST-OP-02C/EST-OP-02C.1D_RESULTADO_FINAL.md`
- `BACKEND/PRPA/App.Infra.Data/Persistence/Estoque/Consultas/ConsultaOperacionalEstoqueService.cs`
- `MES-ProjectBook/docs/12 - Decision Log/DL-0022 - Estoque como Dominio e Saldo como Projecao.md`
