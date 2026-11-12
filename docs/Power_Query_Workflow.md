# Power Query Workflow

## Source tables

The case study starts with separate annual sales inputs for 2024, 2025, and 2026, plus a 2026 budget input. The 2026 sales input includes forecast values.

## Transformations

1. Unpivot the monthly columns so each month is represented by a row.
2. Reorder the columns into a consistent layout.
3. Add a year field and extract the scenario (Actual, Budget, or Forecast) from the source data using its delimiter.
4. Create a date field for monthly reporting.
5. Append the prepared inputs into the consolidated `Sales_All` table.
6. Add `Month` and `Month Number` fields to `Sales_All`.

The `Month` column is sorted by `Month Number` so visuals show months chronologically rather than alphabetically.

## Added model columns

These calculated columns were added to `Sales_All` in the Power BI model:

```DAX
Quarter = "Q" & ROUNDUP(Sales_All[Month Number] / 3, 0)

Quarter Number = ROUNDUP(Sales_All[Month Number] / 3, 0)
```

Sort `Quarter` by `Quarter Number` so the quarter labels display in calendar order.

