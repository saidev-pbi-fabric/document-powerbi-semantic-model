# document-powerbi-semantic-model

An agent skill that turns Power BI semantic models into clear, portable Markdown
documentation: a data dictionary, plain-language DAX explanations, a relationship/modeling
review, and an AI-readiness assessment.

Works from TMDL folders, PBIP projects, BIM/JSON metadata exports, Tabular Editor script
output, XMLA metadata exports, DAX measure lists, or plain user-provided schema notes — no
live model connection required.

This is complementary to Microsoft's own `semantic-model-authoring` skill: that one creates,
edits, and deploys models. This one explains and documents an existing one, so teams can
understand, review, govern, onboard, and reuse them. It does not edit, deploy, or publish
anything, and it does not manage workspace permissions or RLS/OLS role membership.

## Why this exists

Power BI's own documentation tooling generally assumes a live Desktop connection or an XMLA
endpoint. A lot of real documentation work happens against files instead — a TMDL export
someone handed you, a PBIP project you're reviewing, a metadata dump from Tabular Editor. This
skill is built for that case: point it at what you actually have, and it produces documentation
without needing a live connection to anything.

The positioning: I studied how high-quality agent skills are structured (Microsoft's own
`skills-for-fabric` collection was the reference), then built a focused skill for a gap I saw —
making semantic models understandable, governable, and AI-ready, not just built or edited.

## Install

Copy this repository's contents into the skill-discovery folder your agent already watches.
No dependencies, no build step.

**Claude Code:**
```bash
git clone https://github.com/saidev-pbi-fabric/document-powerbi-semantic-model ~/.claude/skills/document-powerbi-semantic-model
```

**Codex CLI or GitHub Copilot CLI** (both scan the same personal skills folder):
```bash
git clone https://github.com/saidev-pbi-fabric/document-powerbi-semantic-model ~/.agents/skills/document-powerbi-semantic-model
```

Restart your agent session afterward so it picks up the new skill.

## Example prompts

- "Document this Power BI semantic model."
- "Create a data dictionary from this TMDL folder."
- "Explain the measures and relationships in this PBIP semantic model."
- "Generate business-facing documentation for this Power BI dataset."
- "Review this semantic model for AI/Copilot readiness."
- "Create onboarding docs for a new analyst using this model."
- "Summarize the DAX measures by business area."

## What it produces

| File | When |
|---|---|
| `index.md` | Always — navigation/entry point |
| `model-overview.md` | Always — purpose, storage mode, scale, business domains |
| `data-dictionary.md` | Always — every table and column |
| `measures.md` | Whenever measures exist — plain-language + DAX, grouped by business area |
| `relationships.md` | Whenever more than one table exists — model shape, cardinality, flagged risks |
| `ai-readiness-review.md` | Only when explicitly requested |
| `open-questions.md` | Whenever the pass finds a genuine gap or ambiguity — not silently omitted |

## Privacy by design

This skill is built to also be safe for producing documentation meant for public/open-source
use: it actively checks for and generalizes tenant/workspace IDs, connection strings, file
paths containing usernames, internal group names, and sample production data before emitting
anything. See [`references/privacy-and-redaction.md`](./references/privacy-and-redaction.md).

## See it working

`examples/` contains two real, fully public Power BI sample models (from Microsoft's own
[`powerbi-desktop-samples`](https://github.com/microsoft/powerbi-desktop-samples) repo) with
the complete documentation this skill generated for each — a COVID-19 analytics model and a
CRM sales/service model. Read `examples/Smpl1-docs/index.md` or `examples/Smpl2-docs/index.md`
to see real output, including genuine modeling issues the skill caught (a fragile name-based
relationship where a more robust ID-based one existed but was left inactive, a disabled filter
hiding inside otherwise-live DAX, an orphaned table with no relationships, and more).

## Structure

```
SKILL.md                  — router: workflow, rules, when to load what
references/                — one file per topic, loaded on demand
assets/templates/          — the exact output structure for each generated file
examples/                  — two real public sample models + their generated docs
```

## Scope

**In scope:** inspecting a model from files/exports; documenting tables, columns, measures,
relationships, hierarchies, calculation groups, perspectives, partitions, and data sources;
explaining DAX in plain language; producing a data dictionary, business glossary, and
AI-readiness notes; portable Markdown output; privacy redaction.

**Out of scope:** editing or deploying semantic models, publishing to Fabric, managing
workspace permissions, writing report visuals or PBIR files, running production queries unless
explicitly provided, RLS/OLS role membership.

## License

MIT — see [LICENSE](./LICENSE).
