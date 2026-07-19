# Relationships and Modeling Review — [Model Name]

## Model shape

[Star schema / snowflake / flatter design — stated with reasoning, e.g. "clean star schema:
all dimensions connect directly to the single fact table, no dimension-to-dimension chains."]

## Relationships

| From | To | Cardinality | Cross-filter | Active? |
|---|---|---|---|---|
| [Table[Column]] | [Table[Column]] | 1:* / 1:1 / *:* | Single / Both | Yes/No |

## Diagram

```mermaid
erDiagram
    [DIMENSION_TABLE] ||--o{ [FACT_TABLE] : "[PK column] -> [FK column]"
```

*(One line per relationship in the table above — don't add or drop any. Use Mermaid's ER
cardinality tokens: `||` = exactly one, `o{` = zero-or-many, `|{` = one-or-many; `||` on both
sides = one-to-one. Entity names can't contain spaces, so use the table name with spaces
replaced by underscores and say so once in a caption line under the diagram. Put the real
from/to column names in the edge label so the picture stays traceable back to the table above.
Mermaid's erDiagram has no dashed/solid distinction for active vs. inactive relationships, so
annotate anything the Findings section flags — inactive relationships, bidirectional
cross-filters, a fragile name-based join where a more reliable ID-based one exists — directly
in the edge label text, e.g. `"SystemUserSeq -> SystemUserSeq (INACTIVE - ID-based, more
reliable)"`. Omit a table from the diagram entirely if the Relationships table above shows it
connecting to nothing — don't invent a connection just to include it; note the isolation in a
caption line instead.)*

## Date table

[Which table is marked as the date table, what it connects to, and whether multiple
date-type columns on any fact table create ambiguity about which drives default time
intelligence.]

## Findings

### Worth knowing
- [Descriptive, non-risky observations — e.g. "one inactive relationship exists between X and
  Y, used via `USERELATIONSHIP` in measure Z."]

### Worth a second look
- [Actual risk flags — many-to-many relationships, bidirectional filters, exposed technical
  key columns, naming inconsistencies. State what it is and why it's worth attention; this
  skill flags, it does not fix (see `semantic-model-authoring` for changes).]
