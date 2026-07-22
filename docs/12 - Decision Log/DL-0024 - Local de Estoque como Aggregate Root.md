# DL-0024 - Local de Estoque como Aggregate Root

## Codigo

DL-0024

## Status

Aprovado

## Contexto

A AS-0004 preserva a arquitetura de enderecamento aprovada na AS-0001 e formaliza LocalDeEstoque como Aggregate Root do dominio de Estoque.

LocalDeEstoque e LocalizacaoEstoque nao sao sinonimos. LocalDeEstoque representa o endereco ou espaco fisico governado pelo dominio. LocalizacaoEstoque representa a referencia do posicionamento atual de uma Unidade Logistica, referencia um LocalDeEstoque, nao e Aggregate Root concorrente e preserva compatibilidade conceitual com os documentos anteriores.

## Problema

Local de estoque possui identidade, hierarquia, capacidade, restricoes, compatibilidades e ciclo operacional proprios. Tratar local apenas como campo de saldo ou propriedade da UL enfraquece suas invariantes.

## Decisao

LocalDeEstoque sera Aggregate Root e tambem possuira caracteristicas de cadastro mestre.

O agregado deve proteger identidade do endereco, hierarquia fisica, tipo, estado operacional, capacidade, restricoes, compatibilidade com materiais, compatibilidade com tipos de UL, condicoes ambientais, bloqueio e desativacao.

O agregado nao devera armazenar uma colecao completa e ilimitada de todas as ULs existentes no local. Ocupacao e conteudo devem ser apresentados por projecoes ou consultas especializadas.

Invariantes proprias:

- local desativado nao recebe novas operacoes;
- local bloqueado nao recebe UL, salvo politica excepcional explicita;
- capacidade nao pode ser excedida;
- restricoes de material e tipo de UL devem ser respeitadas;
- hierarquia nao pode formar ciclos;
- um local nao pode ser descendente de si mesmo;
- alteracoes estruturais devem preservar rastreabilidade;
- ocupacao e consultada por projecao e nao por colecao ilimitada interna ao agregado.

## Alternativas

- Local como campo simples em saldo: rejeitado.
- Local como parte interna da UL: rejeitado.
- Local como Aggregate Root proprio com projecoes de ocupacao: aprovado.

## Consequencias

### Consequencias positivas

- Regras de capacidade e compatibilidade ficam protegidas.
- Consultas de ocupacao podem ser otimizadas por projecoes.
- Movimentacoes devem respeitar estado e restricoes do local.

### Consequencias negativas

- Consultas operacionais podem exigir projecoes bem definidas.
- Regras duplicadas entre local, UL e politicas precisam ser evitadas.

### Riscos e compromissos

- Consultas operacionais podem exigir projecoes bem definidas.
- Regras duplicadas entre local, UL e politicas precisam ser evitadas.
- A relacao com LocalizacaoEstoque deve permanecer explicita para evitar agregado concorrente.

## Impactos

- Enderecamento.
- Movimentacao.
- Reserva.
- Inventario.
- Consultas de ocupacao.

## Riscos

- Consultas operacionais podem exigir projecoes bem definidas.
- Regras duplicadas entre local, UL e politicas precisam ser evitadas.

## Pendencias

- Modelagem fisica de LocalDeEstoque.
- Estrategia futura de projecoes de ocupacao e conteudo.
- Modelagem futura de LocalizacaoEstoque como Value Object ou conceito equivalente.

## Relacao com AS-0004

Derivado das secoes 18, 31, 32, 37 e 40 da AS-0004.