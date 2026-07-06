# Measures — COVID Bakeoff

Grouped by source table (this model has almost no `displayFolder` metadata set on measures,
so table-of-origin is used as the grouping instead of an inferred business domain).

## OWID COVID data (10 measures)

### Total cases per mil
Latest reported total confirmed cases per million population, for whatever country/filter
context is selected.
```dax
Total cases per mil = MAX('OWID COVID data'[Total cases per million])
```

### Total deaths p/m
Latest reported total deaths per million population.
```dax
Total deaths p/m = MAX('OWID COVID data'[Total deaths per million])
```

### Latest vaccinations per million
The vaccination rate (per million) as of the most recent date in the current filter context.
Uses a variable to pin "latest date" before averaging, so it reflects one specific day, not an
average across the visible date range.
```dax
Latest vaccinations per million =
    var latestDate = MAX('OWID COVID data'[date])
    return CALCULATE(AVERAGE('OWID COVID data'[New vaccinations smoothed per million]), 'OWID COVID data'[Date] = latestDate)
```

### % of Population Vaccinated
Percentage of population vaccinated. Branches on filter context: if a single country is
selected, uses OWID's own pre-computed per-hundred figure directly; otherwise (multi-country
context) computes it manually from a running-total vaccination measure divided by summed
population. **Blank/zero behavior not explicit** — worth checking how this renders when no
vaccination data exists for a selection.
```dax
% of Population Vaccinated =
    if(HASONEVALUE(Countries[Country]), max('OWID COVID data'[People vaccinated per hundred]), [Total vaccinations running total in Date]/SUM(Countries[Population])*100)/100
```

### Cases 7d Avg per country
Average daily new cases over the trailing 7 days, for the country/context selected, using
country-level `Cases per country[IncrementalCases]` (not the OWID new-cases column).
```dax
Cases 7d Avg per country =
    var sevenDayPeriod = DATESINPERIOD('Dates'[Date], min('Dates'[Date]), -7, DAY)
    return CALCULATE(SUM('Cases per country'[IncrementalCases])/7, sevenDayPeriod)
```

### Cases 7d Mvg Avg per million
Same 7-day trailing average concept as above, but computed from OWID's own `New cases` column
and normalized per million population.
```dax
Cases 7d Mvg Avg per million =
    var mvgAvgCases = CALCULATE(SUM('OWID COVID data'[New cases])/7, DATESINPERIOD('Dates'[Date], min('Dates'[Date]), -7, DAY))
    var population = SUM(Countries[Population])
    return DIVIDE(mvgAvgCases, population) * 1000000
```

### Change in cases WoW%
Week-over-week percentage change in the 7-day average case rate, comparing today's trailing
average to the same measure 7 days ago. **The result is capped at a maximum of 500% (`min(...,
5)`)** — likely to suppress extreme spikes from near-zero denominators rather than reflecting
a genuine business rule; see `open-questions.md`.
```dax
Change in cases WoW% =
    VAR __PREV_WEEK = CALCULATE([Cases 7d Avg per country], DATEADD('Dates'[Date], -7, DAY))
    RETURN min(DIVIDE([Cases 7d Avg per country] - __PREV_WEEK, __PREV_WEEK), 5)
```

### Total vaccinations
Sum of new vaccinations administered in the current filter context (not a running total —
see the next measure for that).
```dax
Total vaccinations = SUM('OWID COVID data'[new_vaccinations])
```

