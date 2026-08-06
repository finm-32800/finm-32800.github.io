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
  market-implied delta map, IV surface, and a Monte Carlo simulation — then
  extending the stale vendor sample with CME E-mini S&P 500 options from
  Databento and validating the substitution on an overlap month.

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
   the pulled data ends August 2023. You will close the gap by pulling
   **E-mini S&P 500 options on futures** from CME Globex (`GLBX.MDP3`) via
   [Databento](https://databento.com), using your own free-trial API key.
   (SPX index options themselves live on the OPRA feed, which is metered and
   expensive; the E-mini complex tracks it closely — the notebook proves
   this on an overlap sample.) Three pieces are blanked out for you to
   complete:
   - the query constants in `src/pull_databento_options.py` (which dataset,
     schema, and parent symbols — think about which E-mini option roots
     continue the *PM-settled* series your WRDS query filters on: quarterly
     ES options are AM-settled, like the classic SPX monthlies),
   - the CME contract-symbol parser (`EW1N3 C4580` → root, month, year,
     type, strike — note the expiration *date* is not in the symbol),
   - the implied-volatility solver in `src/black_scholes.py` (the feed
     carries prices only, so IV and greeks are computed, not
     vendor-supplied — via Black-76 on the parity-implied futures price).

   The pull grabs the most recent two weeks **plus a fixed August-2023
   sample** that overlaps OptionMetrics; the notebook uses the overlap to
   validate the E-mini implied vols against the vendor's, contract by
   contract. It is **cost-guarded**: it prices every query with a free
   `metadata.get_cost` call and refuses to download if the estimate exceeds
   `DATABENTO_MAX_COST` (default $3.00; the default pull costs well under a
   dollar of your $125 trial credits). Check with `--dry-run` first, and do
   not modify the guard.

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
