# Evidence Gap Radar

**Find the under-studied corners of a research field in minutes, using live PubMed counts.**

Evidence Gap Radar crosses a research topic with two lists you choose. Rows might be countries, provinces or populations. Columns might be study designs, outcomes or time periods. The radar counts the PubMed records in every cell of that grid. It then compares each count with what you'd expect if the literature were spread evenly, and flags the cells that fall well short.

The result is a one-screen evidence map. It shows where a thesis, grant proposal or systematic review can add something new, and it backs that up with numbers and reproducible search strings.

![Evidence Gap Radar showing youth mental health research across six South Asian countries and seven study designs](docs/screenshot-light.png)

> **Live demo:** https://tjkawish.github.io/evidence-gap-radar/

---

## Why this exists

Most evidence and gap maps are built by hand over weeks: screening, coding and charting. That depth is needed for a published map. But at the start of a project you only need a quick, defensible answer to one question: **where is the evidence thin?**

The radar answers that question in a minute or two. It does three things a plain keyword search doesn't:

1. **It works in two dimensions.** You see every country × design (or population × outcome) combination at once, not one search at a time.
2. **It adjusts for size.** India will always have more papers than Sri Lanka. The radar compares each cell with an *expected* count, so a small country isn't flagged just because it's small.
3. **It checks itself.** For any flagged cell you can list the top PubMed papers, run a second search outside PubMed (OpenAlex) and open the same search in Google Scholar. That lets you confirm the gap is real before you build a proposal on it.

### Example finding (the built-in demo)

The demo crosses youth mental health with six South Asian countries and seven study designs. It's a real PubMed snapshot taken on 5 October 2026.

| | Found | Expected | Reading |
|---|---:|---:|---|
| Pakistan × Cross-sectional | 220 | ~62 | Over-represented (z = +19.9) |
| Pakistan × Cohort | 66 | ~114 | **Under-studied (z = −4.5)** |
| Bangladesh × Cohort | 34 | ~77 | **Under-studied (z = −4.9)** |
| Nepal × RCT | 5 | ~18 | **Under-studied (z = −3.1)** |
| Afghanistan × Economic evaluation | 0 | ~1.7 | No papers found |

Across the region, youth mental health research leans heavily on cross-sectional surveys. Longitudinal cohorts and trials are thin almost everywhere.

---

## Features

- **Gap matrix:** live PubMed counts for every row × column cell, coloured by how far each sits above or below its expected count, or by raw count.
- **Gap score:** each cell gets a Poisson z-score, and cells with z ≤ −2 get a dashed outline. See the [methodology](docs/METHODOLOGY.md).
- **Ranked gap list:** the biggest shortfalls in order, plus patterns across the whole map, such as "Cohort falls short in 4 of 6 countries".
- **Cell inspector:**
  - the exact search string, with a copy button and a link to open it in PubMed
  - the five most relevant PubMed papers, with DOIs
  - a **beyond-PubMed check** against OpenAlex (about 250 million works), which tags relevant papers that PubMed missed
  - a **Google Scholar** button that opens the same search in Scholar
- **Presets:**
  - topics: youth mental health, heat and pregnancy, zero-dose immunization, urban slums, maternal nutrition
  - rows and columns: South Asian countries, Pakistan's provinces, high-burden LMICs, study designs, populations, mental health outcomes, maternal and child outcomes, time periods
- **Fully editable:** every topic, row and column is plain PubMed syntax you can change.
- **Export:** copy the grid as a table, or download a CSV with every count, expected value, z-score and search string, ready for a methods appendix.
- **No install, no backend:** a single HTML file that runs in the browser and calls public APIs directly.
- **Light and dark themes** that work on phones.

---

## Quick start

### Option 1: open the file

1. Download or clone this repository.
2. Open `index.html` in any modern browser.
3. Press **Map the gaps**.

That's all. The page talks directly to NCBI E-utilities (PubMed) and OpenAlex. Both are free public APIs that need no login.

### Option 2: run a local server (optional)

```bash
git clone https://github.com/tjkawish/evidence-gap-radar.git
cd evidence-gap-radar
python3 -m http.server 8000
# open http://localhost:8000
```

### Option 3: use it inside claude.ai

`claude/evidence-gap-radar.html` is the version built as a Claude artifact. Inside claude.ai it searches through your own **PubMed** and **Paperguide** connectors. It can also ask Claude to **draft a research question** (population, intervention, comparison, outcome and design) for any gap.

---

## How to use it

1. **Pick a topic.** Choose a preset, or write your own PubMed query in the topic box.
2. **Pick rows and columns.** Choose presets, or write one item per line as `Label = PubMed query`, for example:
   ```
   Pakistan = Pakistan[tiab] OR Pakistan[mh]
   Cohort   = cohort studies[mh]
   2020–24  = 2020:2024[dp]
   ```
