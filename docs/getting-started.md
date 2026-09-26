# Getting started

This guide takes you from a fresh clone to a running cube and dashboard. Budget about an hour; most of it is installing SQL Server.

> This project was written for Visual Studio 2019 and SQL Server 2016+ on Windows. Nothing here has been re-verified on newer tool versions.

## 1. Prerequisites

| Need | Notes |
|---|---|
| **SQL Server** (Developer or Express) | Install the *Database Engine*. Integration Services and Analysis Services are only available in Developer/Standard/Enterprise, not Express |
| **Analysis Services in Multidimensional mode** | Choose *Multidimensional and Data Mining* during setup; Tabular mode will not host these cubes |
| **SQL Server Management Studio** | To restore the backup and run queries |
| **Visual Studio 2019** with the extensions *SQL Server Data Tools*, *SQL Server Integration Services Projects* and *Analysis Services Projects* | Community edition is enough |
| **Microsoft Access Database Engine (ACE OLEDB 12.0)** | Needed by SSIS to read the `.xlsx` sources. The original download link is kept in [Sources/Fontes/queries/Excel Componente.txt](../Sources/Fontes/queries/Excel%20Componente.txt) |
| **Power BI Desktop** | Free |

## 2. Clone and open

```bash
git clone https://github.com/lorenzouriel/zero-cost-dw.git
cd zero-cost-dw
```

Open `fruit_juice.sln`. It contains three projects: `fruit_juice` (database), `ETL` (SSIS) and `OLAP` (SSAS).

## 3. Create the databases

Two databases are involved (see [Architecture](architecture.md#environments-and-naming)).

1. **`DW_SUCOS` — the source of the fact load.** Unzip [Sources/FULL.zip](../Sources/FULL.zip) and restore `FULL/DW_SUCOS_FULL.bak` in SSMS as a database named `DW_SUCOS`.
2. **`fruit_juice` — the warehouse.** In Visual Studio, right-click the `fruit_juice` project → **Publish**, set the target database name to `fruit_juice` and the server to `.` (or your instance), and publish. This creates the `dim`, `fact` and `staging` schemas and all tables.

## 4. Repoint the SSIS file connections

The Excel and CSV connection managers contain absolute paths from the original author's machine. In the `ETL` project, open `Carga Dimensões.dtsx` and, for **each** of the four *Excel Connection Manager* and two *Flat File Connection Manager* entries in the Connection Managers tray, set the path to the matching file in your clone's `Sources/Fontes/` folder.

| Connection | File |
|---|---|
| Excel Connection Manager | `CADASTRO DE CLIENTES.xlsx` |
| Excel Connection Manager 1 / 2 / 3 | `FUNCIONARIOS GRUPO 1.xlsx`, `FUNCIONARIOS GRUPO 2.xlsx`, `PRODUTOS.xlsx` (check each one's current path to see which is which) |
| Flat File Connection Manager / 1 | `MARCAS E CATEGORIAS.csv`, `REGIOES DOS ESTADOS.csv` (again, check each one's current path to see which is which) |

Also check the two OLE DB connections (`Conexão com o DW`, and `Conexão com FONTES_DB` in `Carga Fatos.dtsx`) point at your SQL Server instance rather than `.` if yours is named.

## 5. Choose the period

In `Carga Dimensões.dtsx`, the parameters `Ano_Inicial`, `Mes_Inicial`, `Ano_Final`, `Mes_Final` default to **Jan 2014 – Dec 2014**. The dimension `dim.tempo` is generated for that range only. The dashboard screenshots show data for 2013–2015, so if you load the full backup, widen the range to cover every date in the facts. A fact row whose `Cod_Dia` is not in `dim.tempo` violates a foreign key and the fact load fails.

## 6. Run the ETL

1. Run **`Carga Dimensões.dtsx`** (right-click → Execute Package). Check all tasks turn green.
2. Run **`Carga Fatos.dtsx`**.

Verify in SSMS:

```sql
SELECT 'dim.cliente' AS tbl, COUNT(*) AS n FROM fruit_juice.dim.cliente
UNION ALL SELECT 'dim.tempo',   COUNT(*) FROM fruit_juice.dim.tempo
UNION ALL SELECT 'fact.Fato_001', COUNT(*) FROM fruit_juice.fact.Fato_001;
```

## 7. Deploy the cubes

1. In the `OLAP` project properties → **Deployment**, set **Server** to your Analysis Services instance.
2. Open `Fruit Juice.ds` and confirm it connects to the SQL Server holding `fruit_juice`. Under **Impersonation Information** use the *service account* option (or an account that can read `fruit_juice`).
3. **Build → Deploy Solution**. Deployment also processes the database.
4. Browse a cube in SSMS or Visual Studio to check numbers appear.

## 8. Open the dashboard

Open [Dashboard/fruit_juice.pbix](../Dashboard/fruit_juice.pbix) in Power BI Desktop. It is a live connection, so point it at your server: **Transform data → Data source settings → Change source**, enter your Analysis Services server and database name. Then refresh.

To publish and share it, see the gateway section of the [tutorial](tutorial.md).

## Known gotchas

| Symptom | Cause and fix |
|---|---|
| *"The 'Microsoft.ACE.OLEDB.12.0' provider is not registered"* | Install the Access Database Engine; if Visual Studio is 32-bit and the engine 64-bit (or vice versa), set the project's **Run64BitRuntime** debug option to match |
| Excel/CSV tasks fail with "file not found" | Step 4 — connection paths still point at the original author's `C:\0 - Personal\…` folder |
| Fact load fails on a foreign key to `dim.tempo` | Step 5 — period does not cover the fact dates |
| Fact load fails "Invalid object name `DW_SUCOS.dbo.Fato_001`" | Step 3 — restore the backup under the name `DW_SUCOS`. If the tables in the backup live in a different schema, edit the source query in each `Carga Fato n` task |
| Re-running a package fails with primary-key violations | The packages insert only. Truncate the target tables first (facts, then dimensions) |
| Cube processing fails with a login error | The Analysis Services service account cannot read `fruit_juice`; grant it `db_datareader` or change the data-source impersonation |
| Backup restores but the tables are not where the cubes expect them | Not verified: the backup's own scripts reference `[DW_SUCOS].[dbo].[Fato_001]`, i.e. the `dbo` schema, whereas the current project uses `fact.Fato_001`. Use the backup as the ETL *source*, not as a drop-in replacement for `fruit_juice` |
