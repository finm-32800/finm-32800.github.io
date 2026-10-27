# Week 7: Orchestration Across Projects with Apache Airflow

```{toctree}
:maxdepth: 1
Week6/GitHub_pull_requests.md
Week7/study_guide.md
```

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

- **[HW 3](./HW3.md) is due today at 9:30 am,** the start of class.
- **[HW 4](./HW4.md) and [HW 5](./HW5.md):** dates to be announced on
  Canvas.
- **Today's Airflow lab** runs on the class report's pipelines. It is done in
  class and is not graded.
- **Today's material is on the exam,** Tuesday, December 1. See
  [Exam Preparation](./exam_prep.md).
- **Project consultations end this week,** on Friday, November 13. If your
  group has not had one, book it today: [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile).

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
- Say what containers and Kubernetes add beneath an orchestrator, and how a
  container image relates to the `uv` and `pixi` lockfiles from week 4.

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

## Agenda Item 4: Where the Tasks Run --- Containers and Kubernetes

Briefly, because the job postings name these tools and an orchestrator is where
you meet them.

- **Containers.** A Docker image freezes the whole environment a task runs in,
  operating system included. That is one level below the conda, `uv`, and
  `pixi` lockfiles from week 4, and the same image runs on your laptop, a CI
  runner, or a cluster.
- **Kubernetes** schedules containers across a fleet of machines. Production
  Airflow commonly runs each task in its own container on Kubernetes (the
  `KubernetesExecutor`, or the `KubernetesPodOperator` for a single task), so
  twenty producer pipelines with conflicting dependencies can share one
  scheduler.
- We do not run a cluster. The goal is to recognize the layers when a posting
  names them: the lockfile pins the packages, the image pins the machine, and
  the orchestrator decides when and where the image runs.

## Agenda Item 5: Issues and Pull Requests

[GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md). The
final project's rubric requires every pull request to be reviewed by a
teammate.

- **An issue** records a task or a bug. **A pull request** proposes a
  change, and writing `fixes #12` in it closes issue 12 when the change
  merges.
- **`git pull` and a pull request** sound alike: the first updates your own
  copy, the second asks someone else to pull your branch.
- **Why `main` is protected,** and a review is required before anything
  merges.

## Agenda Item 6: The Paper

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

Week 8 is **packaging, CI, and code quality**: how the packages you install
are built, tested across Python versions, documented, and released by a
workflow, and the checks that run before a push. The paper is Bernanke and
Kuttner (2005), whose policy surprises come from the same fed funds futures
as the FedWatch replication.
