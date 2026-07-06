# Open Questions — COVID Bakeoff

- **`Change in cases WoW%` caps its result at 500% (`min(..., 5)`).** Plausibly a guard
  against extreme spikes when the prior-week denominator is near zero, but the specific cap
  value isn't explained in the source. Resolve by asking the model's author, if reused beyond
  this sample.
- **`GDP Chg` is an exact duplicate of `GDP % chg 2020`** (a straight pass-through reference).
  Unclear whether this is a leftover from renaming, a deliberate alias for a specific visual's
  label requirement, or unused. Flagged rather than assumed.
- **`Cumulative cases per 100` averages two already-aggregated columns** rather than summing
  raw values before dividing (`AVERAGE(CumulativeCases) / AVERAGE(Population) * 100`). This
  produces a correct rate only when the filter context resolves to exactly one state; in a
  multi-state selection this would silently produce a different number than a true "sum of
  cases / sum of population" rate would. Not confirmed whether this measure is only ever used
  in single-state visual contexts — worth checking before reusing it elsewhere.
- **Two separate date roles on `Govt Measures`** (`Date implemented` related to the main
  `Dates` table, `Entry date` related via a column variation to an auto-generated local date
  table) appear to reflect a genuine business distinction (when a policy took effect vs. when
  the record was logged) rather than a modeling oversight — but this wasn't confirmed with a
  model owner, just inferred from the column names and the fact both relationships were kept
  as active, deliberate-looking configurations.
- **`States[Focused on]` manually labels only 6 specific states** ("Fully vaccinated": New
  Mexico, Rhode Island, South Dakota; "One dose": Connecticut, Massachusetts, New Hampshire;
  everything else "Other") with no comment explaining the selection criteria or point-in-time
  basis for those specific groupings. Likely tied to a specific narrative/visual in the
  original report (a "states to watch" callout), not derivable from the data alone.
- **`Cases per US State`'s M query hand-corrects 3 specific data points** (a "New York spike,"
  "Minnesota spike," and "New Jersey spike" replaced with specific different values) with no
  documented source for why those particular corrections were needed. Treat this table's
  incremental-case figures as having undergone undocumented manual cleaning for at least those
  3 state/value combinations.
- **Scope of this pass**: full model — source inspection, data dictionary, measures,
  relationships. AI-readiness review was not run (not requested for this pass).
