# Output Templates — File Roles

Use the matching file under `assets/templates/` as the exact structure for each output file —
don't invent a different shape per task. Copy the template, fill it in, remove sections that
don't apply (note removed sections in `open-questions.md` if their absence reflects a real gap
rather than genuine non-applicability).

| Output file | Template | Produced when |
|---|---|---|
| `index.md` | [documentation-index.md](../assets/templates/documentation-index.md) | Always, for a full pass — links every other file |
| `model-overview.md` | [model-overview.md](../assets/templates/model-overview.md) | Always — purpose, storage mode, scale, business domains |
| `data-dictionary.md` | [data-dictionary.md](../assets/templates/data-dictionary.md) | Always — every table/column |
| `measures.md` | [measures.md](../assets/templates/measures.md) | Whenever measures exist |
| `relationships.md` | [relationships.md](../assets/templates/relationships.md) | Whenever more than one table exists |
| `ai-readiness-review.md` | [ai-readiness-review.md](../assets/templates/ai-readiness-review.md) | Only when explicitly requested |
| `open-questions.md` | (no fixed template — free-form list) | Whenever any gap/ambiguity/uncertainty was found during the pass |

## `open-questions.md` shape

No rigid template — but always structure entries as: what's unclear, why it's unclear, and
(if relevant) what would resolve it. Example:

```markdown
## Open Questions

- **Measure "Adj Factor" purpose unclear** — DAX multiplies by a hardcoded 1.075 with no
  comment or naming clue. Could be a currency adjustment, a tax rate, or a forecast buffer.
  Resolve by asking the model's business owner.
- **Column `Dim_Product[Flag2]` meaning unknown** — binary column, no description, not
  referenced by any measure or relationship in this export. May be unused/legacy.
```

## Naming and location convention

Emit all output files into a `docs/` folder (or wherever the user specifies) at the same
level, flat — don't nest by category unless the user's model is large enough that a flat
folder becomes hard to navigate (judgment call, not a fixed threshold).

## Don't pad output to look thorough

If a section genuinely doesn't apply (e.g., no calculation groups exist), omit that section
from `model-overview.md` rather than writing "N/A" placeholders throughout — a shorter, honest
document is more useful than a padded one.
