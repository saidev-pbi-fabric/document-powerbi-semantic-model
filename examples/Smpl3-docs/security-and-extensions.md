# Security & Extensions — Smpl3 (Regional Sales)

No perspectives exist in this model — that section is omitted rather than padded with "none
found."

## Hierarchies

| Hierarchy | Table | Levels (top → bottom) | Notes |
|---|---|---|---|
| Fiscal Calendar | Date | Year > Quarter > Month | Drill path for date slicers/visuals. |

## Calculation groups

### Time Intelligence

Switches `Total Sales` (or any measure it's applied to) between a current, year-to-date, or
prior-year view without needing three separate measures.

**Calculation items:**

#### Current

Returns the selected measure unchanged — the baseline/current-period view.

```dax
Current = SELECTEDMEASURE()
```

#### YTD

Returns the selected measure accumulated from the start of the calendar year to the current
filter context's date.

```dax
YTD = CALCULATE(SELECTEDMEASURE(), DATESYTD('Date'[Date]))
```

#### Prior Year

Returns the selected measure for the same period one year earlier (same period, prior year —
not a rolling prior-period comparison).

```dax
Prior Year = CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR('Date'[Date]))
```

## Row-level security (RLS) roles

**Documented for reference only — role membership and access grants are out of scope for this
skill; see the Power BI/Fabric portal for that.**

| Role | Table | Filter expression (DAX) | Effect |
|---|---|---|---|
| Regional Manager | Region | `[RegionName] = "North"` | Members of this role only see rows for the "North" region and, by extension through the `Sales`→`Region` relationship, only `Sales` rows tied to North. |

*(Checked the filter literal against `privacy-and-redaction.md` before including it — "North"
is a generic, non-confidential region label in this synthetic model, not a real business term,
so it's included as-is.)*

## Notes

- None. Every hierarchy, calculation group, and RLS role in this model was fully documentable
  from the TMDL source with no ambiguity.
