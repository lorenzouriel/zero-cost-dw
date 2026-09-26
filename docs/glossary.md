# Glossary — Portuguese → English

Object names in the database, cubes, packages and dashboard are in Portuguese. This page maps them to English.

## Prefixes and common words

| Portuguese | English |
|---|---|
| `Cod_` | Code (key) |
| `Desc_` | Description (display name) |
| `Atr_` | Attribute |
| `Nome_` | Name |
| `Nivel` / `Nível` | Level |
| `Hierarquia` | Hierarchy |
| `Fato` | Fact |
| `Meta` | Target / goal |
| `Rateio` | Allocation (proportional apportionment) |
| `Carga` | Load |
| `Fonte(s)` | Source(s) |

## Tables

| Portuguese | English | Type |
|---|---|---|
| `dim.categoria` | Category | Dimension |
| `dim.marca` | Brand | Dimension |
| `dim.produto` | Product | Dimension |
| `dim.cliente` | Customer | Dimension |
| `dim.fabrica` | Factory | Dimension |
| `dim.organizacional` | Sales organisation (management hierarchy) | Dimension |
| `dim.tempo` | Time / calendar | Dimension |
| `fact.Fato_001` | Sales | Fact |
| `fact.Fato_002` | Freight | Fact |
| `fact.Fato_003` | Fixed cost | Fact |
| `fact.Fato_004` | Revenue target | Fact |
| `fact.Fato_005` | Cost target | Fact |

## Columns

| Portuguese | English |
|---|---|
| `Cod_Cidade` / `Desc_Cidade` | City code / name |
| `Cod_Estado` / `Desc_Estado` | State code / name |
| `Cod_Regiao` / `Desc_Regiao` | Region code / name |
| `Cod_Segmento` / `Desc_Segmento` | Customer segment code / name |
| `Atr_Tamanho` | Size |
| `Atr_Sabor` | Flavour |
| `Cod_Filho` / `Desc_Filho` | Child (member) code / name |
| `Cod_Pai` | Parent code |
| `Esquerda` / `Direita` | Left / right (nested-set bounds) |
| `Faturamento` | Revenue |
| `Imposto` | Tax |
| `Custo_Variavel` | Variable cost |
| `Custo_Fixo` | Fixed cost |
| `Quantidade_Vendida` | Quantity sold |
| `Unidades_Vendidas` | Units sold |
| `Frete` | Freight |
| `Meta_Faturamento` | Revenue target |
| `Meta_Custo` | Cost target |

### Time dimension columns

| Portuguese | English |
|---|---|
| `Cod_Dia` | Day key |
| `Data` | Date |
| `Semana` | Week |
| `Nome_Dia_Semana` | Weekday name |
| `Mes` / `Mes_Ano` | Month / month-year |
| `Trimestre` / `Trimestre_Ano` | Quarter / quarter-year |
| `Semestre` / `Semestre_Ano` | Half-year / half-year-year |
| `Ano` | Year |
| `Tipo_Dia` | Day type (*Dia Útil* = business day, *Fim de Semana* = weekend) |

## SSAS cubes

| Portuguese | English |
|---|---|
| `VENDAS` | Sales |
| `CUSTOS` | Costs |
| `PRESIDENCIA` | Presidency / executive |
| `COMPLETO` | Complete |
| `Fato Completa` | Complete fact (all metrics, allocated to the sales grain) |
| `Frete Rateio` | Allocated freight |
| `Custo Fixo Rateio` | Allocated fixed cost |
| `Meta Faturamento Rateio` | Allocated revenue target |
| `Meta Custo Rateio` | Allocated cost target |

## ETL and scripts

| Portuguese | English |
|---|---|
| `Carga Dimensões.dtsx` | Load Dimensions |
| `Carga Fatos.dtsx` | Load Facts |
| `Conexão com o DW` | Connection to the DW |
| `Criação da Dimensão …` | Creation of the … dimension |
| `Execução da rotina Esq Dir Nivel` | Run the left / right / level routine |
| `Transferência Esq Dir Nivel` | Transfer the left / right / level results |
| `SP_MONTAESQDIR` | Stored procedure "build left/right" (nested-set builder) |
| `SP_EXECUTAESQDIR` | Driver script for the procedure above |
| `Ano_Inicial` / `Ano_Final` | Start year / end year |
| `Mes_Inicial` / `Mes_Final` | Start month / end month |

## Business terms

| Portuguese | English |
|---|---|
| Sucos de Frutas | Fruit Juice (the company) |
| Águas Minerais | Mineral water |
| Mate | Mate (herbal tea) |
| Suco de Frutas | Fruit juice |
| Gerência / Gerente | Management / manager |
| Localização | Location |
| Farmácias, Lanchonetes, Lojas de Conveniência, Supermercados | Pharmacies, snack bars, convenience stores, supermarkets (customer segments) |

## Source files (`Sources/Fontes`)

| Portuguese | English |
|---|---|
| `CADASTRO DE CLIENTES.xlsx` | Customer register |
| `FUNCIONARIOS GRUPO 1 / 2.xlsx` | Employees, group 1 / 2 |
| `PRODUTOS.xlsx` | Products |
| `MARCAS E CATEGORIAS.csv` | Brands and categories |
| `REGIOES DOS ESTADOS.csv` | Regions of the states |
| `PERIODOS DE TEMPO.sql` | Time periods (calendar generation) |
| `Consulta Fabricas.txt` | Factories query |
| `Consultas Fatos Fonte de Dados.txt` | Fact queries against the source data |
| `Funções de Extração de Cidade e Estado.txt` | City and state extraction expressions |
| `Funções de Extração de Marca, Sabor e Tamanho.txt` | Brand, flavour and size extraction expressions |
