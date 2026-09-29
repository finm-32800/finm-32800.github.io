# Week 9: Exam, and Basic MLOps --- Experiment Tracking and Monitoring Models

```{note}
The MLflow chapter for this week is posted before class. Until then, this page is
the agenda.
```

Two things happen this week. The **exam** takes the first 75 minutes. Then
MLOps.

MLOps is the discipline of keeping a model healthy *after* it ships. A model in
production is a pipeline that never stops running: new data arrives on a schedule,
the model recomputes its outputs, and someone has to notice when the data goes bad
or the model's behavior drifts. Every tool in this course now points at that
problem, and this week assembles them.

The data is the one you built. The class's predictor dataset from
[HW 5](./HW5.md) is the input, the Airflow DAGs from week 7 are the scheduler, and
the benchmark of [Bejarano et al. (2026)](https://www.financialresearch.gov/working-papers/2026/08/25/time-series-forecasting-methods-financial-markets/)
supplies the models. Nothing this week is a toy.

## Announcements

- **The exam is tonight,** in the first 75 minutes of class. In person,
  closed-book, closed-notes, multiple choice, on a bubble sheet. It covers weeks 0
  through 8. Each question has four options, **one or more may be correct**, and a
  question earns its point only if every bubble is right. There is no partial
  credit. See [Exam Preparation](./exam_prep.md).
- **Nothing else is due.** All five homeworks are behind you. Tonight's
  forecasting lab is done in class and is not graded.
- **Final project presentations are next week.** Every group presents in week 10,
  with an individual oral defense for each member. You will be asked to run and
  modify your own project live.
- **This is the only exam.** There is no separate final exam.

## Objectives

- Say what experiment tracking is for, and why "which run produced this number?"
  is a question you must be able to answer months later.
- Instrument a training run with MLflow: parameters, metrics, artifacts, and the
  code version, and compare runs.
- Fit the benchmark's univariate forecasting methods to a panel of series and
  evaluate them out of sample against the historical mean.
- Explain why the historical mean is the benchmark that matters in this literature,
  and what it means for a method to lose to it.
- Add **drift checks** to a pipeline, so it refuses quietly corrupted inputs rather
  than publishing them, and distinguish data drift from model decay.
- Decide, with evidence, when a deployed model should be retired.
- Close the loop: a scheduled retrain that publishes itself, using the machinery
  from weeks 7 and 8.

## Agenda Item 1: The Exam

First 75 minutes. Bubble sheets and booklets are provided; bring a pencil. Room
and start time are on Canvas.

## Agenda Item 2: Experiment Tracking with MLflow

- The problem: a notebook that reports 0.043 and a directory of seventeen slightly
  different scripts is not a result. What you need recorded is the parameters, the
  data version, the code version, the metric, and the artifact.
- MLflow's pieces: runs, experiments, parameters, metrics, artifacts, and the
  tracking UI. Logging from inside a `doit` task, so tracking is part of the
  pipeline and not a thing you remember to do.
- Comparing runs, and why the comparison is only meaningful if the data was held
  fixed. This is the discipline the benchmark paper is built on.

## Agenda Item 3: The Benchmark

[Bejarano et al. (2026)](https://www.financialresearch.gov/working-papers/2026/08/25/time-series-forecasting-methods-financial-markets/),
*An Open Benchmark for Evaluating Time Series Forecasting Methods across Financial
Markets* (OFR Working Paper), is the capstone paper for three reasons: its
pipelines are the [FTSFR](./Week3/ftsfr.md) datasets you met in week 2, its design
holds the data fixed and varies only the method, and it uses no exogenous
regressors, which leaves an obvious extension open for you.

- The design: a fixed panel of financial series, a dozen univariate methods, one
  evaluation protocol. Why that is an experiment-tracking problem by construction.
- The finding worth sitting with: across financial series, most of these methods
  struggle to beat simple baselines. That is not a failure of the software.
- Former students of this course are coauthors, and the reason the paper was
  possible is that every dataset in it rebuilds from source with one command.

## Agenda Item 4: The Exercise

Take a handful of the benchmark's baseline methods, a historical mean, an ARIMA,
a Theta method, and one neural method, and run them as a **scheduled DAG** over the
class's predictor dataset and a few FTSFR series. Log every run to MLflow: the
method, the data interval, and the out-of-sample error against the historical mean.

Then the two halves of the lesson:

1. **Monitoring.** Watch forecast error as new intervals land. Decide when a model
   should be retired. The instability of predictor performance over time, which is
   a table in the paper, becomes something you experience as an operations problem.
2. **The extension.** The benchmark deliberately uses no exogenous regressors, and
   the class report has just assembled forty-odd predictors. Add them as exogenous
   regressors to the equity-premium series and see whether anything survives out of
   sample. Goyal, Welch, and Zafirov would tell you to expect very little, which is
   exactly why doing it honestly, with the point-in-time discipline the scheduler
   enforces, is the right last exercise of the quarter.

## Agenda Item 5: Drift, and Closing the Loop

- **Input drift** versus **model decay**: a schema change, a unit change, a vendor
  backfill, and a regime change all look different in the logs, and you should be
  able to tell them apart.
- Validation checks from week 5, now running on every scheduled arrival, with a
  threshold that fails the DAG rather than publishing a corrupted forecast.
- Retrain, publish, and alert with no human in the loop, using Airflow from week 7
  and Actions from week 8. This is the whole course in one diagram: extract, clean,
  validate, transform, model, track, publish, monitor, and rerun.

## Looking ahead to Week 10

Final project presentations and oral defenses. Each group presents its completed
pipeline, and each member is individually quizzed on both the analysis and the
tools used to build it. See the
[Final Project Instructions and Rubric](./FinalProject/final_project_rubric.md).
