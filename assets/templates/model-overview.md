# Model Overview — [Model Name]

## Purpose

[One paragraph: what business problem this model serves, inferred from table/measure content
if not explicitly told. Flag as inferred if the user didn't confirm it.]

## Storage mode

[Import / DirectQuery / Direct Lake / mixed — and what that means practically for data
freshness in this model specifically.]

## Scale

| | Count |
|---|---|
| Tables | [n] |
| Columns (total) | [n] |
| Measures | [n] |
| Relationships | [n] |
| Hierarchies | [n, if any] |
| Calculation groups | [n, if any] |

## Business domains

[List the inferred (or confirmed) business-area groupings, e.g.:]
- **Sales** — `Fact_Sales`, `Dim_Product`, `Dim_Customer`, measures: `Total Sales`, `Sales YTD`, ...
- **Inventory** — ...

*(State clearly whether these groupings are confirmed by the model owner or inferred from
naming/structure.)*

## Key tables at a glance

| Table | Role | Grain | Notes |
|---|---|---|---|
| [name] | Fact / Dimension | [e.g. "one row per order line"] | [anything notable] |

## Notable design choices

[Anything structurally significant worth flagging up front — e.g. "uses a single shared date
dimension across all facts," "Direct Lake sourced from OneLake, no traditional refresh cycle,"
"uses calculation groups for time intelligence rather than per-measure DAX."]
