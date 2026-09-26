# OLAP — SSAS Multidimensional project

Analysis Services project that builds cubes on top of the `fruit_juice` warehouse.

| Item | Files |
|---|---|
| Cubes | `VENDAS.cube` (sales), `CUSTOS.cube` (costs), `PRESIDENCIA.cube` (executive, all facts), `COMPLETO.cube` (all metrics allocated to the sales grain) |
| Dimensions | `Tempo.dim`, `Cliente.dim`, `Produto.dim`, `Fabrica.dim`, `Organizacional.dim` |
| Data source / view | `Fruit Juice.ds`, `Fruit Juice.dsv` (includes the extra **Fato Completa** table) |
| Partitions | `*.partitions`, one per cube |

Full documentation: **[OLAP](../docs/olap.md)** — cubes, measures, hierarchies, the allocation logic, deployment.

Object names are in Portuguese; see the [glossary](../docs/glossary.md).
