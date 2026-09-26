# Sources

Raw inputs for the ETL, helper scripts and a database backup.

| Path | Contents |
|---|---|
| `Fontes/` | Data files provided by the company: `CADASTRO DE CLIENTES.xlsx` (customers), `PRODUTOS.xlsx` (products), `FUNCIONARIOS GRUPO 1.xlsx` / `GRUPO 2.xlsx` (employees, feed the organisation hierarchy), `MARCAS E CATEGORIAS.csv` (brand → category), `REGIOES DOS ESTADOS.csv` (state → region) |
| `Fontes/queries/` | SQL and expression snippets used by the ETL: calendar generator (`PERIODOS DE TEMPO.sql`), hierarchy builder (`SP_MONTAESQDIR.sql`, `SP_EXECUTAESQDIR.sql`), text-parsing expressions, factory query, fact queries. Some are older copies of logic that now lives inside the SSIS packages, see [ETL](../docs/etl.md) |
| `FULL.zip` | `FULL/DW_SUCOS_FULL.bak` — full backup of the populated database `DW_SUCOS` — and `FULL/Rateio.sql`, the allocation query behind the "complete fact" table |

The `Fontes` folder name is kept as-is because the SSIS connection managers were built against that path; renaming it means repointing them. The `.csv` files are semicolon-delimited.

See the [glossary](../docs/glossary.md#source-files-sourcesfontes) for English names of the files, and [Getting started](../docs/getting-started.md) for how to restore the backup.
