# Catholic Schools in Australia — Interactive Map

Self-contained, single-page interactive map of Catholic schools in Australia, with PDF export.

Currently deployed at: https://robjoseph21.github.io/catholic-schools-map/

---

## Folder structure

```
Website v2.0/
├── index.html                  Production website (currently deployed).
├── index_extended.html         Extended version: same as index.html plus 2 additional
│                               PDF info pages and a per-school table page in the PDF export.
├── CSNSW_logo.png              Coloured CSNSW logo (used in PDF header, header bar).
├── CSNSW_logo_mono.png         White CSNSW logo (used top-right of website nav bar).
├── CSNSW_logo_notext.png       CSNSW shield only (decorative background on info pages).
│
├── data/                       Runtime JSON data consumed by the website.
│   ├── DATA_DICTIONARY.md      Field definitions for schools.json and stats_*.json.
│   ├── schools.json            One record per Catholic school (1,771 schools).
│   └── stats_<geo>.json        Pre-aggregated stats per geography level (state, diocese,
│                               federal electorate, state electorate, LGA, suburb).
│
├── geo/                        Boundary GeoJSONs (~230 MB total).
│   ├── ced/, ced_all.json      Federal electorates (CED, 2025 boundaries).
│   ├── diocese/                Catholic dioceses (28 boundaries).
│   ├── lga/                    Local Government Areas.
│   ├── sal/                    Suburbs/localities.
│   ├── sed/                    State electorates (2025).
│   └── state/                  States and territories.
│
├── source/                     Source data for regenerating data/.
│   ├── Savings Extract.xlsx    Master spreadsheet (one row per school, all fields).
│   └── catholic_pop_reference/ Stats files used only for catholic_pop lookups
│                               (carried forward from a previous pipeline run that aggregated
│                               2021 ABS Census G14 by spatial join).
│
├── rebuild_data.py             Regenerate data/ from source/ files.
├── validate.py                 Sanity-check data/ totals against expected per-state values.
│
├── Backup/                     Snapshots of earlier index.html versions and design references.
│                               Internal — not needed at runtime.
│
└── Trial/                      Earlier PDF prototypes and original design screenshots.
                                Internal — not needed at runtime.
```

---

## Running the website

The site is a single-file SPA with no build step.

**Locally (simplest):**
- Open `index.html` directly in a browser. Some browsers (notably Chrome) block local `fetch()` calls — if the map appears blank, use the http server option below.

**Locally over http (recommended for development):**
```
python -m http.server 8000
```
Then visit `http://localhost:8000/`.

**Deploying:**
- Upload everything except `source/`, `Backup/`, `Trial/`, and the Python scripts to any static host (GitHub Pages, Netlify, S3, etc.).

---

## External dependencies (all CDN-hosted)

The website loads these from the public internet at runtime — no installation needed:

- Leaflet 1.9.4 (map rendering)
- Tom Select 2.3.1 (search dropdown)
- html2canvas 1.4.1 (PDF rendering — `index_extended.html` only)
- jsPDF 2.5.1 (PDF assembly — `index_extended.html` only)
- Lucide (icons)
- Google Fonts: Inter

If the deployment target is offline or behind a firewall, download these and update the `<script>`/`<link>` URLs in the HTML.

---

## Regenerating data

If `source/Savings Extract.xlsx` changes (e.g. new school year), regenerate the runtime JSON:

```
python rebuild_data.py
```

Requirements: Python 3.7+ with `openpyxl` (`pip install openpyxl`).

What it does: reads `source/Savings Extract.xlsx`, filters to `Sector == 'Catholic (incl. RI/MPJP)'`, joins with `source/catholic_pop_reference/` for catholic_pop counts, writes fresh `data/schools.json` and `data/stats_*.json`.

After regenerating, validate:
```
python validate.py
```
This checks per-state CAPEX and CTC totals match expected values. Exits 0 on success.

---

## index.html vs index_extended.html

Both versions read the same `data/` files and behave identically when browsing. The difference is in the PDF export:

| | `index.html` | `index_extended.html` |
|---|---|---|
| Page 1: map + summary stats | Yes | Yes |
| Page 2: "Catholic Education benefits all Australians" info page | No | Yes |
| Page 3: "Catholic Education saves taxpayers money" info page | No | Yes |
| Subsequent pages: per-school table | No | Yes (for diocese / electorate / LGA / suburb views) |

Pick whichever fits the use case. Both can coexist on the same site.

---

## Methodology notes

- **Capex savings per school** are distributed pro-rata by each school's Total Capital Expenditure 2022-24 within each (cohort × state) pot. Cohorts: Catholic = `Sector == 'Catholic (incl. RI/MPJP)'`, Independent = non-Catholic non-Government.
- **CTC (recurrent) savings per school** are distributed per-capita by enrolments within each (cohort × state) pot.
- **Staff and student numbers** are headcount, not FTE.
- Source pots are the user's manually-computed state×sector totals — these are real, not perturbed/dummy. (Some legacy doc references to "DUMMY" or "perturbed" data in `data/DATA_DICTIONARY.md` reflect an older v1 pipeline and do not apply to v2.0.)

To switch capex distribution method (e.g. back to per-capita, or 50:50 blend), edit the `'capex_savings'` value in `rebuild_data.py`'s `FIELD_MAP` to point at one of the alternative columns in `Savings Extract.xlsx`, then re-run `python rebuild_data.py`. The available columns are documented in the FIELD_MAP comment block.

---

## Git

The folder includes a `.git` directory with the full commit history from prior development. To check status:

```
git log --oneline
```

The remote (`origin`) points at https://github.com/RobJoseph21/catholic-schools-map.git
