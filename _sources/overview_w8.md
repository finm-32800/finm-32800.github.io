# Week 8: Packaging, CI, and Code Quality

```{toctree}
:maxdepth: 1
Week6/python_packaging_with_hatch.md
Week5/sphinx.md
Week8/study_guide.md
```

Week 4 read a workflow and week 5 deployed one. This week turns to where most
workflows in the wild live: Python packages. Every package you have installed
this quarter was built, tested on several versions of Python, documented, and
released by one. We look at how, then at the checks that run before a push,
Ruff and pre-commit, and close by drawing the line between a CI service and
the orchestrator of last week.

## Announcements

- **[HW 4](./HW4.md) and [HW 5](./HW5.md):** dates to be announced on
  Canvas.
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
predictor dataset the whole class built. It meets on December 1, after the
break.
