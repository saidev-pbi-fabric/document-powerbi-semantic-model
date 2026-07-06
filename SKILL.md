---
name: document-powerbi-semantic-model
description: >-
  Document Power BI semantic models from TMDL, PBIP, model metadata exports (BIM/JSON),
  Tabular Editor script output, XMLA metadata exports, DAX measure lists, or user-provided
  schema notes. Use when creating business-facing or technical Markdown documentation for
  tables, columns, measures, relationships, hierarchies, calculation groups, perspectives,
  partitions, data sources, model design, DAX logic, data dictionaries, onboarding guides, or
  AI/Copilot readiness reviews. Complementary to `semantic-model-authoring`, which creates,
  edits, and deploys models — this skill explains and documents an existing one, it does not
  modify it. Does not edit, deploy, or publish models, and does not manage workspace
  permissions or RLS/OLS role membership.
---

# Power BI Semantic Model Documentation

Turns a Power BI semantic model — from files, exports, or notes — into clear, portable
Markdown documentation: a data dictionary, plain-language measure explanations, a
relationship/modeling review, and an AI-readiness assessment.

## Workflow Selector

| User wants to... | Do this |
|---|---|
| Document a whole model from scratch | Full workflow below, all steps |
| "Create a data dictionary" | Steps 1-2, then [output-templates.md](./references/output-templates.md) `data-dictionary.md` only |
| "Explain the measures" / "what does this DAX do" | Steps 1-2, then [dax-explanation.md](./references/dax-explanation.md) |
| "Review this model for AI/Copilot readiness" | Steps 1-2, then [ai-readiness.md](./references/ai-readiness.md) |
| "Review relationships" / "any modeling risks here" | Steps 1-2, then [relationship-and-modeling-review.md](./references/relationship-and-modeling-review.md) |
| "Create onboarding docs for a new analyst" | Full workflow, emphasize `model-overview.md` + `data-dictionary.md` |
| Model source is unclear or mixed (some TMDL, some pasted notes) | Start at [source-types.md](./references/source-types.md) |

## Required Privacy Rules — read before writing anything

This skill is designed to also be safe for public/open-source use. Before any output is
written, **always** load [privacy-and-redaction.md](./references/privacy-and-redaction.md) and
apply it. In short: never include tenant IDs, workspace IDs, connection strings, endpoints,
file paths containing usernames, internal security group names, sample customer/production
data rows, or proprietary business terms in generated output unless the user has explicitly
said the output is for internal/private use only and accepted that scope. When in doubt,
generalize the example rather than reproduce it verbatim. Run the final "public safety check"
from that reference before presenting any output as done.

## Workflow

### 1. Identify source type and collect files

Load [source-types.md](./references/source-types.md). Determine what you actually have —
a TMDL folder, a PBIP project, a BIM/JSON export, Tabular Editor script output, an XMLA
metadata dump, a CSV/Markdown measure inventory, or pasted schema notes — since the inspection
method differs per source. **If the source is ambiguous or incomplete, ask the user rather
than guessing which files matter.**

### 2. Build the inventory

Load [documentation-workflow.md](./references/documentation-workflow.md) and
[tmdl-and-pbip-inspection.md](./references/tmdl-and-pbip-inspection.md) (if the source is
TMDL/PBIP). Catalog every table, column, measure, relationship, hierarchy, calculation group,
perspective, partition, and data source you can find. Note anything you cannot determine
(hidden logic, undocumented columns) rather than inventing an explanation.

### 3. Map business domains

Group tables and measures by apparent business area (Sales, Inventory, Quality, Finance,
etc.) using naming patterns, display folders, and relationships as evidence. State this
grouping as inferred, not authoritative, unless the user confirms it.

### 4. Explain DAX and business logic

Load [dax-explanation.md](./references/dax-explanation.md). For every measure: state its
business purpose in one sentence before the mechanics, then explain the calculation. Flag
filter-context assumptions, time-intelligence patterns, and blank-vs-zero behavior explicitly.

### 5. Review relationships and modeling

Load [relationship-and-modeling-review.md](./references/relationship-and-modeling-review.md).
Identify star-schema shape, fact vs. dimension tables, active/inactive relationships,
many-to-many risk, bidirectional filters, hidden technical columns, and the date table's role.

### 6. Assess AI/Copilot readiness (only when asked, or as part of a full documentation pass)

Load [ai-readiness.md](./references/ai-readiness.md). Review naming friendliness,
descriptions, synonyms, display folders, hidden-field exposure, measure ambiguity, and
certified/core status.

### 7. Produce documentation using the templates

Load [output-templates.md](./references/output-templates.md) to decide which files to
produce, then use the matching file(s) under `assets/templates/` as the exact structure —
don't invent a different shape. Typical output set: `index.md`, `model-overview.md`,
`data-dictionary.md`, `measures.md`, `relationships.md`, and (when requested)
`ai-readiness-review.md`.

