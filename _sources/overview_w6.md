# Week 6: Data Validation; SQL at Scale, Remote Machines, and Job Schedulers

```{toctree}
:maxdepth: 1
Week5/unit_tests.md
notebooks/_01_data_sources_overview_ipynb.ipynb
notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb
Week8/medium_sized_data_strategies.md
Week8/polars_exercises.md
Week8/remote_machines_and_hpc.md
Week8/exercise_jupyter_on_midway.md
Week8/optional_lab_clean_trace_on_midway.md
Week6/chartbook_catalog.md
Week3/ftsfr.md
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

- **[HW 3](./HW3.md) is due next week,** Tuesday, November 10.
- **[HW 4](./HW4.md) and [HW 5](./HW5.md):** dates to be announced on
  Canvas.
- **Accept your invite to the report repo.** Every student gets a GitHub
  collaborator invite to the private report repository. Check your email or
  [github.com/notifications](https://github.com/notifications) and confirm in
  class that you can open it.
- **Confirm your cluster access in class today.** You need the WRDS Cloud (the same WRDS
  account, over SSH) and Midway3 (RCC). If either fails, we fix it in class.
- **The exam is Tuesday, December 1,** in week 9, covering weeks 0 through 8.
  See [Exam Preparation](./exam_prep.md).
- **Project consultations end next week.** If your group has not had one, book
  it today: [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile).

## Objectives

- Say what makes a good unit test, and write tests that are the
  *specification* for a function rather than an afterthought:
  [Unit Tests](./Week5/unit_tests.md).
- Distinguish a unit test from a data-validation check, and wire both into a
  pipeline so a run refuses to publish bad data.
- Test code that talks to the network without talking to the network:
  fixtures, small in-memory databases, and cached samples.
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

Carried over from week 5, which made room for live monitors and the math
of HW 3:

## Agenda Item 1: Tests and Data Checks

[Unit Tests](./Week5/unit_tests.md), with the monitor you deployed last week
as the example.

- **Tests as a specification.** The reason every homework says "read the
  failing test": a docstring says what a function should do, a test says it
  in a form the machine can check.
- **Testing code that pulls data, without pulling it.** The monitor's tests
  run with no API key: they hand the parser FRED's "." for a missing day and
  check it becomes a missing value. HW 1 tested its SQL against a small
  in-memory database the same way.
- **A unit test is not a data check.** A unit test asks whether your
  function is right; a data check asks whether today's data are sane:
  duplicate dates, values out of range, stale timestamps. The monitor runs
  its checks before it publishes, and a failure leaves yesterday's page up.
  The next two items are the two kinds of data check: conventions you must
  defend, and an exact diff against a reference.

## Agenda Item 2: No Ground Truth --- Cleaning FINRA TRACE

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

## Agenda Item 3: A Ground Truth --- The Order Book

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

## Agenda Item 4: Medium Data, Polars, and Parquet

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

## Agenda Item 5: Remote Machines and Two Schedulers

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

## Agenda Item 6: NYSE TAQ on the WRDS Cloud

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

## Looking ahead to Week 7

Week 7 is **Apache Airflow**:
`doit` orders the steps inside one project, and an orchestrator orders many
projects. Your report contributions are twenty-odd small pipelines with one
consumer, which is exactly the structure Airflow exists for.
