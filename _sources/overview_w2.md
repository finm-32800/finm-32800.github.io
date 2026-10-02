# Week 2: Task Runners and SQL, featuring Fama-French 1993

```{toctree}
:maxdepth: 1
Week3/what_is_a_task_runner.md
Week3/doit_examples.md
notebooks/_02_basics_of_SQL_ipynb.ipynb
notebooks/_08_CAPM_to_multifactor_models_ipynb.ipynb
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

- **[HW 1](./HW1.md) is due Tuesday, October 20,** but aim to finish it by next
  week, October 13, when [HW 2](./HW2.md) launches. We take questions at the
  start of class. Part D, the Fama-French factors and the investment
  sort, is what we cover tonight.
- **Final project list and survey.** The
  [Potential Final Projects](./FinalProject/potential_final_projects.md) list is
  posted, and the preference survey goes out this week. Form your group now:
  each group is **exactly 4 people**, and **one person per group** submits the
  survey. Assignments are emailed after it closes.
- **[HW 2](./HW2.md) launches next week**, so HW 1 and HW 2 overlap by a week.
  Every assignment from here on overlaps the next one that way, which is why the
  recommended HW 1 finish is a week before its deadline.

## Objectives

- Explain what a build system does and why `doit` re-runs only what changed:
  [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md).
- Write `doit` tasks with correct file dependencies and targets, and read a
  `dodo.py` as a dependency graph: [PyDoit Examples](./Week3/doit_examples.md).
- Write enough SQL to query CRSP and Compustat and to make the joins these
  queries need: [Basics of SQL](notebooks/_02_basics_of_SQL_ipynb.ipynb).
- Know the layout a data project should have, and why:
  [the ChartBook project template](./Week3/project_structure.md).
- State what the CAPM fails to explain and how SMB and HML absorb it, and
  construct the factors from raw CRSP and Compustat.
- Know the alternatives to conda for pinning an environment:
  [`uv` and `pixi`](./Week3/uv_and_pixi.md).

## Agenda

1. **HW 1 questions.** Parts A to C should be working; if your WRDS pull is not
   authenticating, we fix that first.
2. **Why task runners?** [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md):
   what problem they solve and where `doit` sits relative to Make and friends.
   Then hands-on with [PyDoit Examples](./Week3/doit_examples.md), using the
   `pydoit/` directory of the
   [in-class examples repo](https://github.com/finm-32800/inclass_examples).
   Tasks, file dependencies, targets, and why a correct dependency graph is the
   whole point.
   *→ HW 1 Part D:* you complete `task_calc_Fama_French_1993` and
   `task_calc_inv_portfolios` in `dodo.py`.
3. **Just enough SQL.** In class I show only what is needed to query CRSP and
   Compustat and to make the simple joins our queries require. Work through
   [Basics of SQL](notebooks/_02_basics_of_SQL_ipynb.ipynb) on your own for the
   rest.
   *→ HW 1 Part D:* you complete the `WHERE` clauses of the Compustat and
   CRSP-Compustat link queries in `src/pull_CRSP_Compustat.py`. These are tested
   against a small in-memory database, so you can iterate without WRDS.
4. **Why the factors exist, before how they are built.** Last week's
   [From Mean-Variance to the CAPM](notebooks/_07_mean_variance_to_CAPM_ipynb.ipynb)
   ended with one factor, the market. If investors also hedge changes in their
   opportunities, more factors appear:
   [From the CAPM to Multifactor Models](notebooks/_08_CAPM_to_multifactor_models_ipynb.ipynb)
   covers the ICAPM, the consumption CAPM, and the APT. The
   [HW 1 Guide E](notebooks/_06_CAPM_and_Fama_French_ipynb.ipynb)
   then tests it on portfolios sorted by size, book-to-market, and investment:
   the market alone is not the tangency portfolio, the CAPM leaves alphas
   behind, and SMB and HML absorb some of them but not all. That is the
   argument of Fama and French (1993), and the reason we are about to build
   the factors.
5. **The case study, end to end.** Walk the
   Fama-French pipeline with [HW 1 Guide D](notebooks/_05_Fama_French_1993_ipynb.ipynb): automated
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

- **What a finished project looks like.** Walk the
  [Project Previews](./FinalProject/project_previews.md) page: three real
  projects from past cohorts, one each for the site, the report, and the
  extension, with what to notice and what not to copy.
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
