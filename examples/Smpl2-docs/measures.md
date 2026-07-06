# Measures — Contoso CRM Sales & Service

Grouped by source table (no `displayFolder` metadata present in this model).

## Cases (8 measures)

### Case Count
Total number of support cases in the current filter context.
```dax
Case Count = COUNTROWS('Cases')
```

### Escalations
Share of cases marked as escalated.
```dax
Escalations = DIVIDE(CALCULATE(COUNTROWS('Cases'),'Cases'[Is Escalated] = TRUE()) , [Case Count],0)
```

### SLA Compliance
Share of cases that did **not** violate SLA (i.e., compliance rate, not violation rate — note
the measure name is the positive framing of the `Is SLA Violation` flag).
```dax
SLA Compliance = DIVIDE(CALCULATE(COUNTROWS('Cases'),'Cases'[Is SLA Violation] = FALSE()) , [Case Count],0)
```

### Cases MoM%
Month-over-month percent change in case volume (model's own metadata tags this a
"MonthOverMonth" template measure).
```dax
Cases MoM% =
    VAR __PREV_MONTH = CALCULATE([Case Count], DATEADD('Case Calendar'[Date], -1, MONTH))
    RETURN DIVIDE([Case Count] - __PREV_MONTH, __PREV_MONTH)
```

### Cases % by Product
Share of total case volume attributable to whichever product is in the current filter
context, against the all-products total.
```dax
Cases % by Product = DIVIDE([Case Count], CALCULATE([Case Count],All('Products')))
```

### Cases % by Subject
Same pattern as above, but against the all-subjects total instead of all-products.
```dax
Cases % by Subject = DIVIDE([Case Count], CALCULATE([Case Count],All('Cases'[Subject])))
```

### CSAT Impact, CSAT Impact - Subject, CSAT Impact - Products, CSAT Impact - Agent
Four parallel measures, each answering "how much does removing this dimension's current
selection from the CSAT average change the result" — i.e., a normalized impact/sensitivity
score for Topic, Subject, Product, and Agent respectively. Mechanically identical pattern,
differing only in which column is excluded in the `AllAvgExcept` filter.
```dax
CSAT Impact =
    VAR FactorAvg = AVERAGE('Cases'[CSAT])
    VAR AllAvg = CALCULATE(AVERAGE('Cases'[CSAT]), ALL('Cases'))
    VAR AllAvgExcept = CALCULATE(AVERAGE('Cases'[CSAT]), FILTER(ALL('Cases'), 'Cases'[Topic] <> SELECTEDVALUE('Cases'[Topic])))
    RETURN 1 - (AllAvgExcept / AllAvg)
```
*(`CSAT Impact - Subject`, `- Products`, `- Agent` follow the identical pattern, filtering on
`Subject`, `ProductSeq`, and `Agent` respectively instead of `Topic`.)* **Note:** `FactorAvg` is
computed in every variant but never actually used in the `RETURN` — a harmless dead variable,
not a bug, but worth knowing if refactoring these measures.

## Opportunities (10 measures)

### Revenue Won
Total value of opportunities with status "Won", in the current filter context.
```dax
Revenue Won = CALCULATE(SUMX(Opportunities, Opportunities[Value]), FILTER(Opportunities, Opportunities[Status] = "Won"))
```

### Revenue In Pipeline
Total value of open opportunities at pipeline step 2 or later (parsed from the leading digit
of `PipelineStep`), adjusted by the what-if `Opportunity Forecast Adjustment` percentage.
```dax
Revenue In Pipeline =
    VAR Revenue = CALCULATE(SUMX(Opportunities, Opportunities[Value]), FILTER(Opportunities, Opportunities[Status] = "Open" && VALUE(LEFT(Opportunities[PipelineStep],1)) >=2))
    RETURN Revenue + (Revenue * ('Opportunity Forecast Adjustment'[Forecast Adjustment Value] / 100))
```

### Forecast %
Combined won + pipeline revenue as a percentage of the revenue goal (`Rev Goal`, on `Owners`).
```dax
Forecast % = (([Revenue Won]+[Revenue In Pipeline]))/ [Rev Goal]
```

### Forecast
Combined won + pipeline revenue, as a dollar figure (the numerator of `Forecast %`).
```dax
Forecast = ([Revenue Won]+[Revenue In Pipeline])
```

### Opportunity Count In Pipeline
Count of open opportunities. **Note:** a stage filter (`PipelineStep IN {...}`) is present in
the DAX but commented out (`--`) — currently counts *all* open opportunities regardless of
stage, not just later-stage ones as the commented-out logic would suggest. Flagged in
`open-questions.md`.
```dax
Opportunity Count In Pipeline = CALCULATE(COUNT(Opportunities[Value]), FILTER(Opportunities, Opportunities[Status] = "Open"))
```

### Opportunity Count
Total count of all opportunities regardless of status.
```dax
Opportunity Count = COUNTAX(Opportunities,TRUE())
```

### Count of Won / Count of Lost
Count of opportunities with status "Won" / "Lost" respectively.
```dax
Count of Won = COUNTAX(FILTER(KEEPFILTERS(Opportunities),Opportunities[Status] = "Won"), Opportunities[OpportunitySeq])
```

### Close %
Win rate — won opportunities as a share of all closed (won + lost) opportunities. Excludes
still-open opportunities from the denominator entirely (not a share of all opportunities).
```dax
Close % = [Count of Won]/([Count of Won]+[Count of Lost])
```

### Revenue Open
Total value of opportunities still open (all pipeline steps, unlike `Revenue In Pipeline`
which only counts step 2+).
```dax
Revenue Open = CALCULATE(SUMX(Opportunities, Opportunities[Value]), FILTER(Opportunities, Opportunities[Status] = "Open"))
```

## Owners (1 measure)

### Rev Goal
A dynamically-computed revenue goal: 75% of open pipeline value (stage 3+) plus revenue
already won, rounded to the nearest 100,000 — falling back to rounding to the nearest 10,000
if that produces a non-positive number (e.g., for an owner/context with very little pipeline).
```dax
Rev Goal =
    VAR RevenueInPipeline = CALCULATE(SUMX(Opportunities, Opportunities[Value]), FILTER(Opportunities, Opportunities[Status] = "Open" && VALUE(LEFT(Opportunities[PipelineStep],1)) >=3))
    VAR BaseGoal = MROUND(([Revenue Won]+ (RevenueInPipeline*.75)),100000)
    RETURN IF(BaseGoal > 0, BaseGoal, MROUND(([Revenue Won]+ (RevenueInPipeline*.75)),10000))
```

## Opportunity Forecast Adjustment (1 measure, hidden)

### Forecast Adjustment Value
Reads the currently-selected value from the what-if `Forecast Adjustment` parameter slicer,
defaulting to 0 if nothing is selected. Feeds `Revenue In Pipeline`'s adjustment.
```dax
Forecast Adjustment Value = SELECTEDVALUE('Opportunity Forecast Adjustment'[Forecast Adjustment], 0)
```
