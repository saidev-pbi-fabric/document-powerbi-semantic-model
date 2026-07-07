# Relationships and Modeling Review — Smpl3 (Regional Sales)

## Model shape

Clean star schema: both dimensions (`Date`, `Region`) connect directly to the single fact
table (`Sales`); no dimension-to-dimension chains. `Time Intelligence` is a calculation group,
not a relational table, so it has no relationships to any other table.

## Relationships

| From | To | Cardinality | Cross-filter | Active? |
|---|---|---|---|---|
| `Sales[RegionID]` | `Region[RegionID]` | 1:* (many-to-one, TMDL default — not an explicit override) | Single | Yes |
| `Sales[DateKey]` | `Date[Date]` | 1:* (many-to-one, TMDL default — not an explicit override) | Single | Yes |

Neither relationship has an explicit `fromCardinality`/`toCardinality` set in
`relationships.tmdl` — per TMDL's defaulting convention this means many-to-one from `Sales` to
each dimension, which matches the tables' apparent fact/dimension roles, so this isn't flagged
as an open question.

## Date table

`Date` is marked as the model's date table (`isDateTable`) and connects to `Sales` via
`DateKey`. `Sales` has only one date-type column (`DateKey`), so there's no ambiguity about
which column drives default time intelligence.

## Findings

### Worth knowing
- Both relationships rely on the TMDL many-to-one default rather than an explicit cardinality
  override — worth confirming against live data if the model grows and a "many" default ever
  looks inconsistent with a table's actual role.
- `Sales[RegionID]` and `Sales[DateKey]` are both hidden technical join columns, correctly
  marked hidden — not exposed to report authors.

### Worth a second look
- None found. No many-to-many relationships, no bidirectional cross-filtering, no exposed
  technical key columns, no naming inconsistencies across the model's small object set.
