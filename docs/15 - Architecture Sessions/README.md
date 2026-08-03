# Architecture Sessions

Esta pasta registra sessoes de arquitetura realizadas para o projeto MES/MOM.

## Objetivo

Uma Architecture Session consolida o contexto, os problemas analisados, alternativas avaliadas, decisoes aprovadas, justificativas, riscos, impactos e pendencias futuras de uma discussao arquitetural.

## Regras de Uso

- Registrar sessoes somente com base em decisoes aprovadas ou contexto explicitamente validado.
- Nao transformar hipoteses em decisoes.
- Nao criar entidades, campos, migrations, endpoints ou telas sem aprovacao especifica.
- Encaminhar decisoes objetivas para `MES-ProjectBook/docs/12 - Decision Log/`.
- Registrar pendencias tecnicas quando a implementacao depender de modelagem futura.

## Sessoes Registradas

| ID | Titulo | Data | Status | Decision Logs Relacionados |
|---|---|---|---|---|
| AS-0001 | Arquitetura de Enderecamento e Localizacao de Estoque | 2026-07-15 | Concluida | DL-0001, DL-0002, DL-0003, DL-0004, DL-0005 |
| AS-0002 | Movimentacoes de Estoque e Operacao Assistida | 2026-07-16 | Concluida | DL-0006, DL-0007, DL-0008, DL-0009, DL-0010, DL-0011 |
| AS-0003 | Arquitetura de Integracao, Sincronizacao, Governanca e Eventos | 2026-07-17 | Concluida | DL-0012, DL-0013, DL-0014, DL-0015, DL-0016, DL-0017, DL-0018, DL-0019, DL-0020, DL-0021 |
| AS-0004 | Arquitetura do Dominio de Estoque | 2026-07-22 | Concluida | DL-0022, DL-0023, DL-0024, DL-0025, DL-0026, DL-0027, DL-0028, DL-0029 |
| AS-0005 | Arquitetura da Primeira Vertical Funcional do Dominio de Estoque | 2026-07-24 | Concluida | DL-0030, DL-0031, DL-0032 |
| AS-0006 | Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque | 2026-07-24 | Concluida | Nao gera Decision Log |
| AS-0007 | Arquitetura Tecnica da Primeira Vertical Funcional de Estoque | 2026-07-24 | Concluida | DL-0033, DL-0034, DL-0035, DL-0036, DL-0037 |
| AS-0008 | Finalidade Configurada e Classificacao Efetiva dos Locais de Estoque Legados | 2026-07-31 | Concluida | DL-0042 |
| AS-0009 | Modelo de Planta, Armazem e Estrutura Fisica Industrial | 2026-08-03 | Concluida | DL-0043 |
| AS-0010 | Unificacao da Identidade Fisica e Operacional dos Locais de Estoque | 2026-08-03 | Concluida | DL-0044 |

Proxima etapa planejada:

Planejar a refatoracao tecnica para usar `LocalizacaoEstoque` como identidade unica, preservando idempotencia, concorrencia, reservas e outbox, sem remover `CLOCALDEESTOQUE` antes da migracao controlada.

## Arquivos

- `AS-0001 - Arquitetura de Enderecamento e Localizacao de Estoque.md`
- `AS-0002 - Movimentacoes de Estoque e Operacao Assistida.md`
- `AS-0003 - Arquitetura de Integracao Sincronizacao Governanca e Eventos.md`
- `AS-0004 - Arquitetura do Dominio de Estoque.md`
- `AS-0005 - Arquitetura da Primeira Vertical Funcional do Dominio de Estoque.md`
- `AS-0006 - Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque.md`
- `AS-0007 - Arquitetura Tecnica da Primeira Vertical Funcional de Estoque.md`
- `AS-0008 - Finalidade Configurada e Classificacao Efetiva dos Locais de Estoque Legados.md`


- `AS-0009 - Modelo de Planta Armazem e Estrutura Fisica Industrial.md`
- `AS-0010 - Unificacao da Identidade Fisica e Operacional dos Locais de Estoque.md`
