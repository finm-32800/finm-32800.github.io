# Homework 1: CRSP, the CAPM, and Fama-French

```{toctree}
:maxdepth: 1
HW1_guide_A_github_skills.md
notebooks/_03_CRSP_market_index_ipynb.ipynb
notebooks/_04_SP500_constituents_and_index_ipynb.ipynb
notebooks/_05_Fama_French_1993_ipynb.ipynb
notebooks/_06_CAPM_and_Fama_French_ipynb.ipynb
```

- **Launched:** week 1. **Due:** Tuesday, October 20, at the start of the
  week 4 session. Also posted on Canvas.
- **Aim to finish by Tuesday, October 13,** the week 3 session. You get three
  weeks, but [HW 2](./HW2.md) launches in week 3 and is itself due two weeks
  after that. The third week here is slack, not the plan: spend it and you are
  carrying two assignments at once.
- **Link to Assignment:** a private copy is created for you in the
  [hw-finm-32800](https://github.com/hw-finm-32800) organization. The link to
  yours is on Canvas. **Your copy is created from your answer to the GitHub
  username survey on Canvas,** so fill that out first. You can open your copy
  once you accept the invitation that GitHub emails you.
- **Goes with:** weeks 1 and 2

## Overview

One assignment, two papers, one data family. The first half rebuilds the CRSP
market portfolio and the S&P 500 from their constituents. The second half
merges CRSP with Compustat and replicates the Fama-French three-factor model.
Together they tell one story: the CAPM says the market portfolio is the only
priced risk, and Fama and French (1993) show what the market portfolio leaves
unexplained and what fixes it. Week 1 teaches the first half and launches the
assignment; week 2 teaches the second half.

Everything runs as a pipeline: a pinned environment, automated WRDS pulls, and
unit tests that compare your series to published references. The way to work
is to run `pytest`, read the failing test, and write the code that passes it.
Run `doit` first so that the data is pulled before the tests run. Do not edit
the test files.

The assignment has five parts, A to E, and each has a guide, listed above.
Parts A to D are graded by the tests and are worth 34 points in total, one per
test. Part E has nothing to fill in and no points. Every push runs the suite on
the course runner and posts your score to the repository's Actions tab. Search
the repository for `TODO` to find the blanks.

You will need the `wrds` package and a `.pgpass` file so that the pulls can
authenticate to WRDS without prompting; see the section "Creating a .pgpass
file" in [Connecting to the WRDS Platform With Python](notebooks/_01_wrds_python_package_ipynb.ipynb).

## Before you start (not graded): background videos

CRSP and Compustat in WRDS:

 - [WRDS Web Queries](https://player.vimeo.com/video/1019787855?h=83ee06d532). We automate the query process with the [`wrds`](https://pypi.org/project/wrds/) package, but web queries are a good way to explore the data first.
 - [CRSP Coverage](https://vimeo.com/417302309)
 - [CRSP: Useful Variables](https://wrds-www.wharton.upenn.edu/pages/grid-items/crsp-useful-variables/). Covers points we made in class about cleaning CRSP (for example, negative prices).
 - [CRSP Stock Database Structure](https://wrds-www.wharton.upenn.edu/pages/grid-items/crsp-stock-database-structure/). Merging stock files and event files via SQL, which is how delisting returns are incorporated.
 - [Introduction to Compustat, Part 1](https://vimeo.com/417303405) and [Part 2](https://vimeo.com/1125031335). Slides are [here](https://wrds-www.wharton.upenn.edu/documents/1374/intro_comp_access_link.pptx).
 - [Merging CRSP and Compustat Data](https://vimeo.com/447503392)

Fama-French factor construction, if you want to see it done in SAS first:

 - [Fama-French SMB and HML: SAS Replication](https://vimeo.com/447603278)
 - [1. Book Equity](https://vimeo.com/447631819), [2. CRSP Stock Data](https://vimeo.com/447635241), [3. CRSP](https://vimeo.com/447867614), [4. Merge CRSP and Compustat, B/M Ratio](https://vimeo.com/447871296), [5. Calculating Fama-French Factors](https://vimeo.com/447876324)

## Part A (graded, 3 pts): GitHub Skills

Complete three tutorials from the [GitHub Skills](https://skills.github.com/)
page, using **public repositories** in your own GitHub account, and record the
links in `src/github_skills.py`.

- [HW 1 Guide A: GitHub Skills](./HW1_guide_A_github_skills.md)

## Part B (graded, 4 pts): the CRSP market index

The CAPM's market portfolio is, in practice, a value-weighted index of all
stocks. You will replicate CRSP's value-weighted and equal-weighted market
indices from the monthly stock file. Complete `src/calc_CRSP_indices.py`.

- [HW 1 Guide B: The CRSP Market Index](notebooks/_03_CRSP_market_index_ipynb.ipynb)

## Part C (graded, 15 pts): the S&P 500

You will reconstruct the S&P 500 from its constituents two ways and see why
only one of them is a tradable strategy. Complete the membership query in
`src/pull_SP500_constituents.py` and the index calculations in
`src/calc_SP500_index.py`.

- [HW 1 Guide C: Reconstructing the S&P 500](notebooks/_04_SP500_constituents_and_index_ipynb.ipynb)

## Part D (graded, 12 pts): constructing the Fama-French factors

You will merge CRSP with Compustat, replicate the size and value factors of
[Fama and French (1993)](https://www.jufinance.com/mag/fin534_16/Common_risk_factors_Fama_French_JFE1993.pdf),
and reuse the same pipeline for a second sort, on corporate investment.

- [HW 1 Guide D: Constructing the Fama-French Factors](notebooks/_05_Fama_French_1993_ipynb.ipynb)

Fill in the code marked `TODO`. The blanks span the full pipeline, from query to automation:

 - **SQL queries** (`src/pull_CRSP_Compustat.py`): complete the WHERE clauses of the queries that pull Compustat fundamentals and the CRSP-Compustat link table. These are tested locally against a small in-memory database, without a WRDS connection, so you can iterate quickly. You must complete them before the automated download will work.
 - **Portfolio construction** (`src/shared_portfolio_sorts.py` and `src/calc_Fama_French_1993.py`): compute book equity, apply the common-stock and exchange screens, compute firm-level market equity, compute the NYSE breakpoints, and construct SMB and HML from the six size/book-to-market portfolios. The docstrings state what each function must produce; consult Fama and French (1993) and the CRSP/Compustat documentation linked in the code.
 - **The investment sort** (`src/calc_inv_portfolios.py`): the investment measure (asset growth), its NYSE breakpoints, and the portfolio assignment, as in the Fama-French five-factor model (Fama and French, 2015). It reuses the book equity, market equity, and universe screens from above, which is the point: a well-structured pipeline makes the second sort much cheaper than the first.
 - **Pipeline automation** (`dodo.py`): complete the PyDoit task definitions `task_calc_Fama_French_1993` and `task_calc_inv_portfolios` so that `doit` runs these stages with the correct file dependencies and targets. See [What is a task runner?](Week3/what_is_a_task_runner.md) and https://pydoit.org/tasks.html.

The reference factors and sorted portfolios are downloaded automatically from the [Kenneth French data library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html). The unit tests check that the series your code builds from raw CRSP and Compustat data statistically match them. The unit tests are the specification: when unsure what a function should produce, read its test in `src/test_*.py`.

## Part E (not graded): testing the CAPM and the three-factor model

There is nothing to fill in. Once Part D works, `doit` runs
`src/calc_CAPM_and_FF3.py` on your portfolios. The guide shows what they are
for: whether the market is the tangency portfolio from HW 0, which alphas the
CAPM leaves behind, and how many of them SMB and HML absorb.

- [HW 1 Guide E: Testing the CAPM and the Three-Factor Model](notebooks/_06_CAPM_and_Fama_French_ipynb.ipynb)

For the theory behind it, without derivations, read
[From Mean-Variance to Factor Models](./Week3/from_mean_variance_to_factor_models.md).
