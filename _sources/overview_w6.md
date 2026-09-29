# Week 6: SQL at Scale, Remote Machines, and Job Schedulers

```{toctree}
:maxdepth: 1
Week8/medium_sized_data_strategies.md
Week8/polars_exercises.md
Week8/remote_machines_and_hpc.md
Week8/exercise_jupyter_on_midway.md
Week8/optional_lab_clean_trace_on_midway.md
Week1/collaborative_report_and_style_guides.md
```

When the data outgrows your laptop, the toolkit changes. Polars replaces pandas,
Parquet replaces CSV, and the computation moves to a machine you reach over SSH
and operate entirely from the command line. This is why so many postings insist on
Linux and "Unix scripting," and why "schedulers" appears in Citadel's list of
required skills.

We use **two** schedulers this week, deliberately. The WRDS Cloud runs **Grid
Engine** (`qsub`, `qrsh`, `qsas`); UChicago's Midway3 runs **SLURM** (`sbatch`,
`sinteractive`). They do the same job in different dialects, and seeing both is
the point: what you are learning is batch scheduling, not one vendor's commands.

## Announcements

- **[HW 3](./HW3.md) is due tonight,** at the start of class.
- **[HW 4](./HW4.md) is due next week,** Tuesday, November 10. Tonight's lab takes
  it to a cluster, so bring your working single-day reconstruction to class.
- **[HW 5](./HW5.md) launches today,** due Tuesday, December 1: one figure and
  one paragraph for the class report. It is the last homework, and the smallest.
  Aim to open your pull request before Thanksgiving.
- **Accept your invite to the report repo.** Every student gets a GitHub
  collaborator invite to the private report repository. Check your email or
  [github.com/notifications](https://github.com/notifications) and confirm tonight
  that you can open it.
- **Confirm your cluster access tonight.** You need the WRDS Cloud (the same WRDS
  account, over SSH) and Midway3 (RCC). If either fails, we fix it in class.
- **The midterm is in two weeks,** Tuesday, November 17, in the first 75 minutes
  of class, covering weeks 0 through 7. Practice questions and their answer key
  are handed out; see [Exam Preparation](./exam_prep.md).
- **Project consultations end this week.** If your group has not had one, book
  it today: [youcanbook.me](https://finm-32800.youcanbook.me/).

## Objectives

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

## Agenda Item 1: Medium Data, Polars, and Parquet

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

## Agenda Item 2: Remote Machines and Two Schedulers

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

## Agenda Item 3: NYSE TAQ on the WRDS Cloud

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
- Contrast with HW 4: TAQ is equities on the WRDS Cloud; the E-mini order book is
  futures on Midway. Two datasets, two schedulers, one skill.

## Agenda Item 4: Lab, HW 4 on a Cluster

Bring your single-day reconstruction from last week. Tonight it becomes a job
array over a quarter of trading days: a submit script, one array task per day,
results written to scratch, aggregated, and `rsync`'d back. This lab is done in
class and is not graded. The graded part of HW 4 is the single-day path on your
laptop, so keep it working.

An optional practice run on Midway with a different pipeline, cleaning TRACE, is
in [Optional Lab: Clean TRACE on Midway](./Week8/optional_lab_clean_trace_on_midway.md).
It is not graded and it is a good rehearsal.

## Agenda Item 5: Launch HW 5, the Collaborative Report

[HW 5](./HW5.md) is the class's one shared artifact. The whole class produces a
single publication-style PDF on the state of the equity premium predictors, and
the topics are the predictors of
[Goyal, Welch, and Zafirov (2024)](https://academic.oup.com/rfs/article/37/11/3490/7749383).
There are more predictors in that paper than there are students, so everyone gets
their own.

- **Read the instructions:** what you contribute, the workflow, and the rubric
  are posted with the assignment when it launches.
- **Style guides.**
  [The Collaborative Report and Style Guides](./Week1/collaborative_report_and_style_guides.md):
  organizations that publish reports keep a style guide so that many contributors
  produce one coherent document. We look at a few real ones and write ours.
- **Claim an issue.** Each open issue is one predictor. Assign it to yourself, one
  student per issue. Claim early.
- **Build it reproducibly.** Your exhibit is its own file in `src/`, wired into
  `dodo.py`, pulling through FRED, the OFR API, or the
  [ChartBook catalog](./Week6/chartbook_catalog.md). It ships with a test that the
  shared repo's CI runs on every contribution.
- **Open a draft PR early.** You need two classmate reviews, and you owe two
  reviews to others. An early draft PR gets you comments while there is still time
  to act on them. The workflow is the one from
  [GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md).
- **Live walkthrough** of the repo skeleton, then review an open PR together.

## Looking ahead to Week 7

Week 7 is **Apache Airflow**:
`doit` orders the steps inside one project, and an orchestrator orders many
projects. Your report contributions are twenty-odd small pipelines with one
consumer, which is exactly the structure Airflow exists for.
