# Source Types — Recognizing and Inspecting Model Inputs

This skill can document a semantic model from several different kinds of input. Identify
which one you have before doing anything else — the inspection method differs per type.

## 1. TMDL folder

**Recognize by:** a `definition/` folder containing `model.tmdl`, `database.tmdl` (or
`model.tmdl` alone in newer format), `relationships.tmdl`, and a `tables/` subfolder with one
`.tmdl` file per table. May also include `expressions.tmdl`, `cultures/`, `perspectives.tmdl`,
`roles.tmdl`.

**Inspect by:** reading each file directly — TMDL is plain text, human-readable, diff-friendly.
See [tmdl-and-pbip-inspection.md](./tmdl-and-pbip-inspection.md) for exact file-by-file
guidance.

## 2. PBIP semantic model folder

**Recognize by:** a `<Name>.SemanticModel/` folder (containing the TMDL `definition/` folder
above, plus `definition.pbism`), typically sitting alongside a `<Name>.Report/` folder and a
`<Name>.pbip` entry-point file.

**Inspect by:** same as TMDL folder — the semantic model content lives in
`<Name>.SemanticModel/definition/`. Ignore the `.Report/` folder entirely unless report-layer
context is explicitly requested (out of scope for this skill either way).

## 3. BIM / model JSON export

**Recognize by:** a single `.bim` or `.json` file containing a `model` object with `tables`,
`relationships`, `dataSources` (or `expressions`), and similar top-level keys — this is the
older TMSL/JSON metadata format, still commonly exported from Tabular Editor or SSMS.

**Inspect by:** parsing the JSON structure directly. Table objects contain `columns` and
`measures` arrays; relationship objects have `fromTable`/`toTable`/`fromColumn`/`toColumn` and
a `crossFilteringBehavior`. Slower to hand-read than TMDL but equivalent information.

## 4. Tabular Editor script output

**Recognize by:** output from a Tabular Editor C# script — often a flattened list or table of
objects (table name, column name, data type, measure name, DAX expression) rather than the
full model structure. May be a `.csv`, `.tsv`, or plain text dump.

**Inspect by:** treating it as a partial inventory. Confirm with the user what the script
actually captured (measures only? columns only? both?) before assuming completeness — this
source type is the most likely to be a partial view, not the full model.

## 5. XMLA metadata export

**Recognize by:** output captured from a live XMLA/AMO connection — could be a DAX
`INFO.VIEW.*` query result, a `TMSCHEMA_*` DMV dump, or a full metadata extract via `ssas-mcp`/
`powerbi-modeling-mcp`-style tooling. Often tabular (rows/columns) rather than nested JSON.

**Inspect by:** reconstructing the model structure from the flattened rows — group by
table/object type first, then treat similarly to a Tabular Editor export. If this came from a
live connection rather than a static export, note that in the output as "captured live from
[source] on [date]" since a live model can drift from this snapshot.

## 6. CSV / Markdown measure inventory

**Recognize by:** a simple spreadsheet-shaped list — measure name, DAX, maybe a business
description column — with no table/column/relationship structure at all.

**Inspect by:** documenting exactly what's provided. Do not infer tables, columns, or
relationships that aren't in this input — if the user wants a full model documentation and
only provided a measure list, say so in `open-questions.md` rather than guessing at the rest
of the model.

## 7. User-pasted schema notes

**Recognize by:** free-text description in the conversation itself — no file, just prose or a
rough list from the user.

**Inspect by:** treating the user's own words as the source of truth, but ask clarifying
questions for anything genuinely ambiguous (column meaning, relationship direction) rather than
guessing. This is the least reliable source type for producing a confident data dictionary —
say so, and keep the output appropriately hedged.

## Mixed or unclear sources

If the user provides more than one type (e.g., a TMDL folder plus a separate pasted note about
a measure that isn't in the TMDL), treat the TMDL/PBIP/BIM/XMLA export as the structural source
of truth and the pasted notes as supplementary business context — call out explicitly in the
output which parts came from which source when they might conflict.

If it's genuinely unclear what you've been given, **ask the user** rather than guessing which
files matter. A wrong assumption here propagates into every downstream document.
