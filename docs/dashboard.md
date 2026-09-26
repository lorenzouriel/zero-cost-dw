# Dashboard

The report is [Dashboard/fruit_juice.pbix](../Dashboard/fruit_juice.pbix), a Power BI Desktop file using a **live connection** to the SSAS cubes: no data is imported, every visual sends MDX queries to Analysis Services. Consequently it cannot open with data until an Analysis Services database is deployed and reachable (see [Getting started](getting-started.md)).

The background images used behind the report pages are in [Dashboard/img](../Dashboard/img).

## Pages

The report has three pages, navigated through the icon menu on the left (labels are in Portuguese).

### Menu — executive overview

![Overview page](../Dashboard/img/menu.png)

KPI cards (revenue target, revenue, tax, freight), performance by customer segment per year, top five products, quantity sold by category and brand, and fixed and variable cost trends.

### Gerência — management

![Management page](../Dashboard/img/gerencia.png)

Revenue and quantity sold per manager, split by product category (mineral water, mate, fruit juice), and a ranked bar chart of managers. Uses the parent-child organisation dimension.

### Localização — location

![Location page](../Dashboard/img/localizacao.png)

Revenue, quantity sold, tax and allocated freight per state and per customer, with a filled map of Brazilian states. `Frete Rateio` (allocated freight) exists only in the [`COMPLETO` cube](olap.md#the-complete-fact-table).

## Publishing to Power BI Service

Publishing a live-connection report to the free Power BI Service and refreshing it against an on-premises Analysis Services server requires an **on-premises data gateway**. The step-by-step (account creation, workspace, gateway configuration) is covered in section 5 of the [tutorial](tutorial.md).
