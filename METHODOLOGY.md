# Methodology

This page explains how Evidence Gap Radar turns PubMed search counts into gap scores. It also covers what the scores can and can't tell you.

## 1. What gets counted

The radar builds four kinds of queries from your inputs. Here `B` is the topic query with the optional filters added:

```
B = (topic) [AND humans[mh]] [AND <year>:3000[dp]]
```

| Quantity | Query | Meaning |
|---|---|---|
| `T` | `B` | All records on the topic |
| `Rᵢ` | `B AND (rowᵢ)` | Topic records for row *i* (e.g. Pakistan) |
| `Cⱼ` | `B AND (colⱼ)` | Topic records for column *j* (e.g. cohort studies) |
| `Oᵢⱼ` | `B AND (rowᵢ) AND (colⱼ)` | Observed records in the cell |

A grid with *r* rows and *c* columns needs `1 + r + c + r·c` searches. Every count is the `count` that PubMed's `esearch` returns for the query. No records are downloaded or screened.

## 2. Expected count

If the topic's literature were spread across rows and columns independently, the share of row *i* that falls in column *j* would equal column *j*'s share of the whole topic:

```
Eᵢⱼ = Rᵢ × Cⱼ / T
```

This is the expected-frequency formula from a chi-square test of independence. In plain words: if cohort studies are 18% of all youth mental health papers, about 18% of Pakistan's youth mental health papers "should" be cohort studies too.

### Why the margins aren't summed from the grid

Rows and columns often overlap. A paper can mention both India and Nepal, or be both a cohort and a cross-sectional analysis. Many relevant papers also fall outside every listed row or column. So `Rᵢ` and `Cⱼ` are counted directly, not added up from the cells, and `T` is the whole topic. The baseline is therefore *the topic as a whole*, not just the rows and columns you listed.

## 3. Gap score

Each cell gets a Poisson z-score:

```
zᵢⱼ = (Oᵢⱼ − Eᵢⱼ) / √max(Eᵢⱼ, 0.5)
```

The floor of 0.5 stops division by very small expected values.

| Rule | Shown as |
|---|---|
| `z ≤ −2` | Gap (dashed outline) |
| `O = 0` and `E ≥ 1` | Gap with no papers (orange zero) |
| `z ≥ 2` | Over-represented |
| `O = 0` and `E < 1` | Not flagged, because even an even spread predicts less than one paper |

The **ranked gap list** shows cells with `z ≤ −1.5` and `E ≥ 2`, plus empty cells with `E ≥ 1`, sorted from the most negative z.

The **patterns** box flags a column when at least half the rows (and at least two) have `z ≤ −2` in it. It flags a column as over-represented when at least two-thirds of the rows have `z ≥ 2`.

### Colour scale

In *vs expected* mode, cell colour encodes `log₂((O + 0.5) / (E + 0.5))`, clamped to ±3 (an 8-fold difference). Orange means below expected and teal means above. In *paper count* mode, colour encodes `log₁₀(O + 1)` scaled to the largest cell.

## 4. Why a z-score and not just "zero papers"

Looking only for empty cells misses most real gaps. In the demo map, Pakistan has 66 youth mental health cohort studies. That isn't zero, but it's well short of the ~114 expected from the share cohorts take up in the field worldwide. Raw counts also mislead: Sri Lanka's 4 RCTs and India's 98 RCTs mean little until you scale each by how much research the country produces overall. The expected count does that scaling.

## 5. Assumptions and caveats

1. **Independence is a baseline, not a target.** Some combinations *should* be rare. Few economic evaluations exist in any field, and some designs suit some settings badly. A negative z says "less than the global mix", not "too little".
2. **The baseline is global.** A South Asian country is compared with the field worldwide, which is dominated by high-income countries. A low z for RCTs may reflect a funding gap rather than a choice of research question. Both are worth knowing.
3. **Search hits aren't studies.**
   - Country terms in `[tiab]` also match papers that only mention the country, such as a single site in a multi-country trial.
   - `[mh]` terms only match indexed records. MeSH indexing lags by weeks to months, so recent years undercount.
   - Publication-type tags (`[pt]`) depend on indexing too.
4. **Poisson noise is a rough guide.** Publication counts are over-dispersed, because papers cluster by research group, cohort and funding cycle. Treat |z| < 3 as suggestive.
5. **One database.** PubMed under-represents social science, education, economics and non-English literature. The OpenAlex spot check and the Google Scholar link exist for this reason.

## 6. The beyond-PubMed check

When you open a cell, the radar sends a plain-language query (`"<column> <row> <topic name>"`) to **OpenAlex**, or to **Paperguide** inside claude.ai. It takes the top 20 results and marks a result as *on target* if its title or abstract mentions both:

- the row label (e.g. "Pakistan"), and
- the column label or a synonym (e.g. cohort → cohort, longitudinal, prospective).

On-target results whose DOI isn't among the PubMed papers listed above are tagged as possibly missing from PubMed. If several such papers turn up, the gap may be partly a database artefact.

This is a relevance spot check, not a second count. Neither OpenAlex search nor Paperguide is built for exact Boolean counting.

## 7. Reproducibility

Every count comes from a query you can see, copy and re-run. The CSV export includes the full query for each cell. PubMed counts change as records are added and indexed, so record the date of your run, which is shown above the matrix and saved in your browser.

## 8. Suggested reporting text

> We used Evidence Gap Radar (Latif, 2026) to map PubMed records on [topic] across [rows] and [columns] on [date]. Expected cell counts were calculated under independence (row total × column total ÷ topic total). Cells with a Poisson z-score ≤ −2 were treated as candidate gaps and checked against OpenAlex and Google Scholar. Search strings for every cell are provided in Supplementary File X.