### 8. Run the privacy check (again)

Re-apply [privacy-and-redaction.md](./references/privacy-and-redaction.md) to the *generated
output*, not just the source — a redacted source can still leak sensitive terms once you've
written prose about it. This is the last step before presenting anything as final.

### 9. Report assumptions, unknowns, and open questions

Every documentation pass will have gaps — undocumented columns, ambiguous measure logic,
naming inconsistencies you can't resolve without asking. Write these to `open-questions.md`
(see [output-templates.md](./references/output-templates.md)) rather than silently guessing or
omitting them.

## Documentation Outputs

| File | Produced when |
|---|---|
| `index.md` | Always, for a full documentation pass — the entry point/navigation |
| `model-overview.md` | Always — purpose, storage mode, table count, business domains |
| `data-dictionary.md` | Always — every table/column with type, description, source |
| `measures.md` | Always if any measures exist — grouped by business area, plain-language + DAX |
| `relationships.md` | Always if more than one table — star schema shape, risks flagged |
| `ai-readiness-review.md` | Only when explicitly requested |
| `open-questions.md` | Whenever any gap/ambiguity was found — do not omit this file to look more complete than the source supports |

## Quality Checklist

Before presenting documentation as finished, confirm:

1. Every measure has a plain-language purpose sentence, not just its DAX reproduced.
2. Every relationship's cardinality and cross-filter direction is stated, not just "related to."
3. Anything uncertain is in `open-questions.md`, not silently omitted or asserted as fact.
4. The privacy check has been run on the *output*, not just noted as a source-inspection step.
5. Business domain groupings are labeled as inferred if the user didn't confirm them.
6. Output uses the templates in `assets/templates/` — no ad hoc structure invented mid-task.

## Reference Files

| Topic | Reference | When to load |
|---|---|---|
| Recognizing input types | [source-types.md](./references/source-types.md) | First — before inspecting anything |
| Standard sequence | [documentation-workflow.md](./references/documentation-workflow.md) | Once source type is known |
| TMDL/PBIP file patterns | [tmdl-and-pbip-inspection.md](./references/tmdl-and-pbip-inspection.md) | When source is TMDL or PBIP |
| Explaining DAX | [dax-explanation.md](./references/dax-explanation.md) | Before writing any measure explanation |
| Relationship/modeling review | [relationship-and-modeling-review.md](./references/relationship-and-modeling-review.md) | Before writing `relationships.md` |
| AI/Copilot readiness | [ai-readiness.md](./references/ai-readiness.md) | When AI-readiness is requested |
| Privacy & redaction rules | [privacy-and-redaction.md](./references/privacy-and-redaction.md) | Before inspecting source AND before emitting output — twice |
| Output file roles | [output-templates.md](./references/output-templates.md) | Before writing any output file |

## Must / Prefer / Avoid

### MUST

- Run the privacy/redaction check before emitting any output, no exceptions, even for
  private/internal use — ask the user to confirm scope if unclear.
- State assumptions and unknowns explicitly in `open-questions.md` rather than presenting a
  best guess as confirmed fact.
- Use the templates under `assets/templates/` for output structure — don't invent a new shape
  per task.

### PREFER

- Plain-language explanation before DAX/technical detail, in that order, for every measure.
- Grouping by business domain over alphabetical or table-order listing, when a sensible
  grouping is evident.
- Flagging a modeling risk (many-to-many, bidirectional filter, missing date table) even if
  not explicitly asked to review modeling — it belongs in `relationships.md` either way.

### AVOID

- Producing documentation from partial inspection and presenting it as complete — say what
  wasn't inspected (e.g., "measures only, relationships not reviewed this pass").
- Inventing business meaning for a column/measure whose name and logic don't make it obvious —
  flag it as unclear instead.
- Testing against, or including examples from, real private/production data — see
  [privacy-and-redaction.md](./references/privacy-and-redaction.md) and use a public/synthetic
  sample model for any testing or demonstration.

### DENY

- Editing, deploying, or publishing the semantic model in any way — this skill documents an
  existing model, it does not change one. Redirect edit/deploy requests to
  `semantic-model-authoring`.
- Managing workspace permissions, RLS/OLS role membership, or access grants — out of scope,
  redirect to the Power BI/Fabric portal.

## Troubleshooting

| Symptom | Fix |
|---|---|
| Source type unclear (mixed files, no obvious TMDL/PBIP structure) | Ask the user what they have rather than guessing; see [source-types.md](./references/source-types.md) |
| Measure logic genuinely ambiguous even after inspection | Write it to `open-questions.md` with your best-effort read flagged as uncertain — don't silently skip it |
| User provides only a partial export (e.g., measures list, no relationships) | Document what's available, explicitly note what's out of scope for this pass in `open-questions.md` |
| Business domain grouping doesn't map cleanly to naming/display folders | Ask the user for the real business grouping rather than forcing an inferred one |
