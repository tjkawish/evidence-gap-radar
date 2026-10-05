# Contributing

Thanks for helping improve Evidence Gap Radar. Contributions from researchers who aren't developers are especially welcome: better search strings and new presets are some of the most useful changes.

## Ways to help

- **Report a bug.** Open an issue using the *Bug report* template. Include your browser, the topic, rows and columns you used, and what you saw.
- **Suggest or fix a preset.** Open an issue using the *Preset request* template, or edit the `TOPICS` and `AXES` objects near the top of the `<script>` in `index.html`.
- **Improve search strings.** If a country, design or outcome query misses obvious literature, propose a better one. Explain why, ideally with before and after counts.
- **Improve the docs.** Clearer wording in the README or the methodology is always welcome.

## Development

There's no build step. The whole app is `index.html`.

```bash
git clone https://github.com/tjkawish/evidence-gap-radar.git
cd evidence-gap-radar
python3 -m http.server 8000   # then open http://localhost:8000
```

Edit `index.html`, reload the page and test.

### Guidelines

- Keep it a single, dependency-free HTML file. Load nothing except the Google Fonts already used.
- Respect API limits. NCBI allows 3 requests per second without a key. The `throttle()` helper enforces this, so don't bypass it.
- Keep colours in the CSS custom properties (`:root`) so both themes keep working.
- Test at phone width (about 400 px) and in dark mode.
- If you change the statistics, update `docs/METHODOLOGY.md` in the same pull request.
- If you change `index.html`, consider mirroring the change in `claude/evidence-gap-radar.html` (the claude.ai version), or note in the PR that it's standalone only.

### Preset format

Each axis preset is plain text, one item per line:

```
Label = PubMed query
```

Use field tags such as `[tiab]`, `[mh]`, `[pt]` and `[dp]`. Don't use `*` wildcards, because the radar rejects them to keep counts predictable.

## Pull requests

1. Fork the repo and create a branch: `git checkout -b feature/short-description`.
2. Make your change and test it in a browser.
3. Add a line under **Unreleased** in `CHANGELOG.md`.
4. Open a pull request describing what changed and why.

## Code of conduct

Be kind, assume good faith, and keep discussion focused on the work.
