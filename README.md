# Finance Reporting Case Study

A Power BI case study analyzing actual sales, budget, and forecast performance across divisions. The report consolidates monthly data and compares results with budget and prior-year performance.

## Business Questions

- How are actual sales tracking against budget and the prior year?
- What is the full-year outlook compared with budget and the prior year?
- Which divisions contribute most to the outlook and variance?
- How do monthly and quarterly trends develop through the year?
- Which months have the highest and lowest actual sales?

## Report Pages

1. Annual Financial Performance
2. Division Performance
3. Flux Analysis
4. Historical Actual Sales
5. 2026 Sales vs Budget

## Power Query

Power Query reshapes the annual sales and budget inputs into a consolidated `Sales_All` table. It unpivots monthly columns, adds year and scenario attributes, creates a date field, appends the inputs, and adds month fields for analysis.

The model also includes `Quarter` and `Quarter Number` columns. Month and quarter labels are sorted by their numeric columns so visuals display in calendar order.

## DAX Measures

The measures cover actual, budget, and forecast totals; variance and prior-year comparisons; YTD and cumulative performance; quarterly outlook; sales share; and peak and lowest sales months.

## Repository Contents

- `report/Case_Study_Sercan_Birsen.pbix` — Power BI report
- `docs/DAX_Measures.md` — DAX measure reference
- `docs/Power_Query_Workflow.md` — transformation steps and added columns
- `docs/Case_study_Formulas.docx` — original formula notes
