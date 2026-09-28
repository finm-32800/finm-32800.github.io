# Homework 5: The Collaborative Report and Airflow

- **Launched:** week 6. **Due:** week 9. The date is posted on Canvas.
- **Link to Assignment:** posted at launch
- **Goes with:** weeks 6, 7, and 9

## Overview

The whole class builds one report, and then a scheduler keeps it current.

The report is the [Collaborative Report](collaborative_report.md): a single,
publication-style PDF on the state of the equity premium predictors, built
from one shared repository. The topics are the predictors of
[Goyal, Welch, and Zafirov (2024)](https://academic.oup.com/rfs/article/37/11/3490/7749383),
*A Comprehensive 2022 Look at the Empirical Performance of Equity Premium
Prediction*. Each student claims one predictor through the issue tracker,
builds the pipeline that pulls its data and brings the series up to today,
contributes one figure and one paragraph through a pull request, reviews two
classmates' pull requests, and ships a test that the shared repository's CI
runs on every contribution. This half is taught in week 6 and uses the
workflow of the [GitHub Issues and Pull Requests](Week6/GitHub_pull_requests.md) chapter.

The second half, taught in week 7, is orchestration. `doit` runs the steps
inside one project; Apache Airflow runs many projects on a clock, or when
their inputs land. Every contribution already declares its outputs in a
`chartbook.toml` manifest, so its Airflow DAG is generated from the manifest
rather than written by hand, and the report's DAG is scheduled on the set of
contributions' outputs: it rebuilds when they have all landed. You run the
scheduler locally with a compressed clock, accumulate real run history, learn
to read a failure from the UI and the logs, and backfill after a simulated
outage.

The last part, taught in week 9, is a forecasting DAG with experiment
tracking. Following the open benchmark of
[Bejarano et al. (2026)](https://www.financialresearch.gov/working-papers/2026/08/25/time-series-forecasting-methods-financial-markets/),
you fit a few of its univariate forecasting methods to the class's predictor
dataset on a schedule, log every run with MLflow, and monitor forecast error
against the historical mean as new data arrives. The extension that ties the
two papers together: add the predictors as exogenous regressors and see
whether anything survives out of sample.

## Learning Outcomes

- Contribute to a shared codebase under peer review: issues, branches, pull requests, a style guide, and tests in CI.
- Build a data pipeline on data another repository already produces, through the ChartBook catalog.
- Generate Airflow DAGs from project manifests, schedule a consumer on upstream assets, diagnose failures from the scheduler's UI and logs, and backfill.
- Track model runs with MLflow and monitor a forecasting model in production.

## Grading

Your report contribution is graded out of 10 points as described on the
[Collaborative Report](collaborative_report.md) page. The scheduler and
forecasting parts are autograded through DAG-integrity tests and a test run
of your DAGs, plus a short incident note: what broke, how you knew, and what
you changed so it cannot recur. The point split and the repository link are
posted at launch.