### Total vaccinations running total in Date
Cumulative vaccinations up to and including the latest selected date — a running total over
the `Dates` axis, using the standard `ALLSELECTED` + `ISONORAFTER` running-total pattern (the
model's own metadata tags this as a "RunningTotal" template measure).
```dax
Total vaccinations running total in Date =
    CALCULATE([Total vaccinations], FILTER(ALLSELECTED('Dates'[Date]), ISONORAFTER('Dates'[Date], MAX('Dates'[Date]), DESC)))
```

### Daily Change (for formatting)
Day-over-day (2-day lag) percent change in the 7-day-average-per-million case rate. The name
("for formatting") and the model's own template metadata ("MonthOverMonth") suggest this
measure exists specifically to drive a KPI/trend-arrow visual rather than for direct display —
flagged, not confidently explained further.
```dax
Daily Change (for formatting) =
    VAR __PREV_DAY = CALCULATE([Cases 7d Mvg Avg per million], DATEADD('Dates'[Date], -2, DAY))
    var changeFromPrevDay = DIVIDE([Cases 7d Mvg Avg per million] - __PREV_DAY, __PREV_DAY)
    RETURN changeFromPrevDay
```

## Cases per US State (14 measures)

### Cases DoD%
Day-over-day percent change in state-level incremental cases. Hidden — likely a helper for
another visual rather than direct display.
```dax
Cases DoD% =
    VAR __PREV_DAY = CALCULATE(SUM('Cases per US State'[Incremental cases]), DATEADD('Dates'[Date], -1, DAY))
    RETURN DIVIDE(SUM('Cases per US State'[Incremental cases]) - __PREV_DAY, __PREV_DAY)
```

### IncrementalCases Mvg Avg
7-day trailing average of new state-level cases.
```dax
IncrementalCases Mvg Avg = CALCULATE(SUM('Cases per US State'[Incremental cases])/7, DATESINPERIOD('Dates'[Date], min('Dates'[Date]), -7, DAY))
```

### Cases per million 7d avg
Same 7-day average, normalized per million state population.
```dax
Cases per million 7d avg =
    var numCases = CALCULATE(SUM('Cases per US State'[Incremental cases])/7, DATESINPERIOD('Dates'[Date], min('Dates'[Date]), -7, DAY))
    var pop = SUM(States[Population])
    return DIVIDE(numCases, pop)*1000000
```

### Cumulative ppl vaccinated per 100
Cumulative rate of fully-vaccinated people per hundred population, defaulting to 0 rather than
blank when no data exists (explicit `COALESCE`, unusual for this model — most other measures
don't guard blank/zero explicitly).
```dax
Cumulative ppl vaccinated per 100 = COALESCE(SUM('Cases per US State'[People fully vaccinated per hundred]),0)
```

### DailyVaccinations Mvg Avg
7-day trailing average of daily vaccinations administered.
```dax
DailyVaccinations Mvg Avg = CALCULATE(SUM('Cases per US State'[daily_vaccinations])/7, DATESINPERIOD('Dates'[Date], min('Dates'[Date]), -7, DAY))
```

### Vaccines distributed
Total vaccines distributed to a state — branches between a sum (multi-date context) and a
"latest known value" read (single-date context), mirroring the same pattern used in
`% of Population Vaccinated` above.
```dax
Vaccines distributed =
    var totalDistributed = SUM('Cases per US State'[total_distributed])
    var latestDate = MAX('OWID COVID data'[date])
    var latestVaccineTotal = CALCULATE(AVERAGE('Cases per US State'[total_distributed]), 'Cases per US State'[Date] = latestDate)
    return if(HASONEVALUE(Dates[Date]), totalDistributed, latestVaccineTotal)
```

### daily_vaccinations running total in Date
Cumulative daily vaccinations, same running-total pattern as OWID's equivalent measure.
```dax
daily_vaccinations running total in Date =
    CALCULATE(SUM('Cases per US State'[daily_vaccinations]), FILTER(ALLSELECTED('Dates'[Date]), ISONORAFTER('Dates'[Date], MAX('Dates'[Date]), DESC)))
```

### Full vaccinations per hundred
Average full-vaccination rate per hundred, defaulting to 0 when blank.
```dax
Full vaccinations per hundred = COALESCE(AVERAGE('Cases per US State'[People fully vaccinated per hundred]),0)
```

### Cumulative cases per 100
Cumulative case rate per 100 population — note this divides an *average* of cumulative cases
by an *average* of population, not a sum; correct only if the filter context resolves to a
single state (otherwise this isn't a true aggregate rate). Flagged, not confidently resolved —
see `open-questions.md`.
```dax
Cumulative cases per 100 = AVERAGE('Cases per US State'[CumulativeCases]) / AVERAGE(States[Population]) * 100
```

### Change from 12/27
Percent change in the case moving-average versus a fixed baseline date (December 27 [year
implied by context, not stated in the DAX]).
```dax
Change from 12/27 =
    VAR __BASELINE_VALUE = CALCULATE([IncrementalCases Mvg Avg], ALLSELECTED(Dates[Start of week]), 'Dates'[Start of week] = DATE(2020, 12, 27))
    VAR __MEASURE_VALUE = [IncrementalCases Mvg Avg]
    VAR __PCT_Diff = IF(NOT ISBLANK(__MEASURE_VALUE), DIVIDE(__MEASURE_VALUE - __BASELINE_VALUE, __BASELINE_VALUE))
    return __PCT_Diff
```

### Ratio fully vaccinated
Ratio of the "fully vaccinated per hundred" figure to the "at least one dose per hundred"
figure — effectively, the share of vaccinated people who are fully vaccinated.
```dax
Ratio fully vaccinated = MAX('Cases per US State'[People fully vaccinated per hundred]) / MAX('Cases per US State'[people_vaccinated_per_hundred])
```

### % Fully Vacc'd
Fully-vaccinated rate as of a fixed date (April 11, 2021), not the current filter context —
a point-in-time snapshot measure.
```dax
% Fully Vacc'd = CALCULATE(MAX('Cases per US State'[People fully vaccinated per hundred])/100, ALLSELECTED(Dates[Start of week]), 'Dates'[Start of week] = DATE(2021, 4, 11))
```

### % One+ Shots
Share of population with at least one vaccine dose, current filter context.
```dax
% One+ Shots = MAX('Cases per US State'[people_vaccinated_per_hundred])/100
```

### Daily Change US (for formatting)
Same "for formatting" pattern as OWID's equivalent measure — day-over-day (2-day lag) percent
change in the state 7-day case rate, likely a KPI-visual helper.
```dax
Daily Change US (for formatting) =
    VAR __PREV_DAY = CALCULATE([Cases per million 7d avg], DATEADD('Dates'[Date], -2, DAY))
    var changeFromPrevDay = DIVIDE([Cases per million 7d avg] - __PREV_DAY, __PREV_DAY)
    RETURN changeFromPrevDay
```

## Countries (2 measures)

### GDP % chg 2020
Average year-over-year GDP change for 2020, pulled from the separate `GDP History` table —
unusual placement (a measure on a dimension table referencing a different fact table), but
mechanically straightforward.
```dax
GDP % chg 2020 = CALCULATE(AVERAGE('GDP History'[% change]), 'GDP History'[Year] = 2020)
```

### GDP Chg
A pass-through alias of the measure above — exists as a separately-named measure with
identical value. Likely created for a specific visual/label requirement; business reason for
having both isn't stated. See `open-questions.md`.
```dax
GDP Chg = [GDP % chg 2020]
```

## Govt Measures (1 measure, hidden)

### Cases 1 days later
Average incremental case count exactly one day after whatever date is in context — used to
look at case trends the day immediately following a government measure. Name has a minor
grammatical quirk ("1 days later") reproduced as-is from the source.
```dax
Cases 1 days later =
    var datePlus = SELECTEDVALUE(Dates[Date])+1
    return CALCULATE(AVERAGE('Cases per country'[IncrementalCases]), FILTER(Dates, Dates[Date] = datePlus))
```
