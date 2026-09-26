# ETL

The ETL is an SSIS project ([ETL/](../ETL/)) with two packages. Run them **in this order**: dimensions first, because every fact table has foreign keys to them.

| Package | Loads | Target tables |
|---|---|---|
| `Carga Dimensões.dtsx` ("Load Dimensions") | All seven dimensions | `dim.*` |
| `Carga Fatos.dtsx` ("Load Facts") | All five fact tables | `fact.Fato_001` … `fact.Fato_005` |

## Parameters

Both packages declare the same four package parameters, but they are only **used by the dimension package**, where they set the date range generated for `dim.tempo`.

| Parameter | `Carga Dimensões` default | `Carga Fatos` default | Meaning |
|---|---|---|---|
| `Ano_Inicial` | 2014 | 2014 | Start year |
| `Mes_Inicial` | 1 | 1 | Start month |
| `Ano_Final` | 2014 | 2014 | End year |
| `Mes_Final` | 12 | 2 | End month |

The fact package's source queries have no `WHERE` clause (see below), so its parameters currently have no effect. To generate a longer calendar, change the dimension package's parameters. The time dimension must cover every date present in the facts, otherwise the `Cod_Dia` foreign keys fail.

## Connections

| Connection manager | Type | Points at |
|---|---|---|
| `Conexão com o DW` | OLE DB | SQL Server `.`, database `fruit_juice`, Windows auth |
| `Conexão com FONTES_DB` | OLE DB | SQL Server `.`, database `DW_SUCOS`, Windows auth — the *source* of the fact load (fact package only) |
| Excel Connection Managers (×4) | ACE OLEDB 12.0 | `CADASTRO DE CLIENTES.xlsx`, `FUNCIONARIOS GRUPO 1.xlsx`, `FUNCIONARIOS GRUPO 2.xlsx`, `PRODUTOS.xlsx` |
| Flat File Connection Managers (×2) | Delimited file | `MARCAS E CATEGORIAS.csv`, `REGIOES DOS ESTADOS.csv` |

> **The Excel and CSV paths are absolute and hard-coded to the original author's machine** (`C:\0 - Personal\portfolio\projects\5\Sources\Fontes\…`). You must repoint each connection manager to your clone's [Sources/Fontes](../Sources/Fontes) folder before running. See [Getting started](getting-started.md).

## Dimension package flow

Tasks with no arrow between them have no dependency on each other.

```
Cliente        Criação da Dimensão Cliente
Fábrica        Criação da Dimensão Fábrica
Tempo          Criação da Dimensão Tempo

Produto        Nível Categoria ──▶ Nível Marca ──▶ Nível Produto

Organizacional Tab 1 Niv 1 ──▶ Tab 1 Niv 2 ──▶ Tab 1 Niv 3 ──▶ Tab 2 Niv 1 ──▶ Tab 2 Niv 2
                                  ──▶ Execução da rotina Esq Dir Nivel
                                  ──▶ Transferência Esq Dir Nivel
```

| Dimension | Source | Transformations |
|---|---|---|
| `dim.cliente` | `CADASTRO DE CLIENTES.xlsx`, `REGIOES DOS ESTADOS.csv` | City and state are split out of combined text columns with SSIS string expressions ([expressions used](../Sources/Fontes/queries/Funções%20de%20Extração%20de%20Cidade%20e%20Estado.txt)); state abbreviation is joined to the region file |
| `dim.fabrica` | Inline `SELECT … UNION` query inside the package (three static rows: Rio de Janeiro, São Paulo, Acre). [Consulta Fabricas.txt](../Sources/Fontes/queries/Consulta%20Fabricas.txt) is an older copy with only two rows | None |
| `dim.tempo` | T-SQL script [PERIODOS DE TEMPO.sql](../Sources/Fontes/queries/PERIODOS%20DE%20TEMPO.sql) | One row per day between the start and end period, with week, month, quarter, half-year and weekday/weekend attributes |
| `dim.categoria` → `dim.marca` → `dim.produto` | `MARCAS E CATEGORIAS.csv`, `PRODUTOS.xlsx` | Loaded top-down so foreign keys resolve. Brand, flavour and size are split out of description columns ([expressions used](../Sources/Fontes/queries/Funções%20de%20Extração%20de%20Marca,%20Sabor%20e%20Tamanho.txt)) |
| `dim.organizacional` | `FUNCIONARIOS GRUPO 1.xlsx`, `FUNCIONARIOS GRUPO 2.xlsx` | Loaded level by level (parents before children), then the nested-set columns are calculated by [`SP_MONTAESQDIR`](data-model.md#stored-procedure) and written back |

## Fact package flow

Five data-flow tasks run **sequentially**: `Carga Fato 1` → `2` → `3` → `4` → `5`.

Each one is an OLE DB source → OLE DB destination copy. The source query is a full-table read from the source connection (`FONTES_DB`, database `DW_SUCOS`):

```sql
SELECT * FROM [DW_SUCOS].[dbo].[Fato_001]   -- likewise Fato_002 … Fato_005
```

and the destination is the matching `fact.Fato_00n` table in `fruit_juice`. In practice the source database is the one restored from the backup in [Sources/FULL.zip](../Sources/FULL.zip) under the name `DW_SUCOS` (see [Getting started](getting-started.md)).

The text file [Consultas Fatos Fonte de Dados.txt](../Sources/Fontes/queries/Consultas%20Fatos%20Fonte%20de%20Dados.txt) holds an earlier, period-filtered form of these queries against tables named `TAB_FATO001` … `TAB_FATO005`. It is kept for reference and is **not** what the package runs today.

## Re-running

Neither package contains a `TRUNCATE` or `DELETE` step; they only insert. Re-running against tables that already hold data is expected to fail on primary-key violations. Empty the target tables first (facts before dimensions, because of the foreign keys) or restore the backup.

## Known limitations

- Absolute file paths for the Excel and CSV connections (above).
- Full reload only; no incremental or upsert logic.
- The fact load copies every row of the source tables regardless of the period parameters.
- The dimension load is bound to the `fruit_juice` catalogue and the fact load to `DW_SUCOS` (source) → `fruit_juice` (target); both names must exist on the server.
- Package protection level is `EncryptSensitiveWithUserKey`. No passwords are stored (Windows authentication), but Visual Studio may prompt about it under a different Windows account.
