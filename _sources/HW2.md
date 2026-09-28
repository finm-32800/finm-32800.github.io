# Homework 2: The Yield Curve and the Policy Path

```{toctree}
:maxdepth: 1
notebooks/_01_CRSP_treasury_overview_ipynb.ipynb
notebooks/_02_replicate_GSW2005_ipynb.ipynb
notebooks/_01_fed_funds_futures_data.ipynb
notebooks/_02_fedwatch_replication.ipynb
```

- **Launched:** week 3. **Due:** week 5. The date is posted on Canvas.
- **Link to Assignment:** TBD
- **Goes with:** week 3

## Overview

Two views of the same object, the term structure of interest rates, from two
data sources, published as one website. First you estimate the U.S. Treasury
yield curve with the Nelson-Siegel-Svensson model, following
[Gürkaynak, Sack, and Wright (2006)](https://www.federalreserve.gov/pubs/feds/2006/200628/200628abs.html),
the methodology behind the Federal Reserve's daily published curve. Then you
read the market's expected path for the policy rate out of 30-Day Fed Funds
futures, the way the CME FedWatch tool does. Finally you put the two on the
same axes: the futures-implied expected path against the short end of the
forward curve you fitted, on the same date. They should nearly agree at the
front and drift apart; the gap is the term premium plus the spread between fed
funds and Treasuries, and explaining it is the last task.

The pipeline pulls from four sources: CRSP Treasury quotes from WRDS, the
Fed's published GSW series, ZQ futures from Databento (the pull is cost-guarded
and free under the course license), and the effective fed funds rate from
FRED. The deliverable is a [ChartBook](https://pypi.org/project/chartbook/)
site deployed to GitHub Pages, the same publishing workflow your final project
will use.

## Learning Outcomes

- Estimate the Treasury yield curve with the Nelson-Siegel-Svensson model, using the [`finm`](https://jeremybejarano.com/finm/) package for spot rates, discount factors, cashflows, and fitting.
- Understand how fed funds futures prices imply an expected policy path and FOMC meeting-outcome probabilities.
- Relate the futures-implied path to the forward curve, and explain the gap.
- Build and verify a multi-source pipeline with `doit`, and manage API keys with `.env` files.
- Generate a ChartBook site from the pipeline's dataframes and charts, and publish it to GitHub Pages.

## Part 1 (graded): the yield curve

- **Task 1: GSW yield curve module (2 pts).** Replace the `TODO`/`NotImplementedError` placeholders in `src/gsw2006_yield_curve.py` with imports from the `finm.fixedincome` module (`spot`, `discount`, `calc_cashflows`, `fit`, `gurkaynak_sack_wright_filters`). The unit tests are the specification.
- **Task 2: data pipeline (3 pts).** Make the full pipeline run with `doit`, so that the data pulls, the model fit, and the notebooks complete and produce their targets.
- **Task 3: Federal Reserve yield curve data (1 pt).** Implement `task_pull_fed_yield_curve_data` in `dodo.py`, which downloads the Fed's published GSW series and produces `fed_yield_curve.parquet`.
- **Task 4: notebooks (1 pt).** Ensure the yield-curve notebooks execute as part of the pipeline.

## Part 2 (graded): the policy path

- **Task 5: the FedWatch math (2 pts).** Three functions in `src/fedwatch.py` have had their bodies replaced with `raise NotImplementedError(...)`: `implied_rate`, `solve_post_meeting_rate`, and `move_probability`. Each docstring specifies what the function must do, and the [replication notebook](notebooks/_02_fedwatch_replication.ipynb) derives the same formulas step by step. Your feedback loop runs offline, with no API key: `pytest -vv ./src/test_fedwatch.py`. Once those tests pass, add your Databento key to `.env` and run `doit`.
- **Task 6: the bridge (2 pts).** Complete the notebook that plots the futures-implied expected average fed funds rate for the coming meeting months against the instantaneous forward curve from your fitted NSS parameters on the same date, and write the paragraph that explains the difference.

## Part 3 (graded): publish the site (2 pts)

The pipeline registers its outputs (the consolidated CRSP Treasury data, the Fed's GSW series, the ZQ implied rates, and the meeting probabilities) as dataframes and charts in `chartbook.toml`.

1. Build the site with `chartbook build -f`, which generates the `docs/` folder.
2. Create a **new, separate public repository under your personal GitHub account** to host the site. Do **not** make your assignment repo public and do not enable Pages on it: it contains your graded solutions, tests, and git history. On the free tier, Pages only works on public repositories, which is why the `docs/` folder is gitignored in the assignment repo.
3. Copy the built site into the new repo and push it:

   ```bash
   git clone https://github.com/<your-username>/finm32800-hw2-site.git
   cp -R <your-hw2-repo>/docs/. finm32800-hw2-site/
   cd finm32800-hw2-site
   git add .
   git commit -m "Publish ChartBook site"
   git push
   ```

   The `docs/.` form, with the trailing `/.`, matters: it copies hidden files such as `.nojekyll`, which the site needs to render correctly.

## Warnings

- Your Databento and WRDS credentials go in `.env`, which is gitignored. If a key ever appears in a commit, revoke it and generate a new one.
- Do not edit the test files (`test_*.py`).
