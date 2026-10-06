# Week 2: Task Runners, ChartBook, and SQL, featuring Fama-French 1993

```{toctree}
:maxdepth: 1
notebooks/_08_CAPM_to_multifactor_models_ipynb.ipynb
notebooks/_02_basics_of_SQL_ipynb.ipynb
Week3/what_is_a_task_runner.md
Week3/doit_examples.md
Week4/reports_with_jupyter_notebooks.md
Week3/project_structure.md
Week3/uv_and_pixi.md
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
- **Get a Databento API key before October 13.** HW 2 pulls 30-Day Fed Funds
  futures from Databento's CME Globex feed, which the program's subscription
  covers, and the pipeline does not run without a key in your `.env`. HW 3 and
  HW 4 use the same key. See [Databento](./Week7/databento.md).

## Objectives

- State what the CAPM fails to explain and how SMB and HML absorb it:
  [From the CAPM to Multifactor Models](notebooks/_08_CAPM_to_multifactor_models_ipynb.ipynb).
  Then construct the factors from raw CRSP and Compustat.
- Write enough SQL to query CRSP and Compustat and to make the joins these
  queries need: [Basics of SQL](notebooks/_02_basics_of_SQL_ipynb.ipynb).
- Explain what a build system does and why `doit` re-runs only what changed:
  [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md).
- Write `doit` tasks with correct file dependencies and targets, and read a
  `dodo.py` as a dependency graph: [PyDoit Examples](./Week3/doit_examples.md).
- Use a notebook for what it is good at, reporting, and keep pipeline logic out
  of it: [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md).
  Execute a notebook from the command line with `nbconvert` and wire it into
  `doit`.
- Register a project's notebooks and dataframes in a `chartbook.toml` and build
  a browsable site from it, as one more `doit` target.
- Know the layout a data project should have, and why, and start one with
  `chartbook init`: [the ChartBook project template](./Week3/project_structure.md).
- Know the alternatives to conda for pinning an environment, and set up a
  project with one: [`uv` and `pixi`](./Week3/uv_and_pixi.md).

## Agenda

1. **HW 1 questions.** Parts A to C should be working; if your WRDS pull is not
   authenticating, we fix that first.
2. **The CAPM, in one picture.** Last week's
   [From Mean-Variance to the CAPM](notebooks/_07_mean_variance_to_CAPM_ipynb.ipynb)
   has grown since class. Step 2 now splits a stock's risk into a market part
   and a firm-specific part, with Ford in 2008 as the example, and the page
   ends with the four lines that textbooks draw, CAL, CML, SCL, and SML, side
   by side from ten CRSP stocks. We review the derivation through that figure.
   The alpha it marks is the intercept of one stock's history and the
   distance from the line every stock should sit on, and it is what tonight's
   tests look for.
3. **Why the factors exist, before how they are built.** The CAPM ended with
   one factor. If investors also hedge changes in their opportunities, more
   appear:
   [From the CAPM to Multifactor Models](notebooks/_08_CAPM_to_multifactor_models_ipynb.ipynb)
   states the ICAPM and shows where Fama and French (1993) fit. Then
   [HW 1 Guide E](notebooks/_06_CAPM_and_Fama_French_ipynb.ipynb)
   runs the test on the portfolios your pipeline builds: the market alone is
   not the tangency portfolio, the CAPM leaves alphas, and SMB and HML absorb
   some but not all. That is the argument of Fama and French (1993), and the
   reason we build the factors next.
4. **The case study, end to end.** Walk the Fama-French pipeline with
   [HW 1 Guide D](notebooks/_05_Fama_French_1993_ipynb.ipynb): automated
   CRSP and Compustat pulls feeding book equity, the exchange and share-code
   screens, NYSE breakpoints, the six size/book-to-market portfolios, and unit
   tests against the Ken French library. Those steps have to run in order
   every time the data changes, and that is the problem the rest of tonight
   solves.
5. **Just enough SQL.** The pipeline starts with a pull. In class I show only
   what is needed to query CRSP and Compustat and to make the simple joins our
   queries require. Work through
   [Basics of SQL](notebooks/_02_basics_of_SQL_ipynb.ipynb) on your own for the
   rest.
   *→ HW 1 Part D:* you complete the `WHERE` clauses of the Compustat and
   CRSP-Compustat link queries in `src/pull_CRSP_Compustat.py`. These are tested
   against a small in-memory database, so you can iterate without WRDS.
6. **Why task runners?** [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md):
   what problem they solve and where `doit` sits relative to Make and friends.
   Then hands-on with [PyDoit Examples](./Week3/doit_examples.md), using the
   `pydoit/` directory of the
   [in-class examples repo](https://github.com/finm-32800/inclass_examples).
   Examples 01 and 02: tasks, file dependencies, targets, and why a correct
   dependency graph is the whole point.
   *→ HW 1 Part D:* you complete `task_calc_Fama_French_1993` and
   `task_calc_inv_portfolios` in `dodo.py`.
7. **Notebooks as tasks.** Examples 03 and 04 continue the hands-on.
   [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md):
   why notebooks are for *reporting* and not for pipeline steps, and how
   `nbconvert` executes one from the command line and exports it to HTML. A
   notebook is then just another `doit` task, with file dependencies and
   targets.
   *→ HW 1:* `task_run_notebooks` in your `dodo.py` does exactly this to the
   six guide notebooks.
8. **ChartBook: the site as a build target.**
   [ChartBook](https://pypi.org/project/chartbook/) catalogs a project's
   notebooks, dataframes, and charts and generates a browsable static site from
   them.
   - **The manifest, `chartbook.toml`.** Each `[notebooks.*]`,
     `[dataframes.*]`, and `[charts.*]` entry says what an output is, where it
     comes from, and where it lives. Read HW 1's as the example.
   - **The CLI and the Python API.** `chartbook build` generates the site;
     `chartbook ls` lists what is registered; `chartbook data get-path` returns
     a parquet path; and `data.load(pipeline=..., dataframe=...)` loads it
     straight into pandas or polars.
   - **Where it sits in the pipeline.** `chartbook build` is just another
     `doit` task (`task_generate_pipeline_site` in HW 1), so the site is
     regenerated whenever the data changes, not assembled by hand. Next week
     we publish it to GitHub Pages; in week 4, many projects' manifests
     aggregate into one catalog.
9. **Starting a project: `chartbook init`.** Your final project starts here,
   so we start one live.
   - [The ChartBook project template](./Week3/project_structure.md): the
     directory layout, and the
     [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science/)
     principles behind it (data is immutable, analysis is a DAG, secrets stay
     out of version control).
   - `chartbook init` is a Cookiecutter template. Created with
     [Cruft](https://cruft.github.io/cruft/) instead, the project can pull in
     later improvements to the template with `cruft update`.
   - The template asks which environment manager to use. That is the cue for
     [`uv` and `pixi`](./Week3/uv_and_pixi.md), the follow-up to last week's
     conda material: we scaffold one project with each and compare the
     lockfiles they write.

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
  [Financial Time Series Forecasting Repository](./Week3/ftsfr.md), now a paper
  with former students as coauthors. We meet it properly in week 4 and fit
  forecasting models to it in week 9.

## Looking ahead to Week 3

With a pipeline that rebuilds itself, the next question is where its output
goes. Week 3 is **publishing**: the ChartBook site on GitHub Pages and the
PDF from LaTeX, plus the pull-request workflow that real teams use to change
code. [HW 2](./HW2.md) launches: the Treasury yield curve and the expected path
of the policy rate, published as a website and summarized in a one-page PDF
market brief.
