# Architecture

![Architecture](assets/architecture.png)

## Layers

| Layer | Component | Implementation in this repo |
|---|---|---|
| **Data sources** | Excel workbooks, CSV files, a SQL Server source database | [Sources/Fontes](../Sources/README.md) |
| **Staging** | A `staging` schema is defined for auxiliary tables but is currently empty. The only working tables are `TEMP_AUXTABELA` / `TEMP_AUXCONTROLE`, used to build the organisation hierarchy | [fruit_juice/Schemas/staging.sql](../fruit_juice/Schemas/staging.sql) |
| **Orchestration / ETL** | SSIS packages run in dependency order: dimensions first, then facts | [ETL](../ETL/README.md) |
| **Data warehouse** | Star schema in SQL Server — `dim` and `fact` schemas | [fruit_juice](../fruit_juice/README.md) |
| **Data mart / OLAP** | SSAS Multidimensional cubes built on the warehouse | [OLAP](../OLAP/README.md) |
| **Presentation** | Power BI report using a live connection to the cubes | [Dashboard](../Dashboard/README.md) |

The diagram also shows an on-premises SSRS option in the presentation layer; this repository implements only the Power BI path.

## Data flow

```
Excel / CSV / source DB
        │   (SSIS: Carga Dimensões.dtsx, then Carga Fatos.dtsx)
        ▼
SQL Server  fruit_juice  ──  dim.*  +  fact.*
        │   (SSAS deploy + process)
        ▼
SSAS cubes: VENDAS · CUSTOS · PRESIDENCIA · COMPLETO
        │   (Power BI live connection via on-premises data gateway)
        ▼
Power BI report
```

## Design decisions

- **Star schema with two exceptions.** Product is a *snowflake* (`produto → marca → categoria`) and the organisation dimension is a *parent-child* hierarchy (`organizacional`), stored with left/right/level columns (nested-set model) so SSAS can navigate it efficiently.
- **Five fact tables at different grains** rather than one wide table, because costs, freight and targets are recorded at different levels of detail than sales. Cubes combine only the facts that share dimensions (see [OLAP](olap.md)).
- **A "complete fact" table** allocates the coarser-grain facts down to the sales grain proportionally to quantity sold, so every metric can be sliced by every dimension in one cube (`COMPLETO`). See [OLAP](olap.md#the-complete-fact-table).
- **Zero licence cost.** SQL Server Developer/Express, Visual Studio Community, Power BI Desktop and the Power BI free tier cover every tool used.

## Environments and naming

| Item | Value |
|---|---|
| Database name used by the database project, the dimension load and the SSAS data source | `fruit_juice` |
| Database name inside the shipped backup, and used by the `FONTES_DB` connection in the fact load | `DW_SUCOS` |
| SQL Server instance in every connection string | default local instance (`.`), Windows authentication |

Both names appear in the repository. See [Getting started](getting-started.md#known-gotchas) for how to reconcile them.
