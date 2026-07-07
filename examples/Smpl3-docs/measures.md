# Measures — Smpl3 (Regional Sales)

Grouped by source table (no `displayFolder` metadata present in this model).

## Sales (1 measure)

### Total Sales

Total sales revenue across whatever rows are in the current filter context.

```dax
Total Sales = SUM(Sales[Amount])
```

**Note:** this measure returns `BLANK()` rather than `0` when no rows match the current filter
context (default `SUM` behavior) — a card visual showing it against an empty filter selection
will render blank, not a misleading zero.

To view this measure as a year-to-date or prior-year figure instead of the current-period
total, apply it through the `Time Intelligence` calculation group (see
`security-and-extensions.md`) rather than expecting separate YTD/prior-year measures — none
exist in this model.

## Measures with unclear purpose

None — this model has only one measure and its purpose is unambiguous from name and DAX.
