# Open Questions — Contoso CRM Sales & Service

- **`Territories` table is completely unrelated to the rest of the model.** Contains
  Region/Territory/Country data that overlaps conceptually with `Accounts[Territory]`/
  `Accounts[Region]`, but no relationship connects them. Unclear whether it's an unused
  leftover or meant to be joined manually/via a future relationship. Resolve by asking the
  model's author whether this table is still needed.
- **Owner relationship relies on a text-name match (`Account Owner`/`Sales owner`) while the
  ID-based equivalent (`SystemUserSeq`) exists but is inactive** on both `Opportunities` and
  `Cases`. Could be intentional (owner filtering only meant to flow through `Accounts`) or an
  oversight. See `relationships.md` for the full reasoning.
- **`Opportunities[Blank]` column's formula is literally `BLANK()`** — always evaluates to
  blank, on every row. Likely a template/placeholder column left over from report design
  (e.g., a spacer column for a matrix visual) rather than a data column with real content.
  Not treated as an error, just flagged as unusual.
- **`Opportunity Count In Pipeline`'s DAX has a stage filter commented out**
  (`-- && Opportunities[PipelineStep] IN {...}`), so the measure currently counts *all* open
  opportunities, not just later-stage ones the commented code implies was once intended.
  Whether this is a deliberate simplification or an accidentally-disabled filter isn't
  determinable from the code alone — worth confirming with whoever last edited this measure.
- **`Cases`' four goal columns (`SLA Compliance Goal`, `Resolution Minutes Goal`, `CSAT Goal`,
  `Escalations Goal`) are hardcoded constants** (0.90, 120, 3.75, 0.2), not sourced from any
  data table. Fine for a sample/demo model, but would need to become a real configurable
  table if reused for an actual team with different targets.
- **`Campaigns[Factor]` column exists but isn't referenced by any measure or relationship** in
  this export — purpose unclear from the name alone.
- **`Accounts[Industry Lookup]` (calculated) duplicates `Accounts[Industry]` (source column)**
  — both appear to resolve to the same industry name via different mechanisms (a `LOOKUPVALUE`
  formula vs. a column already present in the source data). Unclear which one is the "real"
  authoritative version or why both exist.
- **`AccountOwnerSeq` on `Accounts` and the `SystemUserSeq`-based owner relationships are both
  unused/inactive candidates** for what the text-based `Account Owner`/`Sales owner`
  relationship actually does — see `relationships.md`.
- **Scope of this pass**: full model — source inspection, data dictionary, measures,
  relationships. AI-readiness review was not run (not requested for this pass).
