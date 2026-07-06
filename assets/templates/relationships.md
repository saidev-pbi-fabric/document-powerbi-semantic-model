# Relationships and Modeling Review — [Model Name]

## Model shape

[Star schema / snowflake / flatter design — stated with reasoning, e.g. "clean star schema:
all dimensions connect directly to the single fact table, no dimension-to-dimension chains."]

## Relationships

| From | To | Cardinality | Cross-filter | Active? |
|---|---|---|---|---|
| [Table[Column]] | [Table[Column]] | 1:* / 1:1 / *:* | Single / Both | Yes/No |

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
