# Week 9: Exam, Orchestration with Apache Airflow, and Experiment Tracking

```{note}
The Airflow chapter for this week is posted before class. Until then, this page
is the agenda.
```

The **exam** takes the first 75 minutes. After it, two short topics close the
quarter.

The first is orchestration. `doit` orders the steps inside one project. An
orchestrator such as **Apache Airflow** orders many projects: it runs each
pipeline on a schedule or when the data it depends on lands, retries what
fails, records every run, and can backfill a gap after an outage. The class
report is the right thing to point it at, because it reads many upstream
pipelines, and each publishes new snapshots on its own schedule.

The second is experiment tracking with **MLflow**: recording which data, code,
and settings produced a number, so that you can still answer that question
months later.

## Announcements

- **The exam is today, 9:30 to 10:45 am,** the first 75 minutes of class. In person,
  closed-book, closed-notes, multiple choice, on a bubble sheet. It covers weeks 0
  through 8. Each question has four options, **one or more may be correct**, and a
  question earns its point only if every bubble is right. There is no partial
  credit. See [Exam Preparation](./exam_prep.md). Today's Airflow and MLflow
  material comes after the exam and is not on it.
- **Final project presentations are next week.** Your project is due 24 hours
  before your group's 30-minute meeting with me. There are no slides and no
  demo, because I will already have run your code. I question each member
  individually, and those answers are graded.
- **This is the only exam.** There is no separate final exam.

## Objectives

- Say what a workflow orchestrator does that a task runner and a CI service
  do not, and when reaching for one is justified.
- Read an Airflow DAG: tasks, dependencies, the schedule, and the data
  interval.
- Explain why a run for interval *t* may use only what was known at *t*, and
  how a scheduler enforces it.
- Explain what makes a task idempotent, and why retries and backfills depend
  on it.
- Say what experiment tracking records, and why a comparison of methods is
  only meaningful when the data is held fixed.

## Agenda Item 1: The Exam

First 75 minutes. Bubble sheets and booklets are provided; bring a pencil. Room
and start time are on Canvas.

## Agenda Item 2: Apache Airflow

- **Why another scheduler.** `doit` knows one project's graph and runs it
  now. `cron` and GitHub Actions run a job on a clock or a push. None of them
  knows that project B's input is project A's output, and none keeps a run
  history across projects. An orchestrator adds that, plus retries and
  backfill, at the cost of a scheduler, a database, and a webserver to keep
  running.
- **The concepts.** DAGs, tasks, and operators; the logical date versus the
  wall clock; the data interval; `catchup`, and what it means for a scheduler
  to decide it owes you fourteen runs.
- **Data-aware scheduling.** A DAG can run when its inputs land rather than
  at a time. The class report is one consumer of many producers: it should
  rebuild when its upstreams have all published.
- **A demo** on the class report's pipelines: a task that fails, a task that
  never ran because its upstream failed, a retry, and a backfill.
- **Point-in-time discipline.**
  [Goyal, Welch, and Zafirov (2024)](https://academic.oup.com/rfs/article/37/11/3490/7749383)
  ask whether any of 46 predictors beats the historical mean of the equity
  premium out of sample. A forecast made at date *t* may use only what was
  known at *t*. Doing that by hand is error-prone; a scheduler with correct
  data intervals makes it structural.

## Agenda Item 3: Experiment Tracking with MLflow

- The problem: a notebook that reports 0.043 and a directory of seventeen
  slightly different scripts is not a result. What you need recorded is the
  parameters, the data version, the code version, the metric, and the
  artifact.
- MLflow's pieces: runs, experiments, parameters, metrics, and artifacts, and
  logging from inside a `doit` task, so tracking is part of the pipeline.
- The benchmark of
  [Bejarano et al. (2026)](https://www.financialresearch.gov/working-papers/2026/08/25/time-series-forecasting-methods-financial-markets/)
  holds the [FTSFR](./Week3/ftsfr.md) data fixed and varies only the method.
  That design is an experiment-tracking problem by construction, and it is
  why the class report's pins matter.

## Looking ahead to Week 10

Final project presentations. Each group meets with me for 30 minutes. I will
already have run your pipeline, so there are no slides and no demo, and I
question each member individually about the paper, the data, the design
choices, and the extension. Those answers are graded. See the
[Final Project Instructions and Rubric](./FinalProject/final_project_rubric.md).
