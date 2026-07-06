# AI / Copilot Readiness Review

Only run this when explicitly requested, or as an optional section of a full documentation
pass. Produces `ai-readiness-review.md`.

## What "AI-ready" means here

Whether a semantic model is well-suited to natural-language querying (Copilot, Data Agents, or
any conversational BI layer) — not a general model-quality review, though there's overlap with
[relationship-and-modeling-review.md](./relationship-and-modeling-review.md).

## Checklist

- **Friendly table and measure names** — does `FctSalesTxn2024` read worse to a natural-
  language query than `Sales Transactions`? Flag technical/abbreviated names that a business
  user wouldn't type when asking a question.
- **Descriptions** — do tables, columns, and measures have description metadata an AI layer
  can use to disambiguate intent? Flag objects with no description, especially ones whose name
  alone is ambiguous.
- **Synonyms** — are common alternate business terms captured anywhere (e.g., "revenue" as a
  synonym for a measure literally named "Sales Amount")? Most models won't have this
  configured — note it as a gap, don't assume its absence is a defect if the user hasn't
  indicated AI-readiness was a design goal before now.
- **Display folders** — do they organize the model in a way that helps disambiguate similar-
  sounding objects, or are dozens of measures dumped in one folder?
- **Hidden fields** — are technical/surrogate columns actually hidden (good — reduces noise
  for both humans and AI) or exposed (bad for both)?
- **Ambiguous measures** — near-duplicate measures with unclear naming differences (e.g.,
  `Sales`, `Sales 2`, `Sales Final`) are a known failure mode for natural-language query
  disambiguation — flag explicitly.
- **Certified / core / promoted status** — if the model or workspace has a certification/
  endorsement concept, note whether key measures/tables carry it; this signals trustworthiness
  to both human users and AI-assisted query tools.
- **Natural-language example questions** — as part of the review output, draft 3-5 example
  questions a business user might ask against this model, and note whether the current model
  structure could answer them cleanly or would likely produce an ambiguous/wrong result.

## Output framing

Structure findings as gaps + one concrete fix suggestion each (e.g., "measure `Sales 2` has no
description and an unclear name relative to `Sales` — recommend renaming or adding a
description clarifying the difference") — actionable, not just descriptive. This skill doesn't
implement the fix, but a vague finding without a suggested direction is less useful than a
specific one.

## Don't overstate confidence

AI-readiness assessment involves some judgment calls (is this name "friendly enough"?). Present
findings as a practical review, not a certified score — avoid inventing a numeric readiness
score unless the user specifically asks for one and defines what scale they want.
