# Week 8: Packaging, CI, and Code Quality

```{toctree}
:maxdepth: 1
Week6/python_packaging_with_hatch.md
Week5/sphinx.md
notebooks/_01_data_sources_overview_ipynb.ipynb
notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb
Week8/polars_exercises.md
Week8/remote_machines_and_hpc.md
Week8/exercise_jupyter_on_midway.md
Week8/optional_lab_clean_trace_on_midway.md
Week8/study_guide.md
```

Week 4 read a workflow and week 5 deployed one. This week turns to where most
workflows in the wild live: Python packages. Every package you have installed
this quarter was built, tested on several versions of Python, documented, and
released by one. We look at how, then at the checks that run before a push,
Ruff and pre-commit, and close by drawing the line between a CI service and
the orchestrator of last week.

## Announcements

- **[HW 4](./HW4.md), the class report, is due today at 9:30 am,** the
  start of class. Only pull requests merged by then count.
- **[HW 5](./HW5.md):** dates to be announced on Canvas.
- **No class next week.** November 24 falls in Thanksgiving break. Week 9 meets
  on December 1.
- **The exam is December 1,** in the first 75 minutes of class, covering weeks 0
  through 8, today included. Practice questions and their answer key are
  handed out today; see [Exam Preparation](./exam_prep.md).
- **Final project work should be underway.** Your project is due 24 hours
  before your final project presentation in week 10. I run and grade the code
  before we meet, so it must run from a clean clone by following your README
  alone.
- **Final project presentation sign-ups.** Book your group's 30-minute slot for
  week 10 at [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile). The last day
  is Friday, December 11.

## Objectives

- Explain the anatomy of a published Python package: `pyproject.toml`, a
  build backend, versioning, a test matrix in CI, and a release:
  [Writing and Publishing Your Own Python Packages](./Week6/python_packaging_with_hatch.md).
- Say what Sphinx builds, and why documentation generated from the code
  stays current: [Sphinx](./Week5/sphinx.md).
