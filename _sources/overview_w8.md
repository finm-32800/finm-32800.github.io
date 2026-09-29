# Week 8: CI/CD with GitHub Actions

```{toctree}
:maxdepth: 1
Week9/github_actions_interactive_dashboard.md
Week9/cron_jobs.md
```

CI/CD means continuous integration and continuous deployment: every time code is
pushed, an automated service checks out the change, runs the full test suite, and,
if everything passes, rebuilds and republishes the output with no human in the
loop. **GitHub Actions** is GitHub's built-in CI/CD service. You have been on the
receiving end of it since HW 1, because the autograder is a workflow. This week
you write the workflow.

The week is built around a contrast with last week. Airflow and GitHub Actions are
both automation triggered by an event. Actions is triggered by a **push**; a
scheduler is triggered by a **clock or a dataset**. Knowing which reaches for
which is the practical skill, and the case study makes the boundary concrete: a
model that must rebuild itself every morning whether or not anyone pushed code.

## Announcements

- **[HW 5](./HW5.md) is due tonight,** at the start of class. It is the last
  homework.
- **No class next week.** November 24 falls in Thanksgiving break. Week 9 meets
  on December 1.
- **The exam is December 1,** in the first 75 minutes of class, covering weeks 0
  through 8, tonight included. Practice questions and their answer key are
  handed out tonight; see [Exam Preparation](./exam_prep.md).
- **Final project work should be underway.** Week 10 is the presentation and the
  oral defense, and the defense assumes you can run and modify your own project
  live: manage the environment, run the pipeline, and use SSH.
- **Final presentation sign-ups.** Book your week 10 slot; all groups present in
  week 10.

## Objectives

- Read and write a GitHub Actions workflow: triggers, jobs, steps, runners,
  matrices, caching, and secrets.
- Explain what the homework autograder has been doing all quarter, and why a
  workflow you cannot read is a workflow you cannot trust.
- Deploy a site automatically from a workflow rather than by hand, and say why
  that is strictly better than the manual copy you did in HW 2.
- Schedule recurring work with [cron](./Week9/cron_jobs.md), read a crontab line,
  and use `schedule:` in a workflow.
- Say clearly where GitHub Actions stops and an orchestrator starts, and defend
  the choice either way.
- Publish an interactive chart as static HTML and understand why a static widget
  needs no server:
  [GitHub Actions and an Interactive Dashboard](./Week9/github_actions_interactive_dashboard.md).
- Explain what a monetary policy surprise is, how it is extracted from fed funds
  futures, and what Bernanke and Kuttner (2005) find about the stock market's
  reaction to it.

## Agenda Item 1: GitHub Actions from the Inside

- The anatomy of a workflow file: `on`, `jobs`, `runs-on`, `steps`, and where the
  YAML lives.
- **Start with the autograder.** Open the workflow that has been grading your
  homework. It checks out your code, builds the environment, runs `pytest`, and
  reports. Nothing magic, and now you can modify it.
- Secrets and why a workflow needs them: the same `.env` problem from week 1, in a
  place you cannot see. What is safe to put in a repository and what is not, and
  what to do if a key does leak.
- Caching and matrices: making a slow workflow tolerable, and testing on more than
  one Python version.
- **Self-hosted runners,** briefly, and why this course uses one.

## Agenda Item 2: Scheduling, and the Boundary

[Cron Jobs](./Week9/cron_jobs.md): the classic Unix scheduler, the five fields, and
the traps (the environment a cron job inherits is not your shell's, and a job that
overruns its interval will overlap itself). Then `schedule:` in a GitHub Actions
workflow, which is cron with someone else's machine.

Then draw the boundary explicitly, because you have now used all three:

| | Triggered by | Knows about | Reach for it when |
|---|---|---|---|
| `doit` | you, at a prompt | one project's file graph | building or rebuilding a project |
| Actions / cron | a push, or a clock | one repository | tests on every change; one job on a schedule |
| Airflow | a clock, or data landing | many projects, with history | pipelines depend on each other, and you need to know what ran |

## Agenda Item 3: Publishing a Live Site

[GitHub Actions and an Interactive Dashboard](./Week9/github_actions_interactive_dashboard.md):
Plotly figures exported as self-contained HTML, a `docs/` folder, and a workflow
that rebuilds and republishes it. The site is interactive in the browser with no
server anywhere, which is why this is the cheapest way to put a live number in
front of someone.

## Agenda Item 4: The Case Study --- A Live FedWatch Monitor

In HW 2 you computed FOMC meeting-outcome probabilities from 30-Day Fed Funds
futures on one date. A number computed once is an exercise; a number that is right
every morning is a product. The case study,
[finm-32800/case_study_fedwatch](https://github.com/finm-32800/case_study_fedwatch),
is the same math wrapped in a workflow that pulls fresh futures data on a
schedule, recomputes, rebuilds the site, and republishes, forever, with nobody
watching:

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication.ipynb)

The things that go wrong here are the interesting part, and they are the same
failure modes you met in the incident lab last week: the vendor is briefly down,
a contract rolls, a holiday means no data and the pipeline must not interpret that
as a zero, and the workflow must be idempotent because it will run again tomorrow.

**The paper.**
[Bernanke and Kuttner (2005)](https://doi.org/10.1111/j.1540-6261.2005.00760.x),
*What Explains the Stock Market's Reaction to Federal Reserve Policy?*, splits a
rate change into the part the market expected and the part it did not, using the
futures price change on the announcement day, and shows that equities respond to
the surprise and essentially not at all to the expected part. It is the natural
paper for this week because its entire empirical content depends on a
point-in-time measurement: what was priced *before* the announcement. Your monitor
is the machine that captures that, every day, without being asked.

## Looking ahead to Week 9

The **exam** is in the first 75 minutes. After it, the last week is **basic
MLOps**: what it takes to keep a model healthy after it
ships. Experiment tracking with MLflow, drift checks on the inputs, and fitting the
forecasting methods of the open benchmark of Bejarano et al. (2026) to the
predictor dataset the whole class built. It meets on December 1, after the
break.
