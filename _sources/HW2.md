# Homework 2


```{toctree}
:maxdepth: 1
notebooks/_04_Fama_French_1993_ipynb.ipynb
notebooks/_06_CAPM_analysis_ipynb.ipynb
notebooks/_07_Fama_French_3_factor_ipynb.ipynb
```

- **Due Date:** Friday, July 17, 2026 at 11:59 pm CT.
- **Link to Assignment:** TBD


## Part 0 (not graded)

Please watch the following videos to better familiarize yourself with CRSP and Compustat in WRDS.

 - Watch the following videos about Compustat. The slides to accompany the videos are available [here](https://wrds-www.wharton.upenn.edu/documents/1374/intro_comp_access_link.pptx).
   - [Introduction to Compustat | Part 1](https://vimeo.com/417303405)
   - [Introduction to Compustat | Part 2](https://vimeo.com/1125031335)
   - [Merging CRSP and Compustat Data](https://vimeo.com/447503392)

 - You may also find the following videos helpful:
   - [Fama-French SMB and HML | SAS Replication](https://vimeo.com/447603278)
   - [Fama-French SMB and HML | 1. Book-Equity](https://vimeo.com/447631819)
   - [Fama-French SMB and HML | 2. CRSP Stock Data](https://vimeo.com/447635241)
   - [Fama-French SMB and HML | 3. CRSP](https://vimeo.com/447867614)
   - [Fama-French SMB and HML | 4. Merge CRSP and Compustat. B/M Ratio](https://vimeo.com/447871296)
   - [Fama-French SMB and HML | 5. Calculating Fama-French Factors](https://vimeo.com/447876324)

## Background: Why These Portfolios? CAPM and Multifactor Models

Before diving into the construction details, understand what the factors and sorted portfolios are *for*. The CAPM says market beta is the only priced risk, so every portfolio's alpha should be zero. It isn't: portfolios sorted on size, book-to-market, and investment earn returns that market beta cannot explain, and those anomalies are exactly what motivated Fama and French to add the SMB and HML factors. Two companion notebooks run this asset-pricing analysis on the very portfolios your pipeline will produce:

 - [CAPM Analysis of Size, Value, and Investment Portfolios](notebooks/_06_CAPM_analysis_ipynb.ipynb): regress each portfolio's excess returns on the market factor and find the significant alphas the CAPM leaves behind
 - [Fama-French 3-Factor Analysis](notebooks/_07_Fama_French_3_factor_ipynb.ipynb): add SMB and HML and test whether the multifactor model absorbs those alphas

## Part 1 (graded)

In Part 1, you will replicate portfolio analysis from the seminal paper [Fama and French (1993)](https://www.jufinance.com/mag/fin534_16/Common_risk_factors_Fama_French_JFE1993.pdf). A guide to this replication is available here:

 - [HW Guide: Replicate Fama-French 1993](notebooks/_04_Fama_French_1993_ipynb.ipynb)

Your job is to fill in the missing code (marked with `TODO`) to complete the replication. The blanks span the full data pipeline, from query to automation:

 - **SQL queries** (`src/pull_CRSP_Compustat.py`): Complete the WHERE clauses of the queries that pull Compustat fundamentals and the CRSP-Compustat link table from WRDS. These are tested locally (without a WRDS connection) by running your queries against a small in-memory database, so you can iterate quickly. Note that you must complete these queries before the automated data download will work.
 - **Portfolio construction** (`src/shared_portfolio_sorts.py` and `src/calc_Fama_French_1993.py`): Compute book equity, apply the common-stock and exchange screens, compute firm-level market equity, compute the NYSE breakpoints, and construct the SMB and HML factors from the six size/book-to-market portfolios. The docstrings state what each function must produce; consult Fama and French (1993) and the CRSP/Compustat documentation linked in the code for the methodology.
 - **Pipeline automation** (`dodo.py`): Complete the PyDoit task definitions (`task_calc_Fama_French_1993` and `task_calc_inv_portfolios`) so that `doit` runs the full pipeline with correct file dependencies and targets. See [What is a task runner?](Week3/what_is_a_task_runner.md) and https://pydoit.org/tasks.html.

The actual Fama-French factors and sorted portfolios are downloaded from the [Kenneth French data library](https://mba.tuck.dartmouth.edu/pages/faculty/ken.french/data_library.html). The HW code is already configured to automatically download these reference data (the precomputed factors and sorted portfolios from the Ken French data library). However, you will also download the raw underlying data from CRSP and Compustat via WRDS and construct the Fama-French factors and sorted portfolios from the raw data yourself. The unit tests will check that your code produces time series for the factors and portfolio returns that statistically match the reference data from the Kenneth French data library.

The unit tests are the specification: when you are unsure exactly what a function should produce, read its test in `src/test_*.py`. Do not modify the test files.

## Part 2 (graded)

In Part 2, you will extend the pipeline to a second portfolio sort: portfolios formed on corporate investment (asset growth), as in the Fama-French five-factor model (Fama and French, 2015). Complete the missing code in `src/calc_inv_portfolios.py`: the investment measure, the NYSE breakpoints, and the portfolio assignment. The module docstring describes the methodology, and the unit tests in `src/test_calc_inv_portfolios.py` check your portfolio returns against the "Portfolios Formed on INV" data from the Ken French data library.

This part reuses the functions you completed in Part 1 (the same book equity, market equity, and universe screens), which is the point: a well-structured pipeline makes the second sort dramatically cheaper to build than the first.

**Citations:**

 - Eugene, Fama, and Kenneth French. "The cross-section of expected stock returns." Journal of Finance 47, no. 2 (1992): 427-465.
 - Fama, Eugene F., and Kenneth R. French. "Common risk factors in the returns on stocks and bonds." Journal of financial economics 33, no. 1 (1993): 3-56.
 - Fama, Eugene F., and Kenneth R. French. "A five-factor asset pricing model." Journal of financial economics 116, no. 1 (2015): 1-22.

## Reminder

Do not make any changes to the unit test files. If an edit is made, you will be required to edit the history of your commits to remove any trace of the edits to these files.
