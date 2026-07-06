# document-powerbi-semantic-model

An agent skill that turns a Power BI semantic model into Markdown documentation: a data
dictionary, plain-language explanations of what the DAX actually does, a relationship review,
and an AI-readiness assessment if you want one.

It works from TMDL folders, PBIP projects, BIM/JSON metadata exports, Tabular Editor script
output, XMLA metadata exports, a plain measure list, or just notes you type in yourself. No
live model connection needed.

This is the counterpart to Microsoft's own `semantic-model-authoring` skill. That one builds
and edits models. This one reads an existing model and explains it, so someone other than the
person who built it can actually understand it. It won't touch the model, deploy anything, or
manage permissions.

## Why this exists

Most Power BI documentation tooling assumes you have a live Desktop connection or an XMLA
endpoint open. In practice, a lot of documentation work doesn't start that way: someone hands
you a TMDL export, you're reviewing a PBIP project someone else built, or you've got a metadata
dump from Tabular Editor and nothing else. This skill is built for that situation. Point it at
whatever you actually have, and it writes the docs without needing a live connection to
anything.

I built this after spending time reading through Microsoft's own `skills-for-fabric` collection
and noticing the gap: plenty of tooling for building and editing models, not much for making an
existing one legible to someone who didn't build it. That's the problem this solves.

## Install

Copy this repo into the skill folder your agent already watches. No dependencies, no build
step.

**Claude Code:**
```bash
git clone https://github.com/saidev-pbi-fabric/document-powerbi-semantic-model ~/.claude/skills/document-powerbi-semantic-model
```

**Codex CLI or GitHub Copilot CLI** (both read from the same personal skills folder):
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
| `index.md` | Always, as the entry point |
| `model-overview.md` | Always: purpose, storage mode, scale, business domains |
| `data-dictionary.md` | Always: every table and column |
| `measures.md` | If measures exist: plain-language explanation plus DAX, grouped by business area |
| `relationships.md` | If more than one table exists: model shape, cardinality, anything risky flagged |
| `ai-readiness-review.md` | Only if you ask for it |
| `open-questions.md` | Whenever something's genuinely unclear, instead of pretending it isn't |

## Privacy by design

This is meant to be safe for producing documentation that ends up somewhere public. Before it
writes anything, it checks for tenant/workspace IDs, connection strings, file paths with
usernames baked in, internal group names, and sample production data, and generalizes them.
See [`references/privacy-and-redaction.md`](./references/privacy-and-redaction.md).

## See it working

`examples/` has two real, fully public Power BI sample models from Microsoft's own
[`powerbi-desktop-samples`](https://github.com/microsoft/powerbi-desktop-samples) repo, a
COVID-19 analytics model and a CRM sales/service model, along with the full documentation this
skill generated for each. Start at `examples/Smpl1-docs/index.md` or
`examples/Smpl2-docs/index.md`. Both catch real issues in the source models: a relationship
that relies on a fragile name match when a more reliable ID-based one existed but was left
inactive, a filter that's commented out inside otherwise-live DAX, a column whose whole formula
is `BLANK()`, a table with no relationship to anything else in the model. Nothing invented,
just what was actually in the TMDL.

## Structure

```
SKILL.md                  — the router: workflow, rules, what to load when
references/                — one file per topic, loaded as needed
assets/templates/          — the exact shape of each output file
examples/                  — two real public sample models plus their generated docs
```

## Scope

**In scope:** inspecting a model from files or exports; documenting tables, columns, measures,
relationships, hierarchies, calculation groups, perspectives, partitions, and data sources;
explaining DAX in plain language; a data dictionary and AI-readiness notes; Markdown output;
redacting anything sensitive along the way.

**Out of scope:** editing or deploying the model, publishing to Fabric, managing workspace
permissions, report visuals or PBIR files, running production queries you didn't explicitly
provide, RLS/OLS role membership.

## License

MIT, see [LICENSE](./LICENSE).
