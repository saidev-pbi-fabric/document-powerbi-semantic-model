# Data Dictionary — Smpl3 (Regional Sales)

One section per table. Descriptions marked "(inferred)" are read from naming/usage, not
source metadata — this synthetic model has no description metadata on any object.

## Date

**Role:** Dimension &nbsp;·&nbsp; **Grain:** one row per calendar day &nbsp;·&nbsp;
**Storage:** Import

| Column | Type | Hidden? | Display Folder | Description |
|---|---|---|---|---|
| Date | dateTime | No | — | Calendar date; marked as the model's date table (inferred from `isDateTable`). |
| Year | int64 | No | — | Calendar year (inferred). |
| Quarter | string | No | — | Calendar quarter, e.g. "Q1" (inferred). |
| Month | string | No | — | Calendar month name (inferred). |

Also carries the `Fiscal Calendar` hierarchy (Year → Quarter → Month) — see
`security-and-extensions.md`.

## Region

**Role:** Dimension &nbsp;·&nbsp; **Grain:** one row per region &nbsp;·&nbsp;
**Storage:** Import

| Column | Type | Hidden? | Display Folder | Description |
|---|---|---|---|---|
| RegionID | int64 | Yes | — | Surrogate key, hidden — supports the relationship to `Sales`, not meant for report authors (inferred). |
| RegionName | string | No | — | Region label, e.g. "North" (inferred). |

## Sales

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per sale &nbsp;·&nbsp;
**Storage:** Direct Lake — reads live from `dbo.Sales` via OneLake, not an import/refresh cycle

| Column | Type | Hidden? | Display Folder | Description |
|---|---|---|---|---|
| SalesID | int64 | Yes | — | Surrogate key, hidden (inferred). |
| RegionID | int64 | Yes | — | Foreign key to `Region`, hidden — technical join column (inferred). |
| DateKey | dateTime | Yes | — | Foreign key to `Date`, hidden — technical join column (inferred). |
| Amount | double | No | — | Sale line value in local currency (inferred). |

## Notes on this dictionary

- No description metadata exists anywhere in this model — every description above is inferred
  from column naming and role, not confirmed by a model owner. See `open-questions.md`.
- `Time Intelligence` is a calculation-group table, not a conventional data table — its
  contents are documented in `security-and-extensions.md` rather than here.
