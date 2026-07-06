# TMDL and PBIP Inspection

File patterns to expect and read when the source is a TMDL folder or a PBIP project's
`<Name>.SemanticModel/` folder.

## Core files

| File | Contains |
|---|---|
| `definition.pbism` | PBIP semantic model manifest — usually just a version/settings pointer, low documentation value but confirms this is a real PBIP semantic model folder |
| `definition/model.tmdl` | Model-level settings: compatibility level, culture, default query settings, sometimes annotations describing tool/version provenance |
| `definition/database.tmdl` | Database-level metadata (older/alternate TMDL layout — may not exist if folded into `model.tmdl`) |
| `definition/relationships.tmdl` | Every relationship: `fromColumn`, `toColumn`, `crossFilteringBehavior` (`automatic`/`bothDirections`/`single`), `isActive`, `fromCardinality`/`toCardinality` |
| `definition/tables/<TableName>.tmdl` | One file per table — columns, measures, partitions, hierarchies all live inside the table's own file |
| `definition/expressions.tmdl` | Shared/named expressions — data source connection definitions, parameters (M query text) |
| `definition/cultures/<locale>.tmdl` | Translations, if any — usually skip unless documentation must be localized |
| `definition/perspectives.tmdl` | Named subsets of the model exposed to specific tools/users |
| `definition/roles.tmdl` | RLS role definitions — **inspect for documentation purposes only** (e.g., "an RLS role named X exists, filtering on column Y"); do not act on role membership, that's out of scope (see `SKILL.md` DENY section) |

## Inside a table's `.tmdl` file

Each table file typically contains, in order:
- `table 'TableName'` header, possibly with `lineageTag` and lists of annotations
- `column` blocks — `dataType`, `sourceColumn` (import/DQ) or `sourceLineageTag` (Direct Lake),
  `formatString`, `isHidden`, `displayFolder`, `summarizeBy`
- `measure` blocks — the DAX expression as a multi-line string, `formatString`, `displayFolder`,
  `isHidden`
- `partition` blocks — the M query (import/DQ) or `entityName`/`schemaName`/`mode: directLake`
  (Direct Lake) describing where the data actually comes from
- `hierarchy` blocks, if any — named hierarchies over a set of columns
- `annotation` lines — often tool metadata, occasionally useful business context if a prior
  author left notes

## What to extract for the data dictionary vs. what to skip

**Extract:** table name, column names + data types + hidden status + display folder, measure
names + DAX + format string + display folder, relationship pairs + cardinality + cross-filter
direction + active/inactive, partition mode (Import/DirectQuery/Direct Lake) per table.

**Skip or summarize only:** `lineageTag`/`sourceLineageTag` GUIDs (internal plumbing, not
useful in human documentation — mention their existence only if explaining Direct Lake column
binding mechanics specifically), raw annotation blocks unless they contain actual business
prose, culture/translation files unless localization is the explicit ask.

## Direct Lake specifics

A table with `mode: directLake` in its partition and `entityName`/`schemaName` pointing at a
OneLake table (rather than an M query) is Direct Lake-sourced. Document this explicitly —
"this table reads live from `<schema>.<entity>` via Direct Lake, not an import/refresh cycle" —
since it materially changes how a reader should think about data freshness compared to an
Import-mode table in the same model.

## Relationship cardinality — TMDL's defaulting convention

`relationships.tmdl` entries frequently omit `fromCardinality`/`toCardinality` entirely. **This
is not "cardinality unknown" — TMDL defaults to many-to-one (from the "from" side to the "to"
side) when the property is absent.** Document the default explicitly ("many-to-one, TMDL
default — not an explicit override") rather than writing "not stated" as if it were a genuine
gap. Only treat cardinality as a real open question when a relationship's cardinality seems
inconsistent with the tables' apparent fact/dimension roles (e.g. a "many" default on what
looks like a one-to-one lookup) — in that case, flag it as worth verifying against live data,
since TMDL's declared cardinality and the data's actual cardinality can drift after a schema
change. An explicit `fromCardinality: one` (or `toCardinality: many` used atypically) is a
genuine signal the model author overrode the default — always call these out specifically.

## PBIP-specific note

If the folder also contains a sibling `<Name>.Report/` directory, **do not inspect or document
it** — report/visual layout is out of scope for this skill (see `semantic-model-authoring` or a
report-authoring skill for that layer). Only the `.SemanticModel/` folder is in scope here.

## When TMDL and a live connection disagree

If documentation is being produced from on-disk TMDL but the user mentions the model is also
live-editable (e.g., via an MCP connection), treat the TMDL as potentially stale and say so —
this skill documents from files/exports, it doesn't connect to a live model itself. If the user
wants a live-connected read instead, that's a `semantic-model-authoring`/`semantic-model-
consumption` task, not this one.
