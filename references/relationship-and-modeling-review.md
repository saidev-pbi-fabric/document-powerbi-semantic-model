# Relationship and Modeling Review

Produces both the descriptive content for `relationships.md` (what exists) and the risk
flags (what might be a problem) — write both into the same output file, clearly separated.

## Descriptive: what to document per relationship

- From table/column → To table/column
- Cardinality (one-to-many, one-to-one, many-to-many) — **TMDL defaults to many-to-one when
  `fromCardinality`/`toCardinality` is absent; that's a real default, not "unknown."** See
  [tmdl-and-pbip-inspection.md](./tmdl-and-pbip-inspection.md#relationship-cardinality--tmdls-defaulting-convention).
  Only flag cardinality as a genuine open question when it looks inconsistent with the tables'
  apparent roles.
- Cross-filter direction (single vs. both/bidirectional)
- Active or inactive (an inactive relationship is only usable via `USERELATIONSHIP` in DAX —
  note which measures, if any, reference it)

## Star schema shape

State whether the model is a clean star schema (dimensions connect only to facts, not to each
other), a snowflake (dimensions chain through other dimensions), or a flatter/wider design.
Identify which tables are facts (typically large row-count, mostly numeric/measure-feeding
columns, foreign keys out to dimensions) versus dimensions (descriptive attributes, smaller row
count, one-side of relationships) — state this as inferred from structure and role, since
tables aren't explicitly tagged "fact" or "dimension" in the model metadata itself.

## Risk patterns to flag when present

- **Many-to-many relationships** — can cause double-counting or ambiguous filter propagation;
  flag which tables and why it exists (sometimes intentional, e.g., a bridge table — say so if
  the pattern looks deliberate rather than accidental).
- **Bidirectional cross-filtering** — flag every bidirectional relationship explicitly; it's a
  common source of unexpected filter propagation and circular-dependency errors as a model
  grows. Note which direction is the "extra" one beyond a normal single-direction star schema.
- **Hidden technical/surrogate-key columns** — columns like `TableID`, `SK_*`, or GUID-shaped
  columns that exist only to support relationships and shouldn't be exposed to report authors.
  Confirm they're marked hidden; flag if a technical key column is visible.
- **Date table role** — confirm there's a marked date table (`isDateTable` or equivalent
  metadata), that it connects to fact tables' date columns with the expected cardinality, and
  whether multiple date-type columns on a fact table create ambiguity about which one drives
  time intelligence by default (versus needing `USERELATIONSHIP`).
- **Naming consistency** — inconsistent casing, abbreviation, or pluralization across similar
  objects (e.g., `CustomerID` vs. `customer_id` on different tables) makes a model harder to
  navigate; flag as a minor finding, not a blocker.

## What NOT to do here

Do not propose fixes as if this were a modeling/authoring task — this skill documents and
flags, it does not implement. If the user wants a risk fixed, that's `semantic-model-
authoring`'s job; hand off rather than attempting the change here (see `SKILL.md` DENY
section).

## Severity framing

When listing findings, group them loosely as "worth knowing" vs. "worth fixing" rather than a
formal severity scale — the goal is a reader (often not the model's original author) quickly
understanding what's solid and what needs a second look, not a formal audit report.
