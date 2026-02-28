# DAX Measure Library (Power BI)

This document lists the key **DAX measures** used in the *Retail Omnichannel Sales Performance Dashboard (2022–2024)*.
The measures are designed to:
- Standardize KPI definitions across pages (Revenue, Customer, Product)
- Enable **time-based comparisons** (PY, variance, growth)
- Improve dashboard usability using **dynamic formatting** (colors, arrows, titles/tooltips)


## Naming Conventions
I use a consistent naming approach so measures are easy to find and reuse.

**Base KPI**
- `[Revenue]`, `[Cost]`, `[Profit]`, `[Sales]`, `[Customers]`, `[Views]`, `[Avg Conversion Rate]`, `[Avg Customer Rating]`, `[Deal Closing Rate]`

**Comparison / Time Intelligence**
- `PY`: Previous Year (same period last year)
- `Variance`: difference vs PY
- `Growth`: percent change vs PY

Example:
- `[Revenue PY]`
- `[Revenue Variance]`
- `[Revenue Growth %]`

**Visual Helpers**
- `[Revenue Growth Arrow]`
- `[Revenue Color]`


---

## KPI Measure Patterns
This is used for all this KPI Measurement:
- `[Revenue]`
- `[Profit]`
- `[Avg COnversion Rate]`
- `[Avg Customer Rating]`
- `[Cost]`
- `[Views]`
- `[Sales]`

### Pattern A — Previous Year (PY)
Used to compare the same period in the previous year.

```DAX
-- Example template (replace [Base KPI] with your KPI)
[Base KPI PY] =
CALCULATE(
    [Base KPI],
    PREVIOUSYEAR('Calendar'[Date])
)
```

### Pattern B — Variance vs PY

```DAX
[Base KPI Variance] =
[Base KPI] - [Base KPI PY]
```
### Pattern C — Growth % vs PY

```DAX
[Base KPI Growth %] =
DIVIDE(
    [Base KPI] - [Base KPI PY],
    [Base KPI PY]
)
```
### Pattern D — Growth Arrow (for KPI Cards)

```DAX
[Base KPI] = 
IF(
    ISBLANK([Base KPI]),
    BLANK(),
    IF(
        [Base KPI Variance] > 0,
        "+" & ROUND([Base KPI] * 100,1) & "%" & " ↑",
        ROUND([Base KPI] * 100,1) & " %" & " ↓"
    )
)
```
### Pattern E — Color (for Conditional Formatting)

```DAX
Avg Customer Rating Color =
    IF(
      [Base KPI Variance]>=0,"Green","Red"
    )
```
---

## Contribution & Share Measures

### Revenue % of Lead Source
Used for charts like “Revenue by Lead Source” with contribution labels.
```DAX
[Revenue % of Lead] =
DIVIDE(
    [Revenue],
    CALCULATE([Revenue], ALL('dim_lead_source'))
)
```

### Revenue % of Channel
```DAX
[Revenue % of Channel] =
DIVIDE(
    [Revenue],
    CALCULATE([Revenue], ALL('dim_sales_channel'))
)
```

### Top N / Ranking Measures
```DAX
[Product Rank by Revenue] =
RANKX(
    ALL('dim_product'[Product]),
    [Revenue],
    ,
    DESC
)
```
