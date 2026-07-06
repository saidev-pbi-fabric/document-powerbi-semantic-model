# Data Dictionary — COVID Bakeoff

Privacy note: two data sources in this model contained real, specific identifiers in their
source query text — a private SQL Server hostname and a local file path containing a personal
Windows username. Both are generalized below per this skill's privacy rules; see each
affected table's Source note.

## OWID COVID data

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per country per date &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** public CSV, Our World in Data
(`covid.ourworldindata.org`)

The largest table in the model — ~50 columns covering cases, deaths, testing, vaccination,
and country demographic/health context, sourced from the OWID public dataset.

| Column | Type | Hidden? | Description |
|---|---|---|---|
| iso_code | string | No | Country ISO code |
| continent | string | Yes | Continent name |
| location | string | No | Country name (as published by OWID) |
| date | dateTime | Yes | Record date |
| Total cases / New cases (+ smoothed variants) | int64/double | Yes | Cumulative and daily new confirmed cases |
| Total deaths / New deaths (+ smoothed variants) | int64/double | No | Cumulative and daily new deaths |
| Total/New cases/deaths per million | double | mixed (cases hidden, deaths not) | Population-normalized case/death metrics |
| Reproduction rate | double | No | Estimated effective reproduction number (R) |
| ICU patients / Hospital patients (+ per million) | double | No | Current hospital burden metrics |
| Weekly ICU/hospital admissions (+ per million) | double | No | Weekly admission counts |
| new_tests, total_tests, positive_rate, tests_per_case, tests_units | double/string | tests_per_case hidden | Testing volume and positivity metrics |
| total_vaccinations, people_vaccinated, people_fully_vaccinated, new_vaccinations(_smoothed) | double | No | Vaccination rollout counts |
| Total/People vaccinated per hundred, New vaccinations smoothed per million | double | No | Population-normalized vaccination metrics |
| stringency_index | double | No | Government stringency index (OWID's own composite) |
| Population, Population density, Median age | int64/double | No | Country demographics |
| Aged 65 and older | double | **Yes** | Population share 65+ |
| Aged 70 and old | double | No | Population share 70+ *(note: source column name is "Aged 70 and old", missing "er" — reproduced as-is from source, not a documentation typo)* |
| GDP per capita, Extreme poverty, Cardiovascular death rate, Diabetes prevalence, Female/Male smoking rate, Handwashing facilities availalbe, Hospital beds per thousand, Life expectancy, human_development_index | double | No | Country health/economic context, joined in from OWID's own combined dataset *(note: "availalbe" is a source-data typo, reproduced as-is)* |

## Cases per US State

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per US state per date &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** private SQL database (generalized:
`<private-sql-server>.database.windows.net`, database `<private-db>`) joined with OWID's
public US vaccinations CSV

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Key | string | No | Source system row key |
| Date | dateTime | No | Record date |
| CumulativeCases, CumulativeDeaths | int64 | No | Running totals |
| State | string | No | US state name |
| Incremental cases, IncrementalDeaths | int64 | No | Daily new counts |
| total_vaccinations, total_distributed, people_vaccinated, people_fully_vaccinated | int64 | No | Vaccination rollout counts, joined in from `us_state_vaccinations` |
| People fully vaccinated per hundred | double | **Yes** | Normalized full-vaccination rate |
| total_vaccinations_per_hundred, people_vaccinated_per_hundred, distributed_per_hundred | double | No | Normalized rollout metrics |
| daily_vaccinations(_raw)(_per_million) | double/int64 | No | Daily vaccination pace |
| share_doses_used | double | No | Share of distributed doses administered |

**Data quality note found in source:** the M query applies three manual point-fixes
(`Fix New York spike`, `Fix Minnesota spike`, `Fix New Jersey spiek` [sic]) that replace
specific incremental-case values on specific rows — evidence of manual outlier correction
baked into the ETL. Worth knowing before trusting incremental-case spikes/dips at face value
elsewhere in the model; see `open-questions.md`.

## Cases per country *(hidden table)*

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per country per date &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** private SQL database (generalized:
`<private-sql-server>.database.windows.net`, database `<private-db>`, view
`[COVID19].[vw_WHO_USAFacts_BingTracker_Data_Combined]`)

| Column | Type | Hidden? | Description |
|---|---|---|---|
| Key, GeoLevel | string | No | Source row key and geography level (filtered to `Country` rows only) |
| Date | dateTime | No | Record date |
| CumulativeCases, CumulativeDeaths, IncrementalCases, IncrementalDeaths | int64 | **Yes** | Case/death counts |
| Country | string | No | Parsed from `Key` (text after the underscore delimiter) |

## CGRT Mandates *(hidden table)*

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per country per date &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** derived from the `OxCGRT_latest` shared
expression (Oxford COVID-19 Government Response Tracker, public GitHub CSV)

All columns are hidden. Each is a human-readable categorical translation of a numeric OxCGRT
policy code (e.g. `School closures` = "No measures" / "Recommend closing" / "Require closing
some levels" / "Require closing all", derived from the raw `C1_School closing` code in
`OxCGRT_latest`). Columns: CountryName, CountryCode, Date, School closures, Workplace
closures, Cancelling public events, Restrictions on gathering, Public transport closures, Stay
at home requirements, Internal mov't restrictions, International travel controls, Face
coverings, Vaccination Policy.

## Govt Measures *(hidden table)*

**Role:** Fact &nbsp;·&nbsp; **Grain:** one row per government measure record &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** local Excel file (generalized:
`<local-file-path>\acaps_covid19_government_measures_dataset.xlsx`) — the ACAPS COVID-19
Government Measures dataset

All columns hidden. Key columns: ID, ISO, COUNTRY, REGION, Category/Measure/Specific Group
Targeted (what the measure was and who it targeted), Comments, NON_COMPLIANCE (raw
enforcement-consequence text), Date implemented, Entry date, Source/Source type/LINK. Three
calculated columns compute cross-table lookups: `Max Daily Vaccinations`, `Daily Cases When
Implemented`, `Change in cases after 60 days` (case trend 60 days after a measure took
effect). `Non compliance result` buckets the raw `NON_COMPLIANCE` text into a small fixed set
(Arrest, Deportation/entry refused, Fines, Legal, Not applicable) via a `SWITCH`.

## Countries

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** local Excel
file (generalized: `<local-file-path>\acaps_covid19_government_measures_dataset.xlsx`),
enriched with population/area/density joined in from a public web-scraped country-flags site

| Column | Type | Hidden? | Description |
|---|---|---|---|
| ISO | string | No | Country ISO code (relationship key to most fact tables) |
| Country | string | No | Country name |
| REGION | string | **Yes** | Raw source region |
| Continent | string | No | Derived region grouping (North America split out explicitly; else raw region) |
| Population, Growth Rate, Area (km2), Density | int64/double | No | Country demographics, web-scraped |
| Flag | string (Image URL) | No | Flag image URL |

## Dates

**Role:** Dimension (date table) &nbsp;·&nbsp; **Storage:** calculated (`CALENDARAUTO()`)

| Column | Description |
|---|---|
| Date | Key column |
| Week Number | `WEEKNUM(Dates[Date])` |
| Start of week | Calculated week-start date, **hidden** |

## GDP History

**Role:** Dimension/fact &nbsp;·&nbsp; **Grain:** one row per country per year &nbsp;·&nbsp;
**Storage:** Import &nbsp;·&nbsp; **Source:** local text file (generalized:
`<local-file-path>\WEO_History.txt`) — IMF World Economic Outlook data, unpivoted from a wide
year-columns layout

| Column | Type | Description |
|---|---|---|
| ISO, Country | string | Country identifiers |
| Year | int64 | Year |
| GDP (bn) | double | GDP in billions, constant prices |
| % change | double | Year-over-year GDP change |

## Lats *(hidden table)*

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import (embedded/compressed inline data, not an
external source query)

| Column | Type | Description |
|---|---|---|
| State | string | US state name |
| Latitude, Longitude | double | Geographic coordinates |
| Average Temperature | double | Average state temperature |

## States

**Role:** Dimension &nbsp;·&nbsp; **Storage:** Import &nbsp;·&nbsp; **Source:** OWID's public
US vaccinations CSV (state list only), enriched via joins to population and `Lats`

| Column | Type | Hidden? | Description |
|---|---|---|---|
| State | string | No | US state name |
| Total cases | int64 (calc) | **Yes** | `MAX` of cumulative cases from `Cases per US State` |
| State (by cases) | string (calc) | **Yes** | Same as `State`, sorted by `Total cases` — a sort-helper column |
| Population | double | **Yes** | State population |
| State (groups) | string (calc) | **Yes** | Buckets states into Islands/Territories vs. Main states |
| Flag | string (Image URL) | No | Flag image URL |
| State sorting | int64 (calc) | **Yes** | Manual sort-order override for a small subset of states |
| Focused on | string (calc) | **Yes** | Manually curated subset labeling ("Fully vaccinated" / "One dose" / "Other") — see `open-questions.md`, business rationale for this specific state selection is unclear |
| Latitude, Longitude | double | **Yes** | From `Lats` |
| Above or Below | string (calc) | **Yes** | Temperature above/below a fixed 67.7° threshold |
| March Temperatures | string (calc) | No | Cooler/Hotter state grouping by temperature |
| Average Temperature | double | **Yes** | From `Lats` |

## Days with restrictions *(hidden, calculated table)*

**Role:** Calculated (DAX `SUMMARIZECOLUMNS`, not data-source-backed) &nbsp;·&nbsp;
**Grain:** one row per country

All columns hidden. Counts, per country, how many recorded days each of 9 restriction types
(school closures, workplace closures, etc., mirroring `CGRT Mandates`' categories) was NOT
"No measures" — i.e., a per-country total of days under each restriction type over the
recorded period.

## Days with restrictions grouped

**Role:** Calculated (DAX `SUMMARIZECOLUMNS`, not data-source-backed) &nbsp;·&nbsp;
**Grain:** one row per country

Takes the day-counts from `Days with restrictions` and buckets each into a range label
(`<100 days`, `100-200 days`, `200-300 days`, `>300 days`).

## Auto-generated date tables (not user-authored, documented for completeness only)

`DateTableTemplate_3fa67ac2-...` and `LocalDateTable_88205f4b-...` are Power BI Desktop's own
automatic date/time hierarchy tables, silently created because `Govt Measures[Entry date]`
wasn't otherwise related to the manual `Dates` table. Not part of the intentional business
model — no further documentation produced for these.
