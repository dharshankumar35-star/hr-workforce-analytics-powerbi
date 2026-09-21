# HR Workforce Analytics — Power BI Dashboard

A workforce headcount and attrition analytics project built on a star-schema data model,
SQL-based ETL, Power Query, and Power BI with DAX time intelligence and Row-Level Security.

## Dashboard preview

### Executive Overview
![Executive Overview](docs/01_executive_overview.png)

### Attrition Deep Dive
![Attrition Deep Dive](docs/02_attrition_deep_dive.png)

### Regional Manager View
![Regional Manager View](docs/03_regional_manager_view.png)

## Data model

![Data model](docs/04_data_model.png)

- `FactHeadcount`: monthly snapshot grain (one row per employee per active month),
  enabling headcount trends, YoY comparisons, and rolling averages
- `DimEmployee`, `DimDepartment`, `DimGeography`, `DimDate`: dimension tables, all
  related to the fact table one-to-many. `DimDate` is marked as the official Date Table.

## Stack

| Layer | Tool |
|---|---|
| Source data | Synthetic HR dataset (~14.5K monthly fact rows) |
| ETL | SQL (staging → cleaned star schema), see `/sql` |
| Transformation | Power Query M, see `/powerquery` |
| Modeling & measures | Power BI, DAX, see `/dax` |
| Security | Row-Level Security by region, see `/docs/row_level_security.md` |

## DAX highlights (13 measures)

- `Attrition Rate` uses `DIVIDE()` for safe division
- `Headcount YoY %` uses `SAMEPERIODLASTYEAR()` and variables
- `Rolling 3-Month Avg Headcount` uses `AVERAGEX` + `DATESINPERIOD`
- `Department Rank by Attrition` uses `RANKX` with `ALL()`
- `Attrition Rate Excl. Sales` and `Headcount - Active Only` use `CALCULATE` to modify filter context
- `Cumulative Departures YTD` is a running total that resets each year

Full formulas and explanations: [`dax/measures.md`](dax/measures.md)

## Report pages

1. **Executive Overview**: KPI cards (headcount, departures, attrition rate, average
   monthly income), headcount trend, attrition rate by department, and a Year slicer
2. **Attrition Deep Dive**: department table with headcount, departures, attrition rate
   and rank, plus departures by month
3. **Regional Manager View**: headcount by region and a region/country breakdown table,
   used to demonstrate Row-Level Security

## Row-Level Security

Four static roles (`Region_East`, `Region_North`, `Region_South`, `Region_West`), each
filtering `DimGeography[Region]`. The filter flows through the star schema to the fact table,
so every page respects the role. Tested with Power BI Desktop's **View as** feature.

A dynamic version (a user-to-region mapping table with `USERPRINCIPALNAME()`, so one role
serves everyone) is described in [`docs/row_level_security.md`](docs/row_level_security.md)
as the next step for production use.

## How to run this yourself

Open `HR_Workforce_Analytics.pbix` in Power BI Desktop, or see
[`docs/BUILD_GUIDE.md`](docs/BUILD_GUIDE.md) for step-by-step assembly from the CSV files
in `/data` (or connect to `/data/hr_workforce.db`, SQLite).
