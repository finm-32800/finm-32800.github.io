# A Tour of the Course

Almost everything in this course lives on GitHub. This page points you to the
places you will visit every week.

## This textbook

You are reading it: <https://finm-32800.github.io/>. The sidebar has one
chapter per lecture and one page per homework. The textbook is itself a
product of the tools taught in the course. It is written in Markdown and
Jupyter notebooks, built by a task runner, and published with GitHub Pages. By
the end of the quarter you will be able to build a site like it for your own
project.

## The GitHub organization

The course's repositories are collected in one GitHub organization:
<https://github.com/finm-32800>. A few are worth opening now.

- [`hw0`](https://github.com/finm-32800/hw0) is the repository for
  [Homework 0](../HW0.md).
- [`inclass_examples`](https://github.com/finm-32800/inclass_examples) holds the
  small, self-contained demos used in lecture: virtual environments,
  environment variables, PyDoit, unit tests, Sphinx, Polars, WRDS, Databento,
  and LaTeX.
- [`case_study_clean_trace`](https://github.com/finm-32800/case_study_clean_trace)
  is a complete pipeline that cleans the FINRA TRACE corporate bond data. It is
  the kind of project you will be able to read, run, and extend by week 5.
- [`finm`](https://github.com/jmbejara/finm) is a student-led Python package for
  financial mathematics and quantitative finance. It is published on
  [PyPI](https://pypi.org/project/finm/), so anyone can `pip install finm`, and
  its documentation is at <https://jeremybejarano.com/finm/>. It lives under my
  own GitHub account and not in the course organization. We study how packages
  like it are built in week 4, and students in this course contribute to it.

Most homework repositories are private. Once you enroll, you will receive your
own private copy of each one.

## A pipeline that runs every day

The [FedWatch monitor](https://finm-32800.github.io/case_study_fedwatch/)
recomputes the market-implied probabilities of Federal Reserve rate changes
from fed funds futures prices. Nobody runs it by hand. GitHub Actions pulls new
data, runs the pipeline, and republishes the page on a schedule. Building and
deploying your own copy is a homework assignment later in the quarter.

## The discussion board

Questions about the homework, the final project, and the course go on the
[discussion board](https://github.com/orgs/finm-32800/discussions), not in
email. That way everyone benefits from the answer.

- To be notified of new posts, "watch" the
  [course website repository](https://github.com/finm-32800/finm-32800.github.io):
  click the **Watch** button at the top right of that page.
- Introduce yourself in the
  [introductions thread](https://github.com/orgs/finm-32800/discussions/2). It
  is also the place to find partners for the final project.

## Past final projects

The best preview of what you will be able to do at the end of the course is
what earlier students did. See the
[past final projects](../FinalProject/past_final_projects.md) page. Each one is
a replication of a published finance paper, built as an automated pipeline.
