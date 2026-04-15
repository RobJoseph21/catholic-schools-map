# Data Dictionary

## schools.json

One record per Catholic school. Used for map dots and hover details.

| Field | Type | Source | Description |
|---|---|---|---|
| id | int | ACARA SML ID | Unique school identifier |
| name | string | School Profile | School name |
| suburb | string | School Profile | Suburb/locality |
| state | string | School Profile | State abbreviation (NSW, VIC, etc.) |
| postcode | string | School Profile | 4-digit postcode |
| lat | float | School Location | Latitude (GDA2020) |
| lon | float | School Location | Longitude (GDA2020) |
| type | string | School Profile | Primary / Secondary / Combined / Special |
| sector | string | School Profile | Catholic (all schools in this dataset are Catholic) |
| enrolments | int | School Profile | Total Enrolments headcount |
| fte_enrolments | float | School Profile | Full Time Equivalent Enrolments |
| teaching_staff | float | School Profile | FTE Teaching Staff |
| non_teaching_staff | float | School Profile | FTE Non-Teaching Staff |
| total_staff | float | Derived | teaching_staff + non_teaching_staff |
| icsea | int | School Profile | ICSEA score |
| capex_savings | float | DUMMY | Perturbed CAPEX savings ($). NOT REAL. |
| ctc_savings | float | DUMMY | Perturbed CTC/recurrent savings ($). NOT REAL. |
| total_savings | float | DUMMY | capex_savings + ctc_savings. NOT REAL. |
| diocese | string | Sector/Diocese lookup or spatial join | Catholic diocese name |
| ced | string | Spatial join | Federal electorate name (2025 boundaries) |
| sed | string | Spatial join | State electorate name (2025 boundaries) |
| lga | string | Spatial join | Local Government Area name |

## stats_{geography}.json

Pre-aggregated statistics per geographic unit. One file per geography level.

| Field | Type | Description |
|---|---|---|
| name | string | Geography name (e.g. "Epping", "NSW", "Sydney") |
| geo_type | string | Geography level (state / diocese / ced / sed / lga / sal) |
| schools | int | Count of Catholic schools |
| students | int | Total enrolments |
| staff | float | Total FTE staff (teaching + non-teaching) |
| catholic_pop | int | Catholic population from 2021 Census |
| capex_savings | float | DUMMY — aggregate CAPEX savings |
| ctc_savings | float | DUMMY — aggregate CTC/recurrent savings |
| total_savings | float | DUMMY — capex + ctc |

## Expected Totals (for validation)

| Metric | National Total | Source |
|---|---|---|
| Catholic schools | ~1,752 | ACARA (Catholic sector only) |
| CAPEX savings (dummy) | $2,747,870,034 | State totals match real data |
| CTC savings (dummy) | $5,623,012,719 | State totals match real data |

## CAPEX Savings by State (real totals, preserved in dummy data)

| State | CAPEX | CTC |
|---|---|---|
| ACT | $64,029,991 | $247,914,687 |
| NSW | $1,040,110,214 | $2,036,703,027 |
| NT | $9,187,780 | $24,497,918 |
| QLD | $517,902,084 | $933,804,523 |
| SA | $88,338,975 | $320,734,582 |
| TAS | $20,054,139 | $77,027,684 |
| VIC | $724,977,871 | $1,247,904,519 |
| WA | $283,268,980 | $734,425,779 |
