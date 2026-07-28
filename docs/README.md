# Project Book MES/MOM

Este diretorio concentra a documentacao oficial do produto MES/MOM.

## Indice Principal

- `01 - Visão Geral do Projeto/`
- `02 - Arquitetura do Sistema/`
- `03 - Modelo de Dados/`
- `04 - Cadastros Mestres/`
- `05 - Estoque/`
- `06 - Produção (MES)/`
- `07 - Qualidade/`
- `08 - Rastreabilidade/`
- `09 - OEE e Indicadores/`
- `10 - Integrações/`
- `11 - Roadmap/`
- `12 - Decision Log/`
- `13 - Backlog/`
- `14 - Padrões de Desenvolvimento/`
- `15 - Architecture Sessions/`
- `20 - Glossario Arquitetural/`

## Documentos de Governanca Arquitetural

- `11 - Roadmap/Roadmap Geral.md`
- `12 - Decision Log/README.md`
- `15 - Architecture Sessions/README.md`
- `20 - Glossario Arquitetural/Glossario Arquitetural do MES.md`

## Status das Architecture Sessions

| AS | Titulo | Status | Principais DLs |
|---|---|---|---|
| AS-0001 | Arquitetura de Enderecamento e Localizacao de Estoque | Concluida | DL-0001 a DL-0005 |
| AS-0002 | Movimentacoes de Estoque e Operacao Assistida | Concluida | DL-0006 a DL-0011 |
| AS-0003 | Arquitetura de Integracao, Sincronizacao, Governanca e Eventos | Concluida | DL-0012 a DL-0021 |
| AS-0004 | Arquitetura do Dominio de Estoque | Concluida | DL-0022 a DL-0029 |
| AS-0005 | Arquitetura da Primeira Vertical Funcional do Dominio de Estoque | Concluida | DL-0030 a DL-0032 |
| AS-0006 | Avaliacao de Prontidao Tecnica para Implementacao da Primeira Vertical de Estoque | Concluida | Nao gera DL |
| AS-0007 | Arquitetura Tecnica da Primeira Vertical Funcional de Estoque | Concluida | DL-0033 a DL-0037 |

Proxima etapa planejada:

Implementar o primeiro incremento de codigo recomendado pela AS-0007: nucleo de dominio de UnidadeLogistica e MovimentacaoDeEstoque com testes unitarios, sem API publica e sem migrations.

