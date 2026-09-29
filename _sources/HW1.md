# Homework 1: CRSP, the CAPM, and Fama-French

```{toctree}
:maxdepth: 1
notebooks/_02_CRSP_market_index_ipynb.ipynb
notebooks/_03_SP500_constituents_and_index_ipynb.ipynb
notebooks/_04_Fama_French_1993_ipynb.ipynb
notebooks/_06_CAPM_analysis_ipynb.ipynb
notebooks/_07_Fama_French_3_factor_ipynb.ipynb
```

- **Launched:** week 1. **Due:** Tuesday, October 20, at the start of the
  week 4 session. Also posted on Canvas.
- **Aim to finish by Tuesday, October 13,** the week 3 session. You get three
  weeks, but [HW 2](./HW2.md) launches in week 3 and is itself due two weeks
  after that. The third week here is slack, not the plan: spend it and you are
  carrying two assignments at once.
- **Link to Assignment:** a private copy is created for you in the
  [hw-finm-32800](https://github.com/hw-finm-32800) organization. The link to
  yours is on Canvas.
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

The four graded parts are worth 33 points in total, one per test. Every push
runs the suite on the course runner and posts your score to the repository's
Actions tab. Search the repository for `TODO` to find the blanks.

You will need the `wrds` package and a `.pgpass` file so that the pulls can
authenticate to WRDS without prompting; see the section "Creating a .pgpass
file" in [Connecting to the WRDS Platform With Python](notebooks/_01_wrds_python_package_ipynb.ipynb).

## Part 0 (not graded): background videos

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

## Part 1 (graded, 2 pts): GitHub Skills

Complete the following tutorials from the [GitHub Skills](https://skills.github.com/) page, using **public repositories** in your own GitHub account, and record the links in the assignment as instructed.

 - [Review pull requests](https://github.com/skills/review-pull-requests)
 - [Resolve merge conflicts](https://github.com/skills/resolve-merge-conflicts)

## Part 2 (graded, 19 pts): the market portfolio and the S&P 500

The CAPM's market portfolio is, in practice, a value-weighted index of all
stocks. You will replicate CRSP's value-weighted and equal-weighted market
indices from the monthly stock file, then reconstruct the S&P 500 from its
constituents two ways and see why only one of them is a tradable strategy.

- [HW Guide Part A: CRSP Market Returns Indices](notebooks/_02_CRSP_market_index_ipynb.ipynb)
- [HW Guide Part B: Reconstructing the S&P 500 Index](notebooks/_03_SP500_constituents_and_index_ipynb.ipynb)

## Part 3 (graded, 9 pts): Fama-French 1993

Before the construction details, understand what the factors are *for*. Two
companion notebooks run the asset-pricing analysis on the very portfolios your
pipeline produces:

 - [CAPM Analysis of Size, Value, and Investment Portfolios](notebooks/_06_CAPM_analysis_ipynb.ipynb): regress each portfolio's excess returns on the market factor and find the significant alphas the CAPM leaves behind.
 - [Fama-French 3-Factor Analysis](notebooks/_07_Fama_French_3_factor_ipynb.ipynb): add SMB and HML and test whether the multifactor model absorbs those alphas.

Then replicate the portfolio analysis from [Fama and French (1993)](https://www.jufinance.com/mag/fin534_16/Common_risk_factors_Fama_French_JFE1993.pdf), following the guide:

 - [HW Guide: Replicate Fama-French 1993](notebooks/_04_Fama_French_1993_ipynb.ipynb)

Fill in the code marked `TODO`. The blanks span the full pipeline, from query to automation:

 - **SQL queries** (`src/pull_CRSP_Compustat.py`): complete the WHERE clauses of the queries that pull Compustat fundamentals and the CRSP-Compustat link table. These are tested locally against a small in-memory database, without a WRDS connection, so you can iterate quickly. You must complete them before the automated download will work.
 - **Portfolio construction** (`src/shared_portfolio_sorts.py` and `src/calc_Fama_French_1993.py`): compute book equity, apply the common-stock and exchange screens, compute firm-level market equity, compute the NYSE breakpoints, and construct SMB and HML from the six size/book-to-market portfolios. The docstrings state what each function must produce; consult Fama and French (1993) and the CRSP/Compustat documentation linked in the code.
 - **Pipeline automation** (`dodo.py`): complete the PyDoit task definition `task_calc_Fama_French_1993` so that `doit` runs this stage with the correct file dependencies and targets. See [What is a task runner?](Week3/what_is_a_task_runner.md) and https://pydoit.org/tasks.html.

The reference factors and sorted portfolios are downloaded automatically from the [Kenneth French data library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html). The unit tests check that the series your code builds from raw CRSP and Compustat data statistically match them. The unit tests are the specification: when unsure what a function should produce, read its test in `src/test_*.py`.

## Part 4 (graded, 3 pts): the investment sort

Extend the pipeline to a second sort, portfolios formed on corporate investment (asset growth), as in the Fama-French five-factor model (Fama and French, 2015). Complete `src/calc_inv_portfolios.py` (the investment measure, the NYSE breakpoints, and the portfolio assignment) and `task_calc_inv_portfolios` in `dodo.py`. The tests in `src/test_calc_inv_portfolios.py` check your returns against the "Portfolios Formed on INV" data from the Ken French library.

This part reuses the functions you completed in Part 3 (the same book equity, market equity, and universe screens), which is the point: a well-structured pipeline makes the second sort dramatically cheaper than the first.
