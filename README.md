# HR Workforce Analytics — Power BI Dashboard

A workforce headcount and attrition analytics project built on a proper star-schema data
model, SQL-based ETL, and Power BI with DAX time-intelligence and Row-Level Security.

## Data model

![Star Schema](star_schema.png)

- `FactHeadcount` — monthly snapshot grain (one row per employee per active month),
  enabling headcount trends, YoY comparisons, and rolling averages
- `DimEmployee`, `DimDepartment`, `DimGeography`, `DimDate` — conformed dimensions

## Stack

| Layer | Tool |
|---|---|
| Source data | Synthetic HR dataset (320 employees, ~14.5K monthly fact rows) |
| ETL | SQL (staging → cleaned star schema) — see `/sql` |
| Transformation | Power Query M — see `/powerquery` |
| Modeling & measures | Power BI, DAX — see `/dax` |
| Security | Row-Level Security by region — see `/docs/row_level_security.md` |

## Key DAX highlights

- `Attrition Rate` using `DIVIDE()` for safe division
- `Headcount YoY %` using `SAMEPERIODLASTYEAR()`
- `Rolling 3-Month Avg Headcount` using `AVERAGEX` + `DATESINPERIOD`
- `Department Rank by Attrition` using `RANKX` with `ALL()`
- `Attrition Rate Excl. Sales` demonstrating `CALCULATE` + filter context modification

Full formulas and explanations: [`/dax/measures.md`](../dax/measures.md)

## Report pages

1. **Executive Overview** — KPI cards, headcount trend, department/region breakdown
2. **Attrition Deep Dive** — ranked attrition by department, YoY trend, drill-through to
   employee-level detail
3. **Regional Manager View** — RLS-filtered view scoped to a single region

## Row-Level Security

Implemented via a `RegionUserMapping` table + `USERPRINCIPALNAME()`, rather than hardcoded
role filters. Details: [`/docs/row_level_security.md`](row_level_security.md)

## How to run this yourself

See [`BUILD_GUIDE.md`](BUILD_GUIDE.md) for step-by-step Power BI Desktop assembly
instructions, or connect directly to `/data/hr_workforce.db` (SQLite) as the source.
