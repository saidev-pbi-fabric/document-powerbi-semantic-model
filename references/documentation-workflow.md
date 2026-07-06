# Documentation Workflow — Standard Sequence

This is the default end-to-end sequence for a full documentation pass. For a narrower request
(just measures, just AI-readiness, etc.) follow the relevant subset per the Workflow Selector
in `SKILL.md`.

## 1. Collect source files

Identify the source type ([source-types.md](./source-types.md)) and gather everything
available. If working from a folder, list it fully before reading individual files — don't
assume the first file you open is representative of the whole model.

## 2. Build inventory

Produce a complete internal catalog before writing any documentation:
- Tables (name, row-count if knowable, whether fact or dimension by apparent role)
- Columns per table (name, data type, hidden/visible, display folder)
- Measures (name, DAX expression, format string, display folder, hidden/visible)
- Relationships (from/to table and column, cardinality, cross-filter direction, active/inactive)
- Hierarchies, calculation groups, perspectives, partitions, data sources — document whichever
  of these actually exist; don't pad the inventory with "none found" sections unless the user
  wants an explicit completeness statement.

Treat this step as separate from writing prose — get the facts assembled and verified against
the source first, narrative comes after.

## 3. Map business domains

Group tables and measures into apparent business areas using naming conventions, display
folders, and relationship clusters as evidence (e.g., all tables/measures referencing "Sales"
or sharing a common dimension likely belong together). Always state this grouping as inferred
unless the user has confirmed the real business taxonomy — a wrong inferred grouping is worse
than an honest "grouped by apparent pattern, not confirmed" caveat.

## 4. Draft object documentation

Using the inventory from step 2, draft the data dictionary content: one entry per table
(purpose, grain, key columns) and one entry per column (name, type, description — inferred
from naming/usage if no description metadata exists, and flagged as inferred when it is).

## 5. Explain measures

Follow [dax-explanation.md](./dax-explanation.md). Every measure gets a plain-language purpose
statement before its mechanics are explained.

## 6. Review relationships

Follow [relationship-and-modeling-review.md](./relationship-and-modeling-review.md). This step
produces both descriptive content (what relationships exist) and risk-flagging content (what
might be a modeling problem) — both belong in `relationships.md`.

## 7. Create index and navigation

Once the individual output files exist, produce `index.md` (or `README.md` if this is the root
of a repo) linking to each — see [output-templates.md](./output-templates.md) for the expected
shape. Don't skip this step even for a small model; it's what makes multi-file documentation
actually navigable.

## 8. Run privacy checks

Apply [privacy-and-redaction.md](./privacy-and-redaction.md) to the drafted output — this is
a check on what you're about to publish, not just on the source you inspected. Do this as a
distinct pass, not folded silently into earlier steps, so it's not skipped under time pressure.

## 9. Emit final docs

Write the output files using the templates in `assets/templates/`. Include `open-questions.md`
whenever step 2-6 surfaced anything uncertain, ambiguous, or out of scope for this pass — this
file is not optional padding, it's where honesty about documentation gaps lives.

## Partial requests

A user asking only for "the measures explained" or "just a data dictionary" doesn't need steps
3, 6, or 7 run in full — but still needs step 8 (privacy check) run on whatever is produced,
every time, regardless of scope.
