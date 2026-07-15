# Frontend

Este documento registra apenas padroes realmente observados no codigo do FRONTEND. Divergencias sao registradas como divergencias, nao como regras oficiais.

Legenda: **Confirmado** = encontrado em arquivo de codigo; **Provavel** = indicado por nome/campo, mas sem comportamento completo confirmado; **Nao identificado** = nao encontrado no codigo analisado.

## Tecnologias observadas

| Item | Evidencia | Classificacao |
|---|---|---|
| Angular 18.2.x. | `FRONTEND/package.json`; `FRONTEND/angular.json` | Confirmado |
| TypeScript 5.5.x, RxJS 7.8.x e Zone.js. | `FRONTEND/package.json` | Confirmado |
| Angular Material 18.2.x. | `FRONTEND/package.json` | Confirmado |
| PrimeNG 17.18.x, PrimeFlex e PrimeIcons. | `FRONTEND/package.json` | Confirmado |
| Syncfusion Angular 28.2.x. | `FRONTEND/package.json`; estilos globais em `FRONTEND/angular.json` | Confirmado |
| Bootstrap 5.2.3 e Chart.js 4.3.0. | `FRONTEND/package.json` | Confirmado |
| Projeto Angular chamado `prpa`, com output em `dist/aguavivasports`. | `FRONTEND/angular.json` | Confirmado |

## Organizacao de pastas e modulos

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Funcionalidades de cadastro ficam em `src/app/application/cadastro`. | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; subpastas de cadastro | Confirmado |
| Operacoes ficam em `src/app/application/operacao`. | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |
| Recursos de autenticacao, menu e token ficam em `src/app/core`. | `FRONTEND/src/app/core/authentication/services/authentication.service.ts`; `FRONTEND/src/app/core/menu/services/menu.service.ts`; `FRONTEND/src/app/core/token/token.interceptor.ts` | Confirmado |
| Cadastros costumam ter subpastas `models`, `services` e `components/list...`/`components/cad...`. | `FRONTEND/src/app/application/cadastro/almoxarifado/*`; `areaestoque/*`; `lotematerial/*` | Confirmado |
| Rotas de cadastro usam prefixo `list...` para telas de listagem. | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts` | Confirmado |
| Rotas de operacao usam nomes diretos como `entradaestoque`, `transferenciaestoque`, `ajusteestoque`. | `FRONTEND/src/app/application/operacao/operacao-routing.module.ts` | Confirmado |

## Services e APIs

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Services Angular usam `HttpClient` e URLs compostas com `environment.urlserver`. | `FRONTEND/src/app/application/cadastro/**/services/*.ts`; `FRONTEND/src/app/core/authentication/services/authentication.service.ts` | Confirmado |
| Ambientes separam `environment.ts` e `environment.prod.ts`. | `FRONTEND/src/environments/environment.ts`; `environment.prod.ts` | Confirmado |
| Dev usa `http://localhost:5046/api/`; producao usa `https://back.techforyou.com.br/api/`. | `FRONTEND/src/environments/environment.ts`; `environment.prod.ts` | Confirmado |
| Interceptor JWT e registrado na aplicacao. | `FRONTEND/src/app/core/token/token.interceptor.ts`; `FRONTEND/src/app/app.module.ts` | Confirmado |
| Alguns services normalizam diferencas entre camelCase e PascalCase vindas da API. | `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts` | Confirmado |

## Interface e estilos

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| SCSS e usado como estilo padrao do projeto. | `FRONTEND/angular.json` | Confirmado |
| Estilos globais incluem `src/styles.scss` e tema Material da Syncfusion. | `FRONTEND/angular.json` | Confirmado |
| Bibliotecas visuais coexistem: Angular Material, PrimeNG, Syncfusion e Bootstrap. | `FRONTEND/package.json` | Confirmado |
| Um unico design system dominante nao foi identificado. | Dependencias e estrutura de componentes em `FRONTEND/package.json` e `FRONTEND/src/app/application` | Nao identificado |

## Nomenclatura observada

| Padrao observado | Evidencia | Classificacao |
|---|---|---|
| Pastas de features aparecem em minusculo e, em muitos casos, sem hifen: `almoxarifado`, `localizacaoestoque`, `roteiroproducao`. | `FRONTEND/src/app/application/cadastro/*` | Confirmado |
| Componentes de cadastro usam prefixos `list` e `cad` no nome da pasta/componente. | `FRONTEND/src/app/application/cadastro/almoxarifado/components/*`; `lotematerial/components/*` | Confirmado |
| Nomes de endpoints nos services variam entre PascalCase (`Almoxarifado`) e kebab-case (`area-estoque`, `roteiros-producao`). | `FRONTEND/src/app/application/cadastro/almoxarifado/services/almoxarifado.service.ts`; `areaestoque/services/areaestoque.service.ts`; `roteiroproducao/services/roteiro-producao.service.ts` | Confirmado |

## Divergencias registradas

| Divergencia | Evidencia | Classificacao |
|---|---|---|
| O outputPath `dist/aguavivasports` nao coincide semanticamente com o nome do projeto Angular `prpa`. | `FRONTEND/angular.json` | Confirmado |
| `environment.ts` usa porta `5046`, enquanto `proxy.conf.json` aponta para `https://localhost:7137`. | `FRONTEND/src/environments/environment.ts`; `FRONTEND/proxy.conf.json` | Confirmado |
| Rotas/endpoints misturam PascalCase e kebab-case. | Services em `FRONTEND/src/app/application/cadastro/**/services/*.ts` | Confirmado |
| Existem rotas de cadastro no frontend para as quais o backend correspondente nao foi identificado nesta leitura. | `FRONTEND/src/app/application/cadastro/cadastro-routing.module.ts`; controllers em `BACKEND/PRPA/PRPA/Controllers` | Confirmado |

## Nao identificado

| Item | Evidencia | Classificacao |
|---|---|---|
| Padrao formal de tratamento visual de erros por componente. | Nao foi consolidado um padrao unico nos arquivos analisados. | Nao identificado |
| Guia visual unico entre Material, PrimeNG, Syncfusion e Bootstrap. | Dependencias coexistem em `FRONTEND/package.json`. | Nao identificado |
