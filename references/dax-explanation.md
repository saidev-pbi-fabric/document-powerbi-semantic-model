# DAX Explanation — Rules for Documenting Measures

## Order: purpose before mechanics

Every measure gets a one-sentence plain-language purpose statement first, then the mechanical
explanation, then the DAX itself. A reader who doesn't know DAX should get value from the first
sentence alone.

**Example shape:**
> **Total Sales YTD** — running total of sales revenue since the start of the current fiscal
> year, reset each year.
> Mechanically: sums `Sales[Amount]` filtered to dates between the fiscal year start and the
> current filter context's max date, using `DATESYTD` against the date table's fiscal-year
> boundary.
> ```dax
> Total Sales YTD = TOTALYTD(SUM(Sales[Amount]), 'Date'[Date], "6/30")
> ```

## Things to always call out when present

- **Filter context assumptions** — does this measure assume a specific filter is already
  applied (e.g., a single product selected) to make sense? State that assumption; a measure
  that returns a confusing total without expected context is a common support question.
- **Time intelligence** — `TOTALYTD`, `SAMEPERIODLASTYEAR`, `DATEADD`, `PARALLELPERIOD`, custom
  fiscal-year logic. State what period comparison is being made in plain terms ("same period,
  prior year" vs. "prior period of equal length" — these are genuinely different and easy to
  conflate).
- **Blank vs. zero behavior** — does the measure return `BLANK()` or `0` when there's no data
  in context, and does that matter for how it renders in a visual (e.g., a card showing "0"
  when there's really no data can mislead a viewer)? Note this explicitly if the DAX has
  visible `BLANK()`/`IF(ISBLANK(...))` handling, or if its absence looks like it could be a gap.
- **Dependencies between measures** — if a measure references another measure
  (`[Other Measure]` inside the expression), document that relationship rather than only
  explaining the referenced measure's logic inline every time it's used.
- **CALCULATE filter modifiers** — `ALL`, `ALLEXCEPT`, `REMOVEFILTERS`, `KEEPFILTERS`,
  `USERELATIONSHIP` all change what the measure sees versus the visual's own filter context.
  Explain what filter is being added or removed and why, not just that a modifier is present.

## Avoid pretending certainty

If a measure's business purpose isn't obvious from its name, DAX, and available context, say
so — write the best-effort technical description of what it calculates, and flag the business
"why" as unknown in `open-questions.md` rather than inventing a plausible-sounding purpose. A
wrong confident explanation is worse than an honest "purpose unclear from available context."

## Grouping for `measures.md`

Group measures by their `displayFolder` if one exists in the source; fall back to the business
domain grouping from [documentation-workflow.md](./documentation-workflow.md) step 3 if display
folders are missing or unhelpful (e.g., everything dumped in one folder). State which grouping
method was used.

## Complexity note

Don't attempt a token-by-token translation of deeply nested DAX. For a genuinely complex
measure (heavy variable use, nested `CALCULATE`, iterator functions like `SUMX`/`FILTER`
combinations), explain it in layers: purpose sentence, then a plain-English walk of what each
major `VAR` computes, then the full expression for reference — not a literal restatement of
DAX syntax in English.
