# Data Dictionary — Contoso CRM Sales & Service

Privacy note: every entity table in this model loads from the same local Excel workbook,
whose file path contains a personal Windows username. Generalized below (as
`<local-file-path>\Contoso - PowerBI Source Data.xlsx`) per this skill's privacy rules.

## Accounts

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
workbook, `AccountTbl` (generalized: `<local-file-path>\Contoso - PowerBI Source Data.xlsx`)

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Account Name | string | No | Company name |
| Street, City, State or Province, Postal Code, Country | string | No | Address fields (each tagged with a Power BI geography data category) |
| Latitude, Longitude | double | No | Geocoordinates |
| Phone | string | No | Contact phone |
| Annual Revenue, Number of Employees | int64 | No | Firmographic data |
| Account Owner | string | **Yes** | Owner name (text) — relationship key to `Owners`, see relationships review |
| AccountID | string | No | Business identifier |
| Industry Lookup | string (calc) | No | `LOOKUPVALUE` against `Industries` by `IndustrySeq` — a calculated redundant copy of `Industry` below |
| Territory, Region | string | No | **Not related to the `Territories` table** — see `open-questions.md` |
| AccountSeq | int64 | **Yes** | Surrogate key |
| IndustrySeq | int64 | **Yes** | FK to `Industries` |
| AccountOwnerSeq | int64 | No | Present but **not used in any relationship** — `Account Owner` (text) is the actual join key instead |
| Industry | string | No | Industry name (also available via `Industry Lookup` calculated column — redundant, see `open-questions.md`) |
| Account Number | string | No | Business identifier |

**Hierarchy:** `Location Hierarchy` — Country → State or Province → City → Street → Account Name.

## Cases

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per support case &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** local Excel workbook, `IncidentTbl`

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Case Created On | dateTime | **Yes** | Case creation date; relates to `Case Calendar` |
| Status, Agent, Title, Origin, Subject, Severity | string | No | Case classification fields |
| Is Escalated, Is SLA Violation | boolean | No | Flags feeding `Escalations`/`SLA Compliance` measures |
| CSAT Label | string | No | Raw satisfaction score label (source: `CustomerSatScore`, text before the delimiter kept as CSAT below) |
| CSAT | int64 | No | Numeric satisfaction score, parsed from `CustomerSatScore` |
| Resolution Minutes, Minutes to First Contact | int64 | No | Time-to-resolution metrics |
| Activities | int64 | No | Activity count on the case |
| SLA Compliance Goal (=0.90), Resolution Minutes Goal (=120), CSAT Goal (=3.75), Escalations Goal (=0.2) | calculated constants | No | Hardcoded target values, not sourced from data — see `open-questions.md` |
| Topic | string (calc) | No | `Products[Product]` looked up by `ProductSeq`, concatenated with `Subject` |
| CaseSeq | int64 | No | Surrogate key |
| SystemUserSeq | int64 | **Yes** | FK to `Owners` (via an inactive relationship — see relationships review) |
| AccountSeq | int64 | **Yes** | FK to `Accounts` |
| ProductSeq | int64 | **Yes** | FK to `Products` |

## Contacts

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
workbook, `ContactTbl`

| Column | Type | Hidden? | Description |
|---|---|---|---|
| ContactID | string | **Yes** | Business identifier |
| Contact, FirstName, LastName | string | No | Name fields |
| Job Title | string | No | |
| Street, City, State or Province, Postal Code, Country | string | No | Address |
| Latitude, Longitude, Phone | double/string | No | Geocoordinates, phone |
| ContactSeq | int64 | No | Surrogate key |
| AccountSeq | int64 | **Yes** | FK to `Accounts` |

## Industries *(hidden table)*

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
workbook, `IndustryTbl`

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Industry | string | No | Industry name |
| IndustrySeq | int64 | **Yes** | Surrogate key |

