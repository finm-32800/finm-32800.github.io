# Study Guide: Week 4

Open-ended questions on the material from week 4. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Databento and Data APIs

*Read: [Databento](../Week7/databento.md),
[Pulling Market Data From Databento](../notebooks/_01_databento_ipynb.ipynb),
and Agenda Item 1 of the [week 4 chapter](../overview_w4.md).*

1. WRDS authenticates you with a password kept in `.pgpass`. Databento
   issues API keys instead. Why create a separate key for each project?
2. What does Databento's `metadata.get_cost` return, and how can a pull use
   it to make sure it never spends money?
3. A Databento request names a dataset, a schema, and the symbols. What does
   the schema choose?
4. What does Databento's Python SDK add on top of its raw HTTP API, and why
   does it matter that the raw API exists?
5. What is a continuous futures series, and what decision does building one
   require? (Read: [LSEG Datastream](../Week7/LSEG_datastream.md).)


## Static Sites and GitHub Pages

*Read: [GitHub Pages and Tearsheets](../Week3/github_pages_preview.md) and the
"Key Concepts" in the
[`github_actions/` examples](https://github.com/finm-32800/inclass_examples/tree/main/github_actions).*

6. What is a static website? Why can you open one from your laptop with no
   server running?
7. What is GitHub Pages? What kind of site can it host, and what can it not
   run?
8. What does an empty `.nojekyll` file at the root of a GitHub Pages site do?
   What can go wrong without it?


## Keeping Output Out of `main`

*Read: Agenda Item 4 of the [week 4 chapter](../overview_w4.md).*

9. The course's `.gitignore` lists `docs/`, the folder `chartbook build`
   writes. Give two reasons not to commit a rebuilt site to `main`.
10. A `gh-pages` branch is kept two commits ahead of `main` by rebasing it
   and force-pushing. What does a force-push do, and when is it safe?


## CI/CD with GitHub Actions

*Read: the [`github_actions/` examples](https://github.com/finm-32800/inclass_examples/tree/main/github_actions)
in the in-class repository,
Agenda Item 5 of the [week 4 chapter](../overview_w4.md), and
[Scheduling Recurring Tasks with Cron](../Week9/cron_jobs.md).*

11. What happens, step by step, when you push a commit to a repository that
   has the workflow below?

   ```yaml
   on: push
   jobs:
     test:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
         - run: pip install -r requirements.txt
         - run: pytest
   ```

12. A workflow file begins with the lines below. Name the three events that
   start the workflow, and say when each one happens.

   ```yaml
   on:
     push:
       branches: [main]
     schedule:
       - cron: '0 22 * * 0'
     workflow_dispatch: {}
   ```

13. A pipeline that runs on GitHub Actions needs an API key. Where should the
   key be stored, and why not in the workflow file or in a committed `.env`
   file?
14. What is the difference between a GitHub-hosted runner and a self-hosted
   one? Why does the course's autograder run on a self-hosted runner?