3. **Set filters.** *Humans only* adds `humans[mh]`. *From year* adds a publication-date floor.
4. **Map the gaps.** The radar runs `1 + rows + columns + rows × columns` searches. A 6 × 7 grid is 56 searches and takes about a minute.
5. **Read the map.** Orange cells have fewer papers than expected, teal cells have more, and dashed outlines are gaps.
6. **Inspect a gap.** Click a cell to see its papers, run the OpenAlex check and open it in Scholar.
7. **Export.** Download the CSV and keep it with your protocol.

### Optional: NCBI API key

Without a key, NCBI allows 3 requests per second, and the radar spaces its requests to match. A free [NCBI API key](https://www.ncbi.nlm.nih.gov/account/settings/) (created under your NCBI account settings) raises that to 10 per second, which makes big grids about 3× faster. Paste it under **NCBI API key** in the builder. It's stored only in your browser's local storage.

---

## Data sources

| Source | Used for | Access |
|---|---|---|
| [PubMed](https://pubmed.ncbi.nlm.nih.gov/) via [NCBI E-utilities](https://www.ncbi.nlm.nih.gov/books/NBK25501/) | All counts, top papers | Public API, no login |
| [OpenAlex](https://openalex.org/) | Beyond-PubMed spot check | Public API, no login |
| [Google Scholar](https://scholar.google.com/) | Manual cross-check (opens in a new tab) | Link only. Scholar offers no public API, so the radar doesn't scrape it |
| PubMed and Paperguide connectors | Same roles, inside claude.ai | Your own connectors |

Please cite the original articles by DOI, and follow the [NCBI usage guidelines](https://www.ncbi.nlm.nih.gov/books/NBK25497/) and the [OpenAlex terms](https://openalex.org/terms).

---

## Methodology in brief

For a topic with `T` records in total, a row with `R` records and a column with `C` records, the expected count in a cell is

```
E = R × C / T
```

This is the independence model behind a chi-square contingency table. The gap score is the Poisson z-score

```
z = (O − E) / √E
```

where `O` is the observed count. A cell is flagged when `z ≤ −2`, or when `O = 0` and `E ≥ 1`.

Full details, assumptions and caveats are in [docs/METHODOLOGY.md](docs/METHODOLOGY.md).

### Limitations

- **Counts are search hits, not screened studies.** Some hits will be off-topic, and some relevant studies will be missed.
- **MeSH indexing lags.** The newest papers are matched only on title and abstract, so recent years can look thinner than they are.
- **Vocabulary matters.** A low count can mean the search string misses that field's terms, not that the research doesn't exist.
- **PubMed covers biomedicine.** Social science and grey literature are under-represented, and that's what the OpenAlex and Scholar checks are for.
- **A flagged cell is a lead, not a finding.** Confirm it with a proper scoping search before you build on it.

---

## Deploy to GitHub Pages

This repository includes a workflow (`.github/workflows/pages.yml`) that publishes the site on every push to `main`.

1. Push the repository to GitHub.
2. Go to **Settings → Pages → Build and deployment** and set **Source** to **GitHub Actions**.
3. Push to `main`, or run the workflow from the **Actions** tab.
4. The site appears at `https://tjkawish.github.io/evidence-gap-radar/`.

---

## Project structure

```
evidence-gap-radar/
├── index.html                  # The app: standalone, single file (NCBI + OpenAlex)
├── claude/
│   └── evidence-gap-radar.html # claude.ai artifact version (PubMed + Paperguide connectors, Claude drafting)
├── docs/
│   ├── METHODOLOGY.md          # Statistics, assumptions, caveats
│   ├── screenshot-light.png
│   ├── screenshot-dark.png
│   └── screenshot-mobile.png
├── .github/
│   ├── workflows/pages.yml     # GitHub Pages deployment
│   └── ISSUE_TEMPLATE/         # Bug report and preset request templates
├── CHANGELOG.md
├── CITATION.cff
├── CONTRIBUTING.md
├── LICENSE
└── README.md
```

---

## Roadmap

- [ ] Trend view: one row × time periods as a sparkline per cell
- [ ] Save and share maps as a link
- [ ] Europe PMC as an extra count source
- [ ] Gap scores against a regional baseline instead of the global one
- [ ] More presets: NCDs, antimicrobial resistance, climate and health

Ideas and pull requests are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md).

---

## Citing

If the radar helps your work, please cite it. GitHub shows a **"Cite this repository"** button built from [`CITATION.cff`](CITATION.cff).

> Latif, T. (2026). *Evidence Gap Radar: live PubMed evidence-gap mapping* (Version 1.0.0) [Computer software]. https://github.com/tjkawish/evidence-gap-radar

---

## Author

**Tajamal Latif**, public health researcher, Islamabad, Pakistan.

## License

[MIT](LICENSE). Free to use, modify and share, with attribution.