## Opportunities

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per sales opportunity &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** local Excel workbook, `OpportunityTbl`

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Budget, Value | int64 | No | Deal budget and value (currency-formatted) |
| Topic, Purchase Process, Rating | string | No | Deal classification |
| Decision Maker Identified | boolean | No | |
| Status | string | No | e.g. Open/Won/Lost — drives most measures |
| PipelineStep | string | No | Pipeline stage, encoded with a leading digit (e.g. "3-Pipeline") — parsed numerically in DAX (`VALUE(LEFT(...,1))`) |
| CloseDate | dateTime | **Yes** | Relates to `Opportunity Calendar` |
| Opportunity Created On | dateTime | **Yes** | Relates to an auto-generated date table (not `Opportunity Calendar`) |
| Weeks Open | int64 (calc) | No | Days between creation and close (or today, if still open), in weeks |
| DaysToClose | int64 | No | Sourced directly (not calculated from the two date columns) |
| Discount, Probability, Probability (raw) | double | No | Deal-level percentages |
| Days Remaining In Pipeline | int64 (calc) | No | Days until `CloseDate` for open deals, 0 otherwise |
| AccountSeq, ProductSeq | int64 | **Yes** | FKs to `Accounts`, `Products` |
| SystemUserSeq | string | **Yes** | FK to `Owners` (inactive relationship) |
| Opportunity Owner Name | string | No | Owner name (text) — not itself a relationship key here (Accounts' equivalent column is) |
| Product Name, Campaign Name, CampaignSeq | string | No | Denormalized product/campaign labels (also independently available via the `Products`/`Campaigns` relationships) |
| Blank | calc, always `BLANK()` | No | A column whose formula is literally `BLANK()` — see `open-questions.md` |

## Owners

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
workbook, `OwnerTbl`

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Sales owner | string | No | Owner name — the active join key from `Accounts` |
| Manager | string | No | Owner's manager |
| SystemUserSeq | int64 | **Yes** | ID-based join key to `Opportunities`/`Cases` — relationships exist but are **inactive** |

## Products

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
workbook, `ProductTbl`

| Column | Type | Description |
|---|---|---|
| Product category | string | Renamed from source `Product LOB` |
| Product | string | Product name |
| MinOppValue, MaxOppValue | decimal | Expected opportunity value range for the product |
| ProductSeq | int64 (hidden) | Surrogate key |

## Campaigns

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
workbook, `CampaignsTbl`

| Column | Type | Description |
|---|---|---|
| Type | string | Campaign type |
| Campaign Name | string | Renamed from source `Name` |
| Factor | int64 | Purpose not evident from name or usage — not referenced by any measure or relationship in this export |
| CampaignSeq | int64 (hidden) | Surrogate key |

## Territories *(hidden table, unrelated to anything else — see open-questions.md)*

**Role:** Dimension (orphaned) &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:**
local Excel workbook, `TerritoriesTbl`

| Column | Description |
|---|---|
| Region, Territory, Country, State Or Province Abbreviation | Geography/territory mapping — overlaps conceptually with `Accounts[Territory]`/`Accounts[Region]`, but no relationship connects them |

## Case Calendar / Opportunity Calendar (calculated date tables)

Both built via an identical DAX pattern (`CALENDAR` + `GENERATE`, scoped to each table's own
date range), producing: Date/DAY, DaySeq, YEAR, MONTH (+ number), YEAR MONTH (+ number), YEAR
WEEK, WEEK, RELATIVE WEEK/DAY/MONTH, RELATIVE 07/30 DAY PERIOD, and (Case Calendar only)
Weekday/WeekdaySeq. Not documented column-by-column here since both are generated, not
hand-authored data — see `model-overview.md` for their role.

## Opportunity Forecast Adjustment *(hidden, what-if parameter table)*

Single column `Forecast Adjustment` (source: `[Value]`), values -80 to 20 in steps of 10 (via
`GENERATESERIES(-80, 20, 10)`) — a what-if slicer input, not a data table. Feeds the hidden
`Forecast Adjustment Value` measure (`SELECTEDVALUE`, default 0), which in turn adjusts
`Opportunities[Revenue In Pipeline]`.