- Lint and format code with [Ruff](https://docs.astral.sh/ruff/), run it on
  every commit with [pre-commit](https://pre-commit.com/), and enforce the
  same checks in CI.
- Use a matrix and a cache in a workflow.
- Say clearly where GitHub Actions stops and an orchestrator starts, and
  defend the choice either way.
- Explain what a monetary policy surprise is, how it is extracted from fed
  funds futures, and what Bernanke and Kuttner (2005) find about the stock
  market's reaction to it.

## Agenda Item 1: Where CI/CD Lives --- Python Packages

Every package you have installed this quarter was built, tested, and
released by a workflow.
[Writing and Publishing Your Own Python Packages](./Week6/python_packaging_with_hatch.md):
`pyproject.toml` as the single source of truth, a build backend, dynamic
versioning, semantic versioning as a contract.

The demo is [`chartbook`](https://pypi.org/project/chartbook/), the package
you have been running all quarter: its `pyproject.toml`, `hatch build`
producing the wheel, `hatch test` running the suite across a matrix of
Python versions in GitHub Actions, and a release cut from the
[changelog](https://backofficedev.github.io/chartbook/changelog.html).
You will not build a package in this course. You will see how one is built,
because this is what "software packaging" means in the postings, and
because HW 3 itself grew out of a package two of last summer's students
wrote for their final project, `crashbounds`, which is credited in the
assignment.

## Agenda Item 2: Documentation with Sphinx

[Sphinx](./Week5/sphinx.md) builds the documentation for pandas and for this
book: pages written in reStructuredText or Markdown, reference pages
generated from the code's own docstrings, and a static site published the same way you published
yours in week 4.

## Agenda Item 3: Checks Before the Push --- Ruff and pre-commit

CI catches a problem after you push. The cheapest checks run before that.

- [Ruff](https://docs.astral.sh/ruff/) is a Python linter and formatter.
  `ruff check` flags unused imports, undefined names, and other mistakes;
  `ruff format` settles style once, so a code review never argues about it.
  Both are configured in `pyproject.toml`.
- [pre-commit](https://pre-commit.com/) runs those checks as a git hook on every
  commit, from a `.pre-commit-config.yaml` checked into the repository, so every
  contributor runs the same checks.
- Then run the same hooks in a workflow, so the check that guards `main` is the
  one you already ran on your laptop. Add both to your final project.

## Agenda Item 4: Matrices, Caching, and the Boundary

- **Matrices and caching.** A `strategy: matrix:` runs the same job on
  several versions of Python, which catches the bug that only one version
  has; a cache keeps a slow workflow from reinstalling everything on every
  run.
- **The boundary.** You have now used three kinds of automation:

| | Triggered by | Knows about | Reach for it when |
|---|---|---|---|
| `doit` | you, at a prompt | one project's file graph | building or rebuilding a project |
| Actions / cron | a push, or a clock | one repository | tests on every change; one job on a schedule |
| Airflow | a clock, or data landing | many projects, with history | pipelines depend on each other, and you need to know what ran |

## Agenda Item 5: The Paper --- Bernanke and Kuttner (2005)

[Bernanke and Kuttner (2005)](https://doi.org/10.1111/j.1540-6261.2005.00760.x),
*What Explains the Stock Market's Reaction to Federal Reserve Policy?*, splits a
rate change into the part the market expected and the part it did not, using the
futures price change on the announcement day, and shows that equities respond to
the surprise and essentially not at all to the expected part. It is the natural
paper for this week because its entire empirical content depends on a
point-in-time measurement: what was priced *before* the announcement. The
[FedWatch case study](https://github.com/finm-32800/case_study_fedwatch)
computes these surprises from the same futures as HW 2, and a monitor like the
one you deployed in week 5 is the machine that records them every day, without
being asked.

## Looking ahead to Week 9

The **exam** is in the first 75 minutes. After it, the last week is **basic
MLOps**: what it takes to keep a model healthy after it
ships. Experiment tracking with MLflow, drift checks on the inputs, and fitting the
forecasting methods of the open benchmark of Bejarano et al. (2026) to the
FTSFR datasets that the class report depends on. It meets on December 1, after the
break.


## Later in the Quarter: Data at Scale

These topics moved out of week 6 to a session later in the quarter, whose
date will be announced. HW 5 will use TAQ on Midway.

When the data outgrows your laptop, the toolkit changes. Polars replaces pandas,
Parquet replaces CSV, and the computation moves to a machine you reach over SSH
and operate entirely from the command line. This is why so many postings insist on
Linux and "Unix scripting," and why "schedulers" appears in Citadel's list of
required skills.

We use **two** schedulers this week, deliberately. The WRDS Cloud runs **Grid
Engine** (`qsub`, `qrsh`, `qsas`); UChicago's Midway3 runs **SLURM** (`sbatch`,
`sinteractive`). They do the same job in different dialects, and seeing both is
the point: what you are learning is batch scheduling, not one vendor's commands.

### Announcement

- **Confirm your cluster access in class that day.** You need the WRDS Cloud (the same WRDS
  account, over SSH) and Midway3 (RCC). If either fails, we fix it in class.

### Objectives

- Explain the major decisions in cleaning FINRA TRACE and why reasonable
  people produce different "clean" datasets: the
  [data sources overview](notebooks/_01_data_sources_overview_ipynb.ipynb) and
  the [Clean TRACE walkthrough](notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb).
- Explain what a limit order book is, what a market-by-order feed contains,
  and how a reconstruction can be validated exactly against a reference book.
- Choose sensible strategies for data between 1 GB and 100 GB:
  [Medium-Sized Data Strategies](./Week8/medium_sized_data_strategies.md).
- Use Polars and explain lazy evaluation, predicate pushdown, streaming, and Hive
  partitioning; know when it beats pandas and when it does not:
  [Polars Exercises](./Week8/polars_exercises.md).
- Connect to a remote machine over SSH, move data with `rsync`, and work entirely
  from the command line: [Remote Machines and HPC](./Week8/remote_machines_and_hpc.md).
- Describe HPC cluster architecture: login nodes, compute nodes, and shared versus
  scratch storage, and why you must never compute on a login node.
- Submit, monitor, and collect a **job array** under SLURM, and do the equivalent
  under Grid Engine on the WRDS Cloud.
- Forward a port to reach a Jupyter notebook running on a compute node:
  [Exercise: Jupyter on Midway](./Week8/exercise_jupyter_on_midway.md).
- Explain how liquidity is measured from intraday trades and quotes, and why the
  choice of data source changes the answer.

### No Ground Truth --- Cleaning FINRA TRACE

Corporate bond transactions are reported to FINRA, and the raw feed contains
cancellations, corrections, reversals, agency double-counts, and prices that
cannot be right. Cleaning it is a sequence of judgment calls, each defensible and
each consequential.

- [Data sources overview](notebooks/_01_data_sources_overview_ipynb.ipynb): what
  TRACE is, who provides what, and what the enhanced file adds.
- [Clean TRACE walkthrough](notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb):
  the Dick-Nielsen filters, step by step, with the count of records each one
  removes.
- Why this matters beyond bonds: Dickerson, Robotti, and Rossetti show that
  corporate bond factor results move materially with these choices. When "clean"
  is a convention, the only defense is that your convention is documented, tested,
  and rerunnable. That is the whole argument of this course, in one dataset.

### A Ground Truth --- The Order Book

Now the opposite situation. A limit order book is the state of a market at an
instant. Databento's CME Globex feed gives you both the raw messages (`mbo`,
market by order) and the vendor's own reconstruction of the top ten levels
(`mbp-10`). So you can rebuild the book from the messages and **diff your answer
against theirs**. Every discrepancy is a located bug, which makes this the
cleanest validation exercise in the course.

- Message types and what each does to the book: add, cancel, modify, trade, fill.
- Replaying messages in order, and what "in order" means when timestamps tie.
- Writing the diff as a test, not as a notebook you eyeball.
- Trade classification with no ground truth: bulk volume classification against
  the tick rule, and checking them against each other.
- **Flow toxicity.**
  [Easley, López de Prado, and O'Hara (2012)](https://doi.org/10.1093/rfs/hhs053)
  define VPIN, a measure of order-flow imbalance computed in *volume time* rather
  than clock time, and show it rising in the hours before the May 2010 flash
  crash as liquidity providers withdrew. They built it on the E-mini S&P 500
  contract, which is the contract you will use. This is what the Chicago options
  market makers who hire from this program do all day.

### Medium Data, Polars, and Parquet

[Medium-Sized Data Strategies](./Week8/medium_sized_data_strategies.md) and
[Polars Exercises](./Week8/polars_exercises.md).

- The three escapes when data will not fit: use less of it (columns, rows,
  dtypes), stream it, or move to a bigger machine. Most problems are solved by the
  first.
- Parquet as a columnar format: why reading three columns of a hundred costs
  almost nothing, and why partitioning matters.
- Polars: expressions, the lazy API, and reading a query plan. Predicate and
  projection pushdown are the reason a lazy scan of a partitioned dataset can beat
  loading a single file.
- **Decoupling pulls from processing.** A pipeline that re-downloads on every run
  is not reproducible and is not polite to the vendor. The pull is one task with a
  cached target; everything downstream reads the cache.

### Remote Machines and Two Schedulers

[Remote Machines and HPC](./Week8/remote_machines_and_hpc.md), then the comparison.

- SSH keys, config files, and `rsync`. Getting results *back* is half the job.
- Cluster anatomy: login node versus compute node, and shared home versus scratch.
  Note the limits: WRDS Cloud gives roughly ten concurrent batch slots, three
  interactive slots, a one-week wall clock, a 10 GB home directory, and scratch
  that is purged after seven days.
- **SLURM on Midway3:** `sinteractive` for a shell on a compute node, `sbatch` for
  a batch job, and `--array` for a job array over independent units of work.
  [Exercise: Jupyter on Midway](./Week8/exercise_jupyter_on_midway.md) covers port
  forwarding.
- **Grid Engine on the WRDS Cloud:** `qrsh` for interactive work, `qsub` for
  batch, `qsas` for SAS. The same concepts under different names.
- **The mapping is the lesson.** Put the two side by side: submit, query the
  queue, kill a job, request memory, request an array. Once you can translate,
  the next scheduler you meet costs you an afternoon, not a week.

### NYSE TAQ on the WRDS Cloud

The in-class case study is
[Holden and Jacobsen (2014)](https://doi.org/10.1111/jofi.12127),
*Liquidity Measurement Problems in Fast, Competitive Markets*. They show that
measuring spreads and price impact from Monthly TAQ, with its second-resolution
timestamps and its missing withdrawn quotes, gives materially distorted answers
against Daily TAQ. The methodological point is the same one we met in TRACE: the
data source is part of the result.

- The authors publish SAS code that runs **on the WRDS Cloud**, computes the full
  NBBO, and produces the standard liquidity measures. We run it there.
- **In-class lab:** each student submits one `qsub` job that computes the NBBO for
  one ticker-day, then brings the output back and analyzes it in Python. You are
  driving someone else's production code as a black box through a scheduler,
  which is an ordinary and useful thing to be able to do.
- Contrast with the order book earlier today: TAQ is equities on the WRDS
  Cloud, and the E-mini order book is futures from Databento. Two datasets, one
  skill.
