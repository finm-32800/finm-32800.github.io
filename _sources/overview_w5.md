# Week 5: Unit Tests and Data Validation with pytest

```{toctree}
:maxdepth: 1
Week5/unit_tests.md
notebooks/_01_data_sources_overview_ipynb.ipynb
notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb
```

You have been running `pytest` since HW 1, mostly to find out whether you were
done. This week the tests become the subject. For a data pipeline, a test answers
questions like *did every date parse? are there duplicate identifiers? do the
weights sum to one? is this price physically possible?* This is what the postings
call "data checks," "data validation," and "data quality control," and it is the
difference between you noticing bad data and a customer noticing it for you.

The week is organized around a distinction. Sometimes there is **no ground
truth** and every filter is a convention you must defend, which is the cleaning
of FINRA TRACE. Sometimes there **is** a ground truth and validation becomes
exact, which is rebuilding an order book and diffing it against the vendor's own.
[HW 4](./HW4.md) is the second kind.

## Announcements

- **[HW 2](./HW2.md) is due tonight,** at the start of class.
- **[HW 3](./HW3.md) is due next week,** Tuesday, November 3.
- **[HW 4](./HW4.md) launches today,** due Tuesday, November 10, in week 7:
  order book validation and flow toxicity. It is the last of the four coding
  assignments. The graded work runs on your laptop; next week's lab takes it to
  a cluster.
- **Project consultations continue** through next week. Every group needs one.
- **Databento keys again.** HW 4 pulls `mbo` and `mbp-10` for the E-mini, both on
  the free CME Globex feed. Reconstruction runs on one day locally before
  anything scales.

## Objectives

- Say what makes a good unit test, and write tests that are the *specification*
  for a function rather than an afterthought: [Unit Tests](./Week5/unit_tests.md).
- Distinguish a unit test from a data-validation check, and wire both into a
  `doit` pipeline so a rebuild refuses to publish bad data.
- Test code that talks to the network without talking to the network: fixtures,
  small in-memory databases, and cached samples.
- Explain the major decisions in cleaning FINRA TRACE and why reasonable people
  produce different "clean" datasets:
  [data sources overview](notebooks/_01_data_sources_overview_ipynb.ipynb) and the
  [Clean TRACE walkthrough](notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb).
- Explain what a limit order book is, what a market-by-order feed contains, and
  how a reconstruction can be validated exactly against a reference book.
- Explain what order-flow toxicity means and what VPIN measures.

## Agenda Item 1: What Makes a Good Test

[Unit Tests](./Week5/unit_tests.md), with the `pytest` examples in the
[in-class repo](https://github.com/finm-32800/inclass_examples).

- Tests as a specification: the reason every homework says "read the failing
  test." A docstring says what a function should do; a test says it in a form the
  machine can check.
- Fixtures, parametrization, and floating-point comparison. Why
  `assert a == b` is usually wrong on returns.
- **Testing code that pulls data.** You cannot hit WRDS in a test suite that must
  run in seconds on a fresh machine. HW 1 already showed the pattern: the SQL
  `WHERE` clauses are tested against a small in-memory database with no
  connection at all. Generalize it.
- **Validation checks are different from unit tests.** A unit test asks whether
  your function is correct; a validation check asks whether today's data is
  sane. Both belong in the pipeline, and the second one runs on every rebuild.
  Gaps, duplicates, schema drift, unit changes, and stale timestamps.

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

## Agenda Item 4: Launch HW 4

[HW 4](./HW4.md) starts local and small: one trading day, `mbo` and `mbp-10` for
the E-mini, rebuild the book, validate it, classify the trades, compute VPIN.
That is the graded work, and it is due in week 7. Next week's lab scales it to a
quarter of trading days on a cluster, because the days are independent and that
is what a job array is for. The lab is done in class and is not graded. The
optional extension aimed at options desks asks whether options market makers
widen their quoted spreads when the underlying's flow turns toxic.

## Looking ahead to Week 6

Week 6 is the machinery that makes a full run of HW 4 possible: **Polars and
Parquet, remote machines over SSH, and two batch schedulers** (Grid Engine on the
WRDS Cloud, SLURM on Midway), with NYSE TAQ and Holden-Jacobsen (2014) as the
in-class case study. [HW 5](./HW5.md), the class report, also launches.
