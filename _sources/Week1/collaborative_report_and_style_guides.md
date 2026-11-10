# The Collaborative Report and Style Guides

In [HW 4](../HW4.md) the class writes one publication-style report on the
economy and the financial markets, with about eighty authors. One coherent
document from that many hands depends on rules everyone follows, and the
report has three:

- **The code:** [`CODE_STYLE.md`](https://github.com/finm-32800/finm_midterm_report/blob/main/CODE_STYLE.md).
  One exhibit is two files that no one else edits, a script in
  `src/exhibits/` and its write-up in `reports/sections/`. Code that two
  exhibits share belongs upstream, in the pipeline that publishes the data.
- **The write-up and the page:** `reports/style_guide_report.pdf`.
- **The chart:** `reports/style_guide_charts.pdf`, applied in code by
  `src/chart_style.py`. A chart has no title inside the image, uses the
  series colors in order, and puts its units on the axis.

The two PDFs are built from their `.tex` sources in `reports/` when you run
`uv run doit` in the report repository.

Most organizations that publish recurring reports keep a style guide of this
kind, covering fonts, colors, chart conventions, and accessibility, so that
many contributors produce one document. Ours is modeled on these:

- [OFR Annual Report 2025](https://www.financialresearch.gov/annual-reports/files/OFR-AR-2025.pdf)
- [Federal Reserve 2024 Annual Report](https://www.federalreserve.gov/publications/files/2024-annual-report.pdf)
- [J.P. Morgan — Outlook 2025: Building on Strength](https://www.jpmorgan.com/content/dam/jpmorgan/documents/wealth-management/outlook-2025-building-on-strength.pdf)
- [J.P. Morgan — Mid-Year Outlook 2025](https://www.jpmorgan.com/content/dam/jpmorgan/documents/wealth-management/mid-year-outlook-2025.pdf)

The two J.P. Morgan outlooks are the closest models for the page and the
chart. A reviewer of your pull request checks your exhibit against these
guides, and you check theirs.
