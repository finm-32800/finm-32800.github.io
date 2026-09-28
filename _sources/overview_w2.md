# Week 2: Task Runners and SQL, featuring Fama-French 1993

```{toctree}
:maxdepth: 1
Week3/what_is_a_task_runner.md
Week3/doit_examples.md
notebooks/_05_basics_of_SQL_ipynb.ipynb
Week3/project_structure.md
Week3/uv_and_pixi.md
Week3/ftsfr.md
```

Last week you pulled data once, by hand. A replication is not one pull: it is
pull, clean, merge, construct, test, report, and once a project has more than
two steps, running them by hand in the right order becomes the main source of
irreproducibility. The fix is a **task runner**, and the case study that
motivates it is the **Fama-French (1993)** replication that finishes
[HW 1](./HW1.md).

## Announcements

- **[HW 1](./HW1.md) is due at the end of week 3.** We take questions at the
  start of class. Parts 3 and 4, the Fama-French factors and the investment
  sort, are what we cover tonight.
- **Final project list and survey.** The
  [Potential Final Projects](./FinalProject/potential_final_projects.md) list is
  posted, and the preference survey goes out this week. Find your partner now:
  each group is **exactly 2 people**, and **one person per group** submits the
  survey. Assignments are emailed after it closes.
- **[HW 2](./HW2.md) launches next week**, so HW 1 and HW 2 will overlap by a
  few days. That is by design and it is how the rest of the quarter runs.

## Objectives

- Explain what a build system does and why `doit` re-runs only what changed:
  [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md).
- Write `doit` tasks with correct file dependencies and targets, and read a
  `dodo.py` as a dependency graph: [PyDoit Examples](./Week3/doit_examples.md).
- Write enough SQL to query CRSP and Compustat and to make the joins these
  queries need: [Basics of SQL](notebooks/_05_basics_of_SQL_ipynb.ipynb).
- Know the layout a data project should have, and why:
  [the ChartBook project template](./Week3/project_structure.md).
- State what the CAPM fails to explain and how SMB and HML absorb it, and
  construct the factors from raw CRSP and Compustat.
- Know the alternatives to conda for pinning an environment:
  [`uv` and `pixi`](./Week3/uv_and_pixi.md).

## Agenda

1. **HW 1 questions.** Parts 1 and 2 should be working; if your WRDS pull is not
   authenticating, we fix that first.
2. **Why task runners?** [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md):
   what problem they solve and where `doit` sits relative to Make and friends.
   Then hands-on with [PyDoit Examples](./Week3/doit_examples.md), using the
   `pydoit/` directory of the
   [in-class examples repo](https://github.com/finm-32800/inclass_examples).
   Tasks, file dependencies, targets, and why a correct dependency graph is the
   whole point.
   *→ HW 1 Part 3:* you complete `task_calc_Fama_French_1993` and
   `task_calc_inv_portfolios` in `dodo.py`.
3. **Just enough SQL.** In class I show only what is needed to query CRSP and
   Compustat and to make the simple joins our queries require. Work through
   [Basics of SQL](notebooks/_05_basics_of_SQL_ipynb.ipynb) on your own for the
   rest.
   *→ HW 1 Part 3:* you complete the `WHERE` clauses of the Compustat and
   CRSP-Compustat link queries in `src/pull_CRSP_Compustat.py`. These are tested
   against a small in-memory database, so you can iterate without WRDS.
4. **Why the factors exist, before how they are built.** The CAPM says every
   portfolio's alpha should be zero. We test that on portfolios sorted by size,
   book-to-market, and investment in the
   [CAPM analysis notebook](notebooks/_06_CAPM_analysis_ipynb.ipynb), find the
   alphas it leaves behind, and watch SMB and HML absorb them in the
   [three-factor notebook](notebooks/_07_Fama_French_3_factor_ipynb.ipynb). That
   is the argument of Fama and French (1993) in two notebooks, and the reason
   we are about to build the factors.
5. **The case study, end to end.** Walk the
   [Fama-French pipeline](notebooks/_04_Fama_French_1993_ipynb.ipynb): automated
   CRSP and Compustat pulls feeding book equity, the exchange and share-code
   screens, NYSE breakpoints, the six size/book-to-market portfolios, and unit
   tests against the Ken French library.
6. **Project structure.** [The ChartBook project template](./Week3/project_structure.md):
   the directory layout your final project will use. We return to ChartBook's
   catalogs and published sites in week 3.
7. **Modern environment tools.** [`uv` and `pixi`](./Week3/uv_and_pixi.md), the
   follow-up to last week's conda material, if time permits.

## Final Projects: Preview and Pitch

This is the week you meet the final projects.

- **What a replication looks like.** Walk the
  [Project Previews](./FinalProject/project_previews.md) page: papers with the
  actual figures and tables you would reproduce.
- **The full list.**
  [Potential Final Projects](./FinalProject/potential_final_projects.md),
  organized by topic. Each entry states the exact tables and figures to
  reproduce and the data sources involved, all verified to be available to you.
  Skim it before the survey goes out.
- **Where this can lead.** Past final projects from this course grew into the
  [Financial Time Series Forecasting Repository](./Week3/ftsfr.md), which is now
  a paper with former students as coauthors. Every dataset in it is fully
  reproducible from source with one command, and that reproducibility, not any
  single forecast, is the paper's contribution. We come back to FTSFR in week 9,
  when we fit forecasting models to it.

## Looking ahead to Week 3

With a pipeline that rebuilds itself, the next question is where its output
goes. Week 3 is **reproducible reports**: notebooks as reports, ChartBook,
LaTeX, and publishing to GitHub Pages, plus the pull-request workflow that real
teams use to change code. [HW 2](./HW2.md) launches: the Treasury yield curve
and the expected path of the policy rate, published as a website.
