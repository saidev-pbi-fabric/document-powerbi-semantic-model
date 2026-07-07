# Documentation Workflow — Inventory Detail and Partial Requests

This file adds depth to two steps in `SKILL.md`'s own Workflow section (step 2, build
inventory; step 3, map business domains) and covers how to scope a narrower request. It does
not restate the full step sequence — `SKILL.md` is the single source of truth for that; if the
two ever seem to disagree, `SKILL.md` wins.

## Building the inventory (depth on SKILL.md step 2)

Produce a complete internal catalog before writing any documentation:
- Tables (name, row-count if knowable, whether fact or dimension by apparent role)
- Columns per table (name, data type, hidden/visible, display folder)
- Measures (name, DAX expression, format string, display folder, hidden/visible)
- Relationships (from/to table and column, cardinality, cross-filter direction, active/inactive)
- Hierarchies, calculation groups, perspectives, partitions, RLS roles, data sources — document
  whichever of these actually exist; don't pad the inventory with "none found" sections unless
  the user wants an explicit completeness statement.

Treat this step as separate from writing prose — get the facts assembled and verified against
the source first, narrative comes after.

## Mapping business domains (depth on SKILL.md step 3)

Group tables and measures into apparent business areas using naming conventions, display
folders, and relationship clusters as evidence (e.g., all tables/measures referencing "Sales"
or sharing a common dimension likely belong together). Always state this grouping as inferred
unless the user has confirmed the real business taxonomy — a wrong inferred grouping is worse
than an honest "grouped by apparent pattern, not confirmed" caveat.

## Partial requests

A user asking only for "the measures explained" or "just a data dictionary" doesn't need the
domain-mapping or relationship-review steps run in full — but still needs the privacy check
(`SKILL.md` step 8) run on whatever is produced, every time, regardless of scope.
