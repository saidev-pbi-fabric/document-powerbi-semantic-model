# Model Overview — Contoso CRM Sales & Service

## Purpose

A CRM analytics model (Contoso sample data) covering the sales pipeline (Opportunities,
Accounts, Contacts, Products, Campaigns) and customer service (Cases, CSAT/SLA tracking).
*(Inferred from table/measure content — entity naming (Accounts, Opportunities, Cases,
SystemUserSeq) matches a Dynamics-style CRM data shape; no explicit model description metadata
was present.)*

## Storage mode

Import. All entity tables load from a single local Excel workbook (multiple named tables
within it). Two tables (`Case Calendar`, `Opportunity Calendar`) are DAX-calculated
(`CALENDAR`/`GENERATE`-based, not `CALENDARAUTO`) with rich relative-date columns (RELATIVE
DAY/WEEK/MONTH). `Opportunity Forecast Adjustment` is a calculated what-if parameter table
(`GENERATESERIES`).

## Scale

| | Count |
|---|---|
| Tables (excluding auto-generated date tables) | 12 |
| Auto-generated date tables (`DateTableTemplate_*`, `LocalDateTable_*`) | 5 |
| Measures | 20 |
| Relationships | 14 (2 inactive) |
| Hierarchies | 1 (`Accounts[Location Hierarchy]`) |
| Calculation groups | 0 found |

## Business domains *(inferred — not confirmed by a model owner)*

- **Sales pipeline** — `Opportunities`, `Accounts`, `Contacts`, `Products`, `Campaigns`,
  `Owners`, `Opportunity Calendar`, `Opportunity Forecast Adjustment`: pipeline value, win/loss
  tracking, forecasting with a what-if adjustment parameter.
- **Customer service** — `Cases`, `Case Calendar`: case volume, SLA compliance, CSAT, and
  escalation tracking.
- **Shared reference** — `Industries`, `Territories` (**note:** `Territories` has no
  relationship to anything else in the model — see `open-questions.md`).

## Key tables at a glance

| Table | Role | Notes |
|---|---|---|
| `Opportunities` | Fact | Sales pipeline; visible; richest measure set alongside `Cases` |
| `Cases` | Fact | Support cases; visible; CSAT/SLA/escalation measures |
| `Accounts` | Dimension | Company accounts; has an address `Location Hierarchy` |
| `Contacts` | Dimension | Individual contacts at accounts |
| `Products` | Dimension | Product catalog with min/max opportunity value bounds |
| `Owners` | Dimension | Sales reps/managers; carries the `Rev Goal` measure |
| `Campaigns` | Dimension | Marketing campaign types |
| `Industries` | Dimension | **Hidden**; industry lookup |
| `Territories` | Dimension | **Hidden**; region/territory/country lookup — **unrelated to any other table** |
| `Case Calendar` | Date table | Calculated, built from `Cases[Case Created On]` range |
| `Opportunity Calendar` | Date table | Calculated, built from `Opportunities[CloseDate]` range |
| `Opportunity Forecast Adjustment` | Parameter | What-if slicer input (-80 to +20 in steps of 10), drives `Revenue In Pipeline`'s adjustment |

## Notable design choices

- **Two calendar tables, both hand-built with elaborate relative-date columns** (RELATIVE
  DAY/WEEK/MONTH/07-DAY-PERIOD/30-DAY-PERIOD) rather than one shared date table — `Case
  Calendar` scoped to case-created dates, `Opportunity Calendar` scoped to opportunity
  close dates. Each of `Opportunity Calendar`'s own date-shaped columns (`Date`, `DAY`,
  `WEEK`, `RELATIVE DAY`) has its own Power-BI-auto-generated date table behind it —
  explaining 4 of the 5 auto date tables in this model.
- **Owner relationship is name-based, not ID-based, in its active form.** `Accounts[Account
  Owner]` → `Owners[Sales owner]` (both text) is active; the ID-based
  `Opportunities/Cases[SystemUserSeq]` → `Owners[SystemUserSeq]` relationships are both
  **inactive**. See `relationships.md` for why this is worth a second look.
- **A what-if parameter table drives a real measure.** `Opportunity Forecast Adjustment`
  (a `GENERATESERIES` parameter) feeds `Revenue In Pipeline`'s adjustment factor via
  `SELECTEDVALUE` — a deliberate what-if slicer pattern, not a data table.
- **Real local file path with a personal Windows username found in every table's source
  query, generalized in this documentation per the skill's privacy rules** — see
  `data-dictionary.md`'s Source note.
