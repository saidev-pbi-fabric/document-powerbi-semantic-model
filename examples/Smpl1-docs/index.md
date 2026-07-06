# COVID Bakeoff — Documentation

Generated documentation for the `COVID Bakeoff` Power BI semantic model (Microsoft's public
`powerbi-desktop-samples` repo, "Sample Reports" folder).

## Contents

- [Model Overview](./model-overview.md) — purpose, storage mode, scale, business domains
- [Data Dictionary](./data-dictionary.md) — every table and column
- [Measures](./measures.md) — plain-language + DAX explanations, grouped by source table
- [Relationships](./relationships.md) — model shape, cardinality, flagged risks
- [Open Questions](./open-questions.md) — assumptions and gaps found during this pass

## Source

- **Input type:** PBIP / TMDL folder (`Smpl1.SemanticModel/definition`)
- **Captured:** 2026-07-06, static file inspection (not a live model connection)
- **Scope of this pass:** full model documentation — data dictionary, measures, relationships.
  AI-readiness review not run (not requested).

## Privacy note

Two data sources in the original file contained real, specific identifiers — a private SQL
Server hostname and a local file path with a personal Windows username. Both were generalized
in this documentation per this skill's privacy/redaction rules; see `data-dictionary.md`'s
per-table Source notes for what was redacted.

---
*Generated using the `document-powerbi-semantic-model` skill.*
