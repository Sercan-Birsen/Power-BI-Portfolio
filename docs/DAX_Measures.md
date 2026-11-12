# DAX Measure Reference

The formulas below are transcribed and formatted from the supplied case study notes. Measure groups follow the analysis they support.

## Scenario totals

```DAX
Total Actual Sales =
CALCULATE(
    SUM(Sales_All[Sales]),
    Sales_All[Scenario] = "Actual"
)

Total Budget Sales =
CALCULATE(
    SUM(Sales_All[Sales]),
    Sales_All[Scenario] = "Budget"
)

Total Forecast Sales =
CALCULATE(
    SUM(Sales_All[Sales]),
    Sales_All[Scenario] = "Forecast"
)
```

## Actual variance

```DAX
Actual Variance vs Budget =
IF(
    ISBLANK([Total Actual Sales]) || ISBLANK([Total Budget Sales]),
    BLANK(),
    [Total Actual Sales] - [Total Budget Sales]
)

Actual Variance vs Budget % =
IF(
    ISBLANK([Total Actual Sales]) || ISBLANK([Total Budget Sales]),
    BLANK(),
    DIVIDE([Actual Variance vs Budget], [Total Budget Sales])
)

Actual Variance vs PY =
IF(
    ISBLANK([Total Actual Sales]) || ISBLANK([Previous Year Sales]),
    BLANK(),
    [Total Actual Sales] - [Previous Year Sales]
)

Actual Variance vs PY % - YoY Growth % (YTD) =
VAR LastM = [Last Actual Month]
VAR CY = MAX(Sales_All[Year])
VAR CurrentYTD =
    CALCULATE(
        [Total Actual Sales],
        Sales_All[Month Number] <= LastM
    )
VAR PreviousYTD =
    CALCULATE(
        [Total Actual Sales],
        Sales_All[Year] = CY - 1,
        Sales_All[Month Number] <= LastM
    )
RETURN
    IF(
        NOT ISBLANK(CurrentYTD),
        DIVIDE(CurrentYTD - PreviousYTD, PreviousYTD)
    )
```

## Cumulative and YTD

```DAX
Actual Sales (YTD) =
IF(
    ISBLANK([Total Actual Sales]),
    BLANK(),
    CALCULATE(
        [Total Actual Sales],
        FILTER(
            ALL(Sales_All[Month Number], Sales_All[Month]),
            Sales_All[Month Number] <= MAX(Sales_All[Month Number])
        )
    )
)

Cumulative Budget =
CALCULATE(
    [Total Budget Sales],
    FILTER(
        ALL(Sales_All[Month Number], Sales_All[Month]),
        Sales_All[Month Number] <= MAX(Sales_All[Month Number])
    )
)

Cumulative Outlook =
CALCULATE(
    [Full Year Outlook],
    FILTER(
        ALL(Sales_All[Month Number], Sales_All[Month]),
        Sales_All[Month Number] <= MAX(Sales_All[Month Number])
    )
)

Cumulative Variance = [Cumulative Outlook] - [Cumulative Budget]

Forecast Sales (Cumulative) =
IF(
    ISBLANK([Total Forecast Sales]),
    BLANK(),
    CALCULATE(
        [Total Forecast Sales],
        FILTER(
            ALL(Sales_All[Month Number], Sales_All[Month]),
            Sales_All[Month Number] <= MAX(Sales_All[Month Number])
        )
    )
)
```

## Forecast variance

```DAX
Forecast Variance vs Budget =
IF(
    ISBLANK([Total Forecast Sales]) || ISBLANK([Total Budget Sales]),
    BLANK(),
    [Total Forecast Sales] - [Total Budget Sales]
)

Forecast Variance vs Budget % =
IF(
    ISBLANK([Total Forecast Sales]) || ISBLANK([Total Budget Sales]),
    BLANK(),
    DIVIDE([Forecast Variance vs Budget], [Total Budget Sales])
)

Forecast Variance vs PY =
IF(
    ISBLANK([Total Forecast Sales]) || ISBLANK([Previous Year Sales]),
    BLANK(),
    [Total Forecast Sales] - [Previous Year Sales]
)

Forecast Variance vs PY % - YoY Growth % =
VAR CY = MAX(Sales_All[Year])
VAR FcstMonths =
    CALCULATETABLE(
        VALUES(Sales_All[Month Number]),
        Sales_All[Scenario] = "Forecast"
    )
VAR PY =
    CALCULATE(
        [Total Actual Sales],
        Sales_All[Year] = CY - 1,
        FcstMonths
    )
RETURN
    IF(
        NOT ISBLANK([Total Forecast Sales]),
        DIVIDE([Total Forecast Sales] - PY, PY)
    )
```

