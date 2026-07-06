# Contoso CRM Sales & Service — Documentation

Generated documentation for the `Contoso CRM Sales & Service` Power BI semantic model
(sample CRM/sales-pipeline model, Contoso demo data).

## Contents

- [Model Overview](./model-overview.md) — purpose, storage mode, scale, business domains
- [Data Dictionary](./data-dictionary.md) — every table and column
- [Measures](./measures.md) — plain-language + DAX explanations, grouped by source table
- [Relationships](./relationships.md) — model shape, cardinality, flagged risks
- [Open Questions](./open-questions.md) — assumptions and gaps found during this pass

## Source

- **Input type:** PBIP / TMDL folder (`Smpl2.SemanticModel/definition`)
- **Captured:** 2026-07-06, static file inspection (not a live model connection)
- **Scope of this pass:** full model documentation — data dictionary, measures, relationships.
  AI-readiness review not run (not requested).

## Privacy note

Every table in the source loads from the same local Excel workbook, whose file path contained
a personal Windows username. Generalized in this documentation per this skill's
privacy/redaction rules; see `data-dictionary.md`'s header note.

---
*Generated using the `document-powerbi-semantic-model` skill.*
