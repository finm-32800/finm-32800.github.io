# Week 7: Orchestration Across Projects with Apache Airflow

```{note}
The Airflow chapter for this week is posted before class. Until then, this page
is the agenda.
```

This week we step up one level in the automation stack.

`doit` orders the steps *inside* one project. A workflow orchestrator orders *many
projects*: it runs each pipeline on a schedule or when the data it depends on
lands, retries what fails, records every run, and can backfill a gap after an
outage. **Apache Airflow** is the most widely used of these tools, and the class
report is the right thing to point it at, because the report is not one pipeline.
It is twenty-odd small producer pipelines with a single consumer that should
rebuild when all of them have landed.

## Announcements

- **[HW 4](./HW4.md) is due tonight,** at the start of class. It is the last of
  the four coding assignments.
- **The midterm is next week,** Tuesday, November 17, in the first 75 minutes of
  class. It covers weeks 0 through 7, tonight included. Practice questions and
  their answer key are handed out; see [Exam Preparation](./exam_prep.md).
- **[HW 5](./HW5.md) is due Tuesday, December 1.** Aim to open your pull request
  before Thanksgiving. Tonight's Airflow lab runs on the report's pipelines. It
  is done in class and is not graded.

## Objectives

- Say what a workflow orchestrator does that a task runner does not, and name the
  cases where reaching for one is justified.
- Read an Airflow DAG: tasks, dependencies, the schedule, and the data interval.
- Explain what a **data interval** is and why a run for interval *t* must use only
  what was known at *t*. This is the same discipline the equity-premium literature
  imposes on an out-of-sample forecast, enforced by the scheduler instead of by
  hand.
- **Generate** DAGs from a manifest rather than writing them by hand, so that
  twenty contributions do not mean twenty hand-maintained files.
- Schedule a consumer on upstream **assets**, so the report DAG fires when its
  inputs have all updated, with AND semantics across many producers.
- Diagnose a failure from the scheduler's UI and its logs, distinguish a code bug
  from an expired credential from a non-idempotent task, and **backfill** after an
  outage.
- Explain what makes a task idempotent and why a scheduler is unusable without it.

## Agenda Item 1: Why Another Scheduler?

You already have `doit`, and `cron` is coming in week 8. Airflow is not a
replacement for either, and the honest version of this week starts with when *not*
to use it.

- `doit` knows one project's dependency graph and runs it now. `cron` runs a
  command at a time. Neither knows that project B's input is project A's output,
  neither records run history, and neither can tell you *which* of last Tuesday's
  runs failed and why.
- What an orchestrator adds: a graph across projects, run history, retries with
  backoff, alerting, and backfill.
- What it costs: a scheduler, a metadata database, and a webserver to keep alive.
  This is the trade every data platform team argues about, and you should be able
  to argue both sides.

## Agenda Item 2: Airflow Concepts, Then the Course's DAGs

- DAGs, tasks, and operators. Logical date versus wall-clock time, and the data
  interval. `catchup` and what it means to have a scheduler decide it owes you
  fourteen runs.
- **Assets** (datasets): scheduling a DAG on the *arrival of data* rather than on a
  clock, and AND semantics when a consumer depends on many producers.
- **Generated DAGs.** Every contribution already declares its outputs in a
  `chartbook.toml` manifest. So its DAG is *generated* from the manifest, one
  producer DAG per contribution, each running that project's `doit` and then its
  `doit test`. Nobody hand-writes Airflow wiring, and a new contribution appears
  in the graph by virtue of having a manifest.
- **The report DAG** is then `schedule=[Asset(...), ...]` over the contributions'
  outputs: it rebuilds when they have all landed. The report becomes a document
  that regenerates itself whenever any input updates, which is the thesis of this
  course in one artifact.

## Agenda Item 3: Operating It

Reading about a scheduler teaches you nothing. You run one.

- `airflow standalone` against your clone, with a compressed clock so that a
  quarter's worth of run history accumulates in minutes.
- Read the UI: the grid, a task's logs, and the difference between a task that
  failed and a task that never ran because its upstream did.
- **The incident lab.** The faults are the real ones: an upstream renames a column
  and twelve charts go red; a WRDS credential expires and fails in a way that
  looks nothing like a code bug; a contribution appends instead of overwrites and
  is therefore not idempotent, so a retry corrupts it.
- **Backfill** after a simulated outage, and confirm the result matches what a
  timely run would have produced.

The shared instance stays a projector demo that I run. You run yours locally.

## Agenda Item 4: The Paper

[Goyal, Welch, and Zafirov (2024)](https://academic.oup.com/rfs/article/37/11/3490/7749383),
*A Comprehensive 2022 Look at the Empirical Performance of Equity Premium
Prediction*, extends the original seventeen predictors to forty-six and asks
whether any of them beats the historical mean out of sample. The answer is
sobering, and the discipline behind it is the reason this paper belongs in the
orchestration week: a forecast made at date *t* may use only what was known at
*t*. Doing that by hand is error-prone. A scheduler with correct data intervals
makes it structural. Week 9 takes the dataset you are all building and actually
fits models to it.

## Looking ahead to Week 8

The **midterm** is in the first 75 minutes. After it, **CI/CD with GitHub
Actions**, and the contrast with what you ran tonight is the point of the week.
Both are automation triggered by an event; one
is triggered by a push and one by a clock or a dataset. The case study is a live
FedWatch monitor that rebuilds itself every morning, computing the policy
surprises of Bernanke and Kuttner (2005) from the futures data you already pulled
in HW 2.
