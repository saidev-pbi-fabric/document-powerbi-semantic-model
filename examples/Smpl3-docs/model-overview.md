# Model Overview — Smpl3 (Regional Sales)

## Purpose

*(Inferred — not confirmed by a model owner.)* A small regional sales reporting model: total
sales revenue by region and time period, with a time-intelligence switch (current / YTD /
prior year) and a role that restricts a regional manager persona to their own region's data.

## Storage mode

Mixed. `Date` and `Region` are Import mode (M query). `Sales` is Direct Lake — it reads live
from `dbo.Sales` via OneLake, not an import/refresh cycle, so its data freshness follows the
lakehouse directly rather than a scheduled refresh. `Time Intelligence` is a calculation group
(no data of its own, so storage mode doesn't apply to it).

## Scale

| | Count |
|---|---|
| Tables | 4 (3 data tables + 1 calculation group) |
| Columns (total) | 9 |
| Measures | 1 |
| Relationships | 2 |
| Hierarchies | 1 |
| Calculation groups | 1 (3 calculation items) |

## Business domains

*(Inferred from structure — not confirmed.)*
- **Sales** — `Sales`, `Region`, `Date`, measure: `Total Sales`, calculation group: `Time Intelligence`

## Key tables at a glance

| Table | Role | Grain | Notes |
|---|---|---|---|
| `Sales` | Fact | One row per sale | Direct Lake — reads live from `dbo.Sales`, not import |
| `Region` | Dimension | One row per region | Import mode |
| `Date` | Dimension | One row per calendar day | Import mode, marked as the model's date table |
| `Time Intelligence` | Calculation group | N/A — 3 calculation items | Switches `Total Sales` between current/YTD/prior-year views |

## Notable design choices

- Uses a calculation group (`Time Intelligence`) rather than separate YTD/prior-year measures
  per metric — scales better if more measures are added later, but means the time-period
  context comes from a slicer on the calculation group, not from the measure name itself.
- `Sales` is Direct Lake-sourced while `Date`/`Region` are Import — a mixed-mode model. Worth
  knowing that `Sales` data freshness is tied to the underlying lakehouse table, not a Power BI
  refresh schedule.
- A single RLS role (`Regional Manager`) restricts visibility to one region — see
  `security-and-extensions.md`.
