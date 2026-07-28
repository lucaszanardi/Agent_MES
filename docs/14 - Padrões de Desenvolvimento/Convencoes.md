# Convencoes de Estrutura e Organizacao do Codigo

Este documento registra o padrao organizacional obrigatorio para implementacoes no projeto MES. O padrao existente do repositorio prevalece sobre preferencias pessoais, padroes genericos ou sugestoes automaticas.

## Regra permanente

Antes de qualquer codificacao, seguir a sequencia:

INSPECIONAR -> CLASSIFICAR -> LOCALIZAR -> CODIFICAR -> VALIDAR

Nunca inverter para codificar primeiro e organizar depois.

## Estrutura por projeto

| Projeto | Responsabilidade | Pastas obrigatorias |
|---|---|---|
| `PRPA` | API HTTP | `Controllers`, `Context`, `Auth`, `Helpers` |
| `App.Service` | Orquestracao de aplicacao, DTOs e validators | `Services`, `DTOs`, `Validators` |
| `App.Domain` | Entidades, agregados, Value Objects, eventos, invariantes e contratos | `Entities`, `Interfaces` e subpastas do dominio |
| `App.Infra.Data` | EF Core, repositories concretos, persistencia tecnica e migrations | `Repository`, `Persistence`, `Mapping`, `Context`, `Migrations` |
| `App.Domain.Tests` | Harness provisorio e testes de dominio/aplicacao/modelo | raiz do projeto de testes ou subpastas por modulo quando houver padrao |

## Responsabilidades por camada

- Controllers ficam em `PRPA/Controllers` e apenas recebem requisicoes HTTP, validam formato basico, chamam services e convertem resultado em HTTP.
- Services ficam em `App.Service/Services` e orquestram casos de uso, repositories, dominio, Unit of Work, idempotencia e resultados de aplicacao.
- DTOs ficam em `App.Service/DTOs`.
- Validators ficam em `App.Service/Validators`.
- Repositories concretos ficam em `App.Infra.Data/Repository`; quando houver modulo, usar `App.Infra.Data/Repository/<Modulo>`.
- Infraestrutura tecnica de persistencia fica em `App.Infra.Data/Persistence`; exemplos: Unit of Work, idempotencia, outbox, coletores transacionais, availability checkers e tradutores tecnicos de erro.
- Mappings EF Core ficam em `App.Infra.Data/Mapping`; mappings por modulo ficam em `App.Infra.Data/Mapping/<Modulo>`.
- DbContext, factories e componentes diretamente ligados ao contexto ficam em `App.Infra.Data/Context`.
- Migrations, designers e snapshots ficam em `App.Infra.Data/Migrations`.
- Regras invariantes permanecem no dominio e nao devem ser movidas para services, controllers ou repositories.

## Padrao de namespaces

O namespace deve acompanhar a pasta fisica, salvo excecao legada documentada.

Exemplos:

- `App.Infra.Data.Repository.Estoque`
- `App.Infra.Data.Persistence.Estoque`
- `App.Infra.Data.Mapping.Estoque`
- `App.Infra.Data.Context`
- `App.Service.Services.Estoque`
- `App.Service.DTOs.Estoque`
- `App.Service.Validators.Estoque`
- `PRPA.Controllers`

## Proibicoes

- Nao criar estruturas paralelas quando uma pasta destinada a responsabilidade ja existir.
- Nao criar repositories concretos fora de `App.Infra.Data/Repository`.
- Nao criar componentes tecnicos de persistencia fora de `App.Infra.Data/Persistence`.
- Nao criar services fora de `App.Service/Services`.
- Nao criar DTOs dentro de `Services`.
- Nao criar validators dentro de `Services` ou `Controllers`.
- Nao mover regras de dominio para camada de aplicacao ou infraestrutura.
- Nao reorganizar arquitetura por iniciativa propria sem demanda explicita.

## Checklist obrigatorio antes de codificar

- Localizar arquivo semelhante.
- Confirmar projeto correto.
- Confirmar pasta correta.
- Confirmar responsabilidade arquitetural.
- Confirmar namespace.
- Confirmar nomenclatura.
- Verificar DI e referencias.
- Somente entao codificar.

## Checklist obrigatorio antes de concluir

- Pesquisar namespaces antigos.
- Verificar imports obsoletos.
- Confirmar que nao ha arquivos duplicados ou abandonados.
- Confirmar que migrations nao foram regeneradas sem autorizacao.
- Executar build e testes exigidos pela tarefa.
- Registrar pendencias quando houver violacao que exija mudanca funcional ampla.