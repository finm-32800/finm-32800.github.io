# Homework 4

- **Due Date:** Friday, August 14, 2026 at 11:59 PM CT
- **Link to Assignment:** The GitHub Classroom link will be posted on Canvas.

```{note}
The yield-curve estimation content that previously formed Homework 4 has moved to
**[Homework 3](./HW3.md)**. Homework 4 is now a short options case study.
```

## Overview

Homework 4 is a deliberately minimal introduction to working with options data,
built from [finm-32900/case_study_options](https://github.com/finm-32900/case_study_options),
the pipeline we walk through in class. In class we review both of its case-study
notebooks; the homework repo itself contains only the SPX side:

- [Corporate Hedging](notebooks/_01_corporate_hedging_ipynb.ipynb) (class only): designing a
  hedging program for a firm exposed to oil prices, using Brent crude futures and
  options. The futures strips and option-chain snapshots ship with the case-study
  repo, so this notebook runs fully offline.
- [SPX Hedging](notebooks/_02_spx_hedging_ipynb.ipynb) (the homework): hedging an equity
  portfolio with S&P 500 index options — costless collars built from the
  market-implied delta map, IV surface, and a Monte Carlo simulation.

As in HW 3, the pipeline is orchestrated with `doit`, and this week's themes
(unit tests, documentation) run straight through it: your grade is determined by
a pytest suite, part of which runs as a GitHub Actions autograder on every push.

## What You Do

1. **Complete the WRDS OptionMetrics pull.** The SQL query in
   `src/pull_options_data.py` is blanked out; the TODO block spells out the
   required columns, tables, join keys, and filters (PM-settled SPX options,
   `am_settlement = 0`). Then run `doit pull_WRDS_options_data` (~200 MB;
   never commit the parquet).

2. **Extend the sample with Databento.** The OptionMetrics feed lags badly —
   the pulled data ends August 2023. You will close the gap by pulling recent
   PM-settled S&P 500 (SPXW) options from the OPRA feed via
   [Databento](https://databento.com), using your own free-trial API key.
   Three pieces are blanked out for you to complete:
   - the query fields in `src/pull_databento_options.py` (which dataset,
     schema, symbol root, and symbology — think about AM vs. PM settlement),
   - the OSI option-symbol parser,
   - the implied-volatility solver in `src/black_scholes.py` (OPRA carries
     prices only, so IV and greeks are computed, not vendor-supplied).

   The pull script is **cost-guarded**: it prices every query with a free
   `metadata.get_cost` call and refuses to download if the estimate exceeds
   `DATABENTO_MAX_COST` (default $3.00; the default two-week window costs
   about $2.50 of your $125 trial credits). Check with `--dry-run` first, and
   do not modify the guard.

3. **Make the tests pass.** `pytest ./src` runs the whole suite. Tests marked
   `requires_data` skip until the parquets exist; the autograder runs the
   no-data tests (including the SQL-completeness and Black-Scholes tests) on
   every push.

## Grading

| Component | Points |
|---|---|
| SQL query completeness (autograded) | 5 |
| Databento pipeline and Black-Scholes tests (autograded) | 3 |
| Full fast test suite (autograded) | 2 |
