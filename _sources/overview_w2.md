# Week 2: Virtual Environments, WRDS, and CRSP

```{toctree}
:maxdepth: 1
Week2/WRDS_intro_and_web_queries.md
notebooks/_01_wrds_python_package_ipynb.ipynb
notebooks/_05_basics_of_SQL_ipynb.ipynb
Week2/env_files.md
```


## Announcements

- **Final Project Partners**: Start thinking about who you'd like to partner with for the final project. I will post the project options later this week. Projects will be completed in pairs.


## Catch-up from Week 1

We didn't get to everything last week, so we'll start by closing those gaps.

- **In-class examples repo**: Clone <https://github.com/finm-32800/inclass_examples> and take a quick tour. This holds the small, self-contained demos we'll draw on all quarter (virtual environments, env vars, PyDoit, Sphinx, polars, WRDS/Datastream, SQL, LaTeX).
- **Virtual Environments**: See [Virtual Environments](./Week1/virtual_environments.md).
  - *Why we care:* I'm going to hand you a series of repositories this quarter, each with its own `requirements.txt` / `environment.yml`. The whole point of pinning dependencies is **reproducibility**---so that you can install the same software versions I used and reproduce my results exactly. Differences in package versions (and in how data gets pulled) are a common, hard-to-debug source of mismatched results.
  - We'll work through `software_environments/` in the in-class repo, which builds the same small app four ways: `conda`, `conda` + `pip`, `uv`, and `pixi`.
  - *→ HW 1:* HW 1 is the first such repo. Before anything else works, you'll create the `finm` environment and `pip install -r requirements.txt`---so this catch-up is the prerequisite for getting started.


## Objectives

The main event this week is **[HW 1](./HW1.md)**: pull data from **WRDS**---specifically **CRSP** (Center for Research in Security Prices)---and use it to **reconstruct the S&P 500 index** from its constituents. Treat everything below as building, piece by piece, toward that assignment. Each topic unlocks a specific step of HW 1 (flagged *→ HW 1* below), and we'll launch the assignment live at the end of class.

- Motivate the platform we'll use for data all quarter: **WRDS**.
  - [Introduction to WRDS and WRDS Web Queries](./Week2/WRDS_intro_and_web_queries.md). Web queries are a good way to explore the data before we automate.
  - *→ HW 1:* explore the CRSP tables by hand first, so the automated pull isn't a black box.
- **`.env` files and secrets**: Where your WRDS credentials live, and how to keep them out of your code and out of Git. See [Env Files](./Week2/env_files.md). (See also `env_vars/` in the in-class repo.)
  - *→ HW 1:* the assignment can't authenticate to WRDS until your credentials are in `.env` (and never committed to Git).
- **Automating the data pull**: [Demo of the WRDS Python package](./notebooks/_01_wrds_python_package_ipynb.ipynb).
  - In the past, the first HW had students manually download data, and differences in those manual steps were a common source of error. This week we learn to automate the download so everyone starts from the same data.
  - *→ HW 1:* this *is* the data pull in HW 1 (`pull_CRSP_stock.py`, `pull_SP500_constituents.py`)---the step that makes the unit tests deterministic.
- **Basics of SQL**: We have a lot to cover today, so in class I'll only show *just* enough SQL to query CRSP and make the simple joins our queries need. The deeper SQL material is left for you to explore on your own---work through [Basics of SQL](./notebooks/_05_basics_of_SQL_ipynb.ipynb) outside of class to fill in the rest.
  - *→ HW 1:* enough to read and tweak the queries that fetch the CRSP tables.

### Launch HW 1 together

HW 1 is the capstone of everything above---it exercises the whole reproducibility chain end to end: a pinned environment, an automated WRDS pull, and deterministic unit tests. We'll start it live in class; you'll finish on your own.

- **Part 1 (independent):** GitHub Skills---[review pull requests](https://github.com/skills/review-pull-requests) and [resolve merge conflicts](https://github.com/skills/resolve-merge-conflicts).
- **Part 2 (start in class):** Replicate the CRSP market index, then **reconstruct the S&P 500 index** from its constituents.
  - [HW Guide Part A: CRSP Market Returns Indices](./notebooks/_02_CRSP_market_index_ipynb.ipynb)
  - [HW Guide Part B: Reconstructing the S&P 500 Index](./notebooks/_03_SP500_constituents_and_index_ipynb.ipynb)
- **The workflow we'll walk through together:**
  1. Clone your HW 1 repository.
  2. Create the `finm` conda environment and `pip install -r requirements.txt`. (HW 1 is the first repo with its own pinned dependencies---exactly why we covered environments today.)
  3. Put your WRDS credentials in `.env`.
  4. Run `doit` to pull the CRSP and constituent data and build the pipeline.
  5. Run `pytest`---watch the tests fail---then fill in the missing code in `calc_CRSP_indices.py` and `calc_SP500_index.py` until they pass.
  6. Push; GitHub Actions runs the autograder.
- **Due Friday, July 10, 2026** (two-week window; HW 2 starts next week and overlaps).


## Looking ahead to Week 3

Next week we extend this to the **Fama-French 1993** replication ([HW 2](./HW2.md))---merging **CRSP with Compustat** and constructing the factors---and make it the case study for the **PyDoit task runner**, the tool that turns a pile of scripts into a pipeline that rebuilds itself with one command. Week 3 is also when you'll meet the **final projects**: I'll walk through the project list and previews, and send out the survey where each group of two indicates its preferred projects and partner.
