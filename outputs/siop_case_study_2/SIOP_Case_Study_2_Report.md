# SIOP Case Study 2 Report

## Objective
Transform monthly SIOP forecast snapshots so the latest snapshot and prior snapshots can be compared side by side, then merge actuals by `Item`, `Country`, and `Period` for forecast accuracy and bias reporting.

## Data Used
- Forecast rows: 246,477
- Actual rows: 500,000
- Forecast snapshots: 2023 P10, 2023 P11, 2023 P12
- Countries: A, B, C, D
- Periods: 15

## Transformation Logic
1. Convert `Item` to text in both Forecast and Actual tables so joins are stable.
2. Aggregate Forecast by `Item`, `Country`, `Period`, and `AsOfPeriod`, summing Revenue, NonRevenue, and Capex forecast quantities.
3. Rank snapshots by period:
   - Resultant = 2023 P12
   - Lag 0 = 2023 P11
   - Lag 1 = 2023 P10
4. Pivot `AsOfPeriod` into separate Resultant, Lag 0, and Lag 1 forecast columns.
5. Aggregate Actual by `Item`, `Country`, and `Period`, then merge to the transformed forecast table.
6. Build slicers for year-period, country/region hierarchy, and lag selection.

## KPI Logic
- Forecast Accuracy % = `1 - ABS(Forecast Qty - Actual Qty) / Actual Qty`
- Forecast Bias % = `(Forecast Qty - Actual Qty) / Actual Qty`
- Use `DIVIDE()` in Power BI so zero-actual cases return blank instead of an error.

## Recommended Dashboard Views
- Executive KPI cards for selected lag: Revenue Forecast, NonRevenue Forecast, Capex Forecast, Revenue Accuracy %, Revenue Bias %.
- Clustered bar chart: Actual Revenue vs selected lag Revenue Forecast by Country.
- Line chart: Revenue Accuracy % by Period.
- Matrix: Country x Period with Actual, selected lag forecast, Accuracy %, and Bias %.
- Slicers: Period, Country, Region/Cluster fields, and Lag Selection.

## Notes
The raw tables contain duplicate item-country-period records, so aggregation before pivoting is required to avoid double counting in Power BI.