## Full-year measures

```DAX
Full Year Budget =
VAR SelectedYear = SELECTEDVALUE(Sales_All[Year])
RETURN
    IF(
        ISBLANK(SelectedYear),
        BLANK(),
        CALCULATE(
            [Total Budget Sales],
            Sales_All[Year] = SelectedYear
        )
    )

Full Year Outlook = [Total Actual Sales] + [Total Forecast Sales]

Full Year Outlook PY =
VAR CY = MAX(Sales_All[Year])
RETURN
    CALCULATE(
        [Full Year Outlook],
        Sales_All[Year] = CY - 1
    )

Full Year Outlook Variance vs Budget =
[Full Year Outlook] - [Total Budget Sales]

Full Year Outlook Variance vs Budget % =
DIVIDE([Full Year Outlook Variance vs Budget], [Total Budget Sales])

Full Year Outlook Variance vs PY =
IF(
    NOT ISBLANK([Full Year Outlook PY]),
    [Full Year Outlook] - [Full Year Outlook PY]
)

Full Year Outlook Variance vs PY % =
DIVIDE([Full Year Outlook Variance vs PY], [Full Year Outlook PY])

Average Monthly Sales =
AVERAGEX(
    VALUES(Sales_All[Month Number]),
    [Full Year Outlook]
)
```

## Actuals timing and prior-year comparison

```DAX
Last Actual Month =
CALCULATE(
    MAX(Sales_All[Month Number]),
    Sales_All[Scenario] = "Actual",
    ALLEXCEPT(Sales_All, Sales_All[Year])
)

Previous Year Sales =
VAR CurrentYear = MAX(Sales_All[Year])
VAR CurrentMonth = MAX(Sales_All[Month Number])
RETURN
    IF(
        CurrentYear = MINX(ALL(Sales_All[Year]), Sales_All[Year]),
        BLANK(),
        CALCULATE(
            [Total Actual Sales],
            FILTER(
                ALL(Sales_All[Year]),
                Sales_All[Year] = CurrentYear - 1
            ),
            Sales_All[Scenario] = "Actual",
            Sales_All[Month Number] <= CurrentMonth
        )
    )
```

## Monthly indicators

```DAX
Lowest Month Sales =
MINX(
    VALUES(Sales_All[Month]),
    [Total Actual Sales]
)

Lowest Sales Month =
VAR LowestValue = [Lowest Month Sales]
RETURN
    CONCATENATEX(
        FILTER(
            ALL(Sales_All[Month], Sales_All[Month Number]),
            CALCULATE([Total Actual Sales]) = LowestValue
        ),
        Sales_All[Month],
        ", "
    )

Peak Month Sales =
MAXX(
    VALUES(Sales_All[Month]),
    [Total Actual Sales]
)

Peak Sales Month =
VAR PeakValue = [Peak Month Sales]
RETURN
    CONCATENATEX(
        FILTER(
            ALL(Sales_All[Month], Sales_All[Month Number]),
            CALCULATE([Total Actual Sales]) = PeakValue
        ),
        Sales_All[Month],
        ", "
    )
```

## Quarterly outlook and division share

```DAX
Quarterly Outlook = [Total Actual Sales] + [Total Forecast Sales]

Quarterly Outlook vs Budget =
[Quarterly Outlook] - [Total Budget Sales]

Quarterly Outlook vs Budget % =
IF(
    ISBLANK([Quarterly Outlook]) || ISBLANK([Total Budget Sales]),
    BLANK(),
    DIVIDE([Quarterly Outlook vs Budget], [Total Budget Sales])
)

Sales Share % =
DIVIDE(
    [Full Year Outlook],
    CALCULATE(
        [Full Year Outlook],
        REMOVEFILTERS(Sales_All[Division])
    )
)
```
