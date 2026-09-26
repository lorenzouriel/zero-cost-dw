# ETL — SSIS project

Two SSIS packages load the warehouse. Run them in this order.

| Package | English name | Loads |
|---|---|---|
| `Carga Dimensões.dtsx` | Load Dimensions | The seven `dim.*` tables from Excel, CSV and inline queries |
| `Carga Fatos.dtsx` | Load Facts | `fact.Fato_001` … `fact.Fato_005` from `DW_SUCOS.dbo.Fato_00n` |

Full documentation: **[ETL](../docs/etl.md)** — task flow, parameters, connections, limitations.

> Before running: the Excel and CSV connection managers use absolute paths from the original author's machine. Repoint them to [Sources/Fontes](../Sources/Fontes) first. See [Getting started](../docs/getting-started.md#4-repoint-the-ssis-file-connections).
