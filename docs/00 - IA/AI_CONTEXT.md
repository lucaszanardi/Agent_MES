# DL-0001 - Arquitetura de Endereçamento de Estoque

## Status

Aprovado

## Data

2026-07-15

## Contexto

O sistema MES/MOM deve suportar múltiplos armazéns e diferentes formas de organização física de estoque.

Foi identificado que:

- um mesmo armazém pode possuir áreas com estruturas de armazenagem distintas;
- uma área de big bags pode utilizar apenas Rua e Bloco;
- uma área paletizada pode utilizar Rua, Coluna, Nível e Posição;
- uma área de líquidos pode utilizar apenas Tanque;
- uma área de piso pode utilizar Setor e Posição;
- os operadores devem conseguir identificar e movimentar materiais com segurança;
- os caminhos das localizações devem permitir identificação inequívoca e leitura por QR Code.

## Problema

Uma hierarquia global única para todos os armazéns e áreas não atende à variedade de estruturas físicas existentes em ambientes industriais.

Também é necessário garantir:

- flexibilidade por área de estoque;
- unicidade operacional das localizações;
- rastreabilidade;
- prevenção de ambiguidades;
- suporte futuro a leitura por QR Code ou código de barras.

## Decisão

### 1. Catálogo global de tipos de localização

`TipoLocalizacao` será mantido como um catálogo global reutilizável.

Exemplos:

- Rua;
- Bloco;
- Coluna;
- Nível;
- Posição;
- Tanque;
- Setor;
- Armário;
- Prateleira;
- Doca;
- Pulmão.

O catálogo define o significado do tipo, mas não define sozinho a estrutura de um armazém.

### 2. Estrutura configurável por área de estoque

Cada `AreaEstoque` poderá definir sua própria estrutura hierárquica de localizações.

Exemplo:

```text
Armazém de Matérias-Primas

├── Área de Big Bags
│   └── Rua > Bloco
│
├── Área Paletizada
│   └── Rua > Coluna > Nível > Posição
│
├── Área de Líquidos
│   └── Tanque
│
└── Área de Piso
    └── Setor > Posição