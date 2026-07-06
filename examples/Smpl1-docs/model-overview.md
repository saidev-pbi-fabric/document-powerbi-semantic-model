# Model Overview — COVID Bakeoff

## Purpose

A COVID-19 analytics model tracking global and US case/death/vaccination trends, government
policy responses (stringency and mandate tracking), and correlating them against economic
(GDP) and demographic (population, age, temperature) context. *(Inferred from table content
and naming — no explicit model description metadata was present in the source.)*

## Storage mode

Import. Every table's partition is `mode: import`, sourced from a mix of public web data
(GitHub-hosted CSVs, Our World in Data, Google's mobility report), one private SQL database,
and local file imports. Two tables (`Days with restrictions`, `Days with restrictions grouped`)
are DAX-calculated tables (`SUMMARIZECOLUMNS`-based) rather than data-source-backed. `Dates` is
a `CALENDARAUTO()` calculated table.

## Scale

| | Count |
|---|---|
| Tables (excluding auto-generated date tables) | 11 |
| Auto-generated date tables (`DateTableTemplate_*`, `LocalDateTable_*`) | 2 |
| Measures | 27 |
| Relationships | 14 (3 bidirectional) |
| Hierarchies | 0 found |
| Calculation groups | 0 found |

## Business domains *(inferred from table names/content — not confirmed by a model owner)*

- **Global COVID metrics** — `OWID COVID data`: cases, deaths, testing, vaccinations by
  country/date (Our World in Data source).
- **US state-level data** — `Cases per US State`, `States`, `Lats`: state case counts,
  vaccination rollout, population, and geography/temperature context.
- **Country-level case counts & economics** — `Cases per country`, `Countries`, `GDP History`:
  case/death counts by country, joined to population/GDP context.
- **Government policy & mandates** — `CGRT Mandates`, `Govt Measures`, `Days with
  restrictions`, `Days with restrictions grouped`: policy stringency tracking (school
  closures, travel controls, etc.) and a separate government-measures dataset (ACAPS) with
  non-compliance-response categorization.
- **Calendar** — `Dates` (manually built, with Week Number and Start-of-week columns) plus
  Power BI's own auto-generated date tables for two datetime columns Power BI didn't map to
  the manual `Dates` table (`Govt Measures[Entry date]`).

## Key tables at a glance

| Table | Role | Notes |
|---|---|---|
| `OWID COVID data` | Fact | Global daily COVID metrics by country; visible, most-measure-dense table |
| `Cases per US State` | Fact | US state daily case/vaccination data; visible |
| `Cases per country` | Fact | Country daily case/death counts; **hidden**, sourced from a private SQL database |
| `CGRT Mandates` | Fact | Government policy stringency by country/date; **hidden** |
| `Govt Measures` | Fact | ACAPS government-measures dataset; **hidden**, one hidden measure |
| `Countries` | Dimension | Country attributes + 2 GDP measures (unusual — measures on a dimension table, see relationships review) |
| `States` | Dimension | US state attributes, several calculated grouping columns |
| `Dates` | Dimension (date table) | Manually built calendar |
| `GDP History` | Dimension/fact | Country-year GDP figures |
| `Lats` | Dimension | State lat/long/temperature lookup; **hidden** |
| `Days with restrictions` | Calculated | Per-country day-counts under each restriction type; **hidden** |
| `Days with restrictions grouped` | Calculated | Buckets the above into day-count ranges |

## Notable design choices

- **Measures live on a dimension table.** `Countries` carries `GDP % chg 2020` and `GDP Chg`,
  even though the GDP data itself is in `GDP History`. Works via a direct `CALCULATE`/filter
  on `GDP History`, but is an unusual placement worth knowing before extending the model.
- **Two date roles on `Govt Measures`.** The table has both `Date implemented` (related to the
  main `Dates` table) and `Entry date` (related via a column *variation* to an auto-generated
  local date table) — these appear to be two genuinely different business concepts (when a
  measure took effect vs. when the record was logged), not a modeling accident. See
  `open-questions.md`.
- **Real credentials/paths found in source, redacted in this documentation set per the
  skill's privacy rules** — a private SQL Server hostname and a local file path containing a
  personal Windows username appeared in several M partition queries. Generalized in
  `data-dictionary.md`; see that file's data-source notes.
