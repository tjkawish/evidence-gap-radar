# Changelog

All notable changes to this project are documented here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the project uses [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [1.0.0] - 2026-10-05

### Added
- Gap matrix with live PubMed counts for every row × column cell.
- Expected counts under independence (`row × column ÷ topic`) and Poisson z-score gap flags.
- Two colour modes: *vs expected* (diverging) and *paper count* (log scale).
- Ranked list of the biggest gaps, plus patterns across the map.
- Cell inspector showing the search string, a PubMed link and the top five PubMed papers with DOIs.
- Beyond-PubMed spot check against OpenAlex, or Paperguide in the claude.ai version.
- Google Scholar link for each cell.
- Presets:
  - topics: youth mental health, heat and pregnancy, zero-dose immunization, urban slums, maternal nutrition
  - axes: South Asian countries, Pakistan's provinces, high-burden LMICs, study designs, populations, outcomes, time periods
- Optional NCBI API key setting.
- Copy-as-table and CSV export with search strings.
- Built-in example map (a PubMed snapshot from 5 October 2026).
- claude.ai artifact version with PubMed and Paperguide connectors and Claude-drafted research questions.
- GitHub Pages deployment workflow.
