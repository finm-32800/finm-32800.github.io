# Week 4: Data APIs, Static Sites, and GitHub Actions

```{toctree}
:maxdepth: 1
Week7/databento.md
notebooks/_01_databento_ipynb.ipynb
Week7/LSEG_datastream.md
Week3/github_pages_preview.md
Week9/cron_jobs.md
Week4/study_guide.md
```

Week 3 left two pieces of HW 2 unfinished: the vendor its futures come from,
and the website its results go to. This week takes both, and then the
machinery that will run them without you. Databento first, as a modern data
API: a key per project, a cost you can check before you pay it, and the same
request made from a Python SDK or as a raw HTTP call. Then the math behind
CME's FedWatch tool. Then the web itself: what a static HTML page is, how
GitHub Pages serves a folder of them, and why the folder `doit` builds stays
out of `main`. We keep it out by hand, with a `gh-pages` branch, and the
chore of doing that is the case for GitHub Actions, which we read through
the workflow that has graded every push since HW 1.

## Announcements

- **[HW 1](./HW1.md) is due tonight,** at the start of class.
- **[HW 2](./HW2.md) is due next Tuesday, October 27.** Questions at the start
  of class, and the FedWatch half is tonight's first item.
- **[HW 3](./HW3.md) launches next week,** due Tuesday, November 10, in week
  7. Tonight introduces it; your private copy is created the week it launches.
- **Project assignments go out Wednesday morning.** The preference survey
  closes tonight at 11:59 pm. **Consultations start this week** and run
  through week 7; if your group has not booked its
  [consultation](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile), do it now.
- **Databento API keys.** HW 3 uses the key you set up for HW 2, and asks you
  to store it as a GitHub Actions secret. Everything we pull is on the CME
  Globex feed (`GLBX.MDP3`) and free under the course license.

## Objectives

- Pull data from a vendor's API with a key kept in `.env`, say why a key per
  project helps with access and metering, and check what a query costs
  before running it: [Databento](./Week7/databento.md) and
  [Pulling Market Data From Databento](notebooks/_01_databento_ipynb.ipynb).
- Say what an SDK adds on top of a raw HTTP API, and make the same request
  both ways.
- Explain how fed funds futures prices imply the probability of a rate move
  at the next FOMC meeting.
- Explain what a static website is, and publish a folder of HTML with GitHub
  Pages: [Publishing to GitHub Pages](./Week3/github_pages_preview.md).
- Say why a pipeline's built output stays out of `main`, and keep a
  `gh-pages` branch by hand.
- Read a GitHub Actions workflow: its triggers, including a `cron`
  schedule, its permissions, its jobs and their conditions, its runners, its
  steps, and its secrets: [Cron Jobs](./Week9/cron_jobs.md).

## Agenda Item 1: Databento, a Modern Data API

[Databento](./Week7/databento.md), with the
[`databento/` examples](https://github.com/finm-32800/inclass_examples/tree/main/databento)
in the in-class repository, which are also a notebook:
[Pulling Market Data From Databento](notebooks/_01_databento_ipynb.ipynb).

- **A key per project.** WRDS authenticates you with a password kept in
  `.pgpass`. Databento issues API keys, as many as you like: one per
  project, so each can be revoked on its own and each project's usage can be
  metered separately. The key lives in `.env`, never in the code.
- **A subscription, or pay as you go.** The course's subscription covers
  CME Globex history with no download limit, but no live feed. A personal
  account pays by the gigabyte instead. `metadata.get_cost` prices any query
  for free before you run it, which makes a cost guard possible: stop unless
  the estimate is zero.
- **Schemas.** One parameter decides whether a request returns trades,
  daily bars, the exchange's settlement statistics, or the full order book.
- **An SDK, and the raw API under it.** The Python client wraps an HTTP API.
  The same request made with `curl` on the command line, or with
  `requests.post` in Python, returns the same data, which is what lets any
  language talk to the service.
- **An aside: [LSEG Datastream](./Week7/LSEG_datastream.md)** on WRDS, for
  continuous futures series, the decision of how to roll from one contract
  to the next, and the tools for finding a series' code.

## Agenda Item 2: FedWatch

[HW 2](./HW2.md) is due in a week. Then the half of it that week 3 did not
reach. Open the
[CME FedWatch tool](https://www.cmegroup.com/markets/interest-rates/cme-fedwatch-tool.html).
A 30-Day Fed Funds futures contract settles at 100 minus the month's average
effective fed funds rate, so its price is the market's forecast of that
average. Split the month at the FOMC meeting: the days before it sit at
today's rate, and the days after it at either today's rate or a new one.
With two outcomes on the table, stay or move by 25 basis points, the price
pins down the probability of the move.

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data_ipynb.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication_ipynb.ipynb)

## Agenda Item 3: Static Sites and GitHub Pages

[Publishing to GitHub Pages](./Week3/github_pages_preview.md).

- **What an HTML page is.** A text file with a head and a body: headers,
  paragraphs, lists, and links. Pages that link to each other by relative
  paths are a website, and nothing about them needs a server: open one from
  your disk and the browser renders it. A dynamic site runs code on a server
  for every visitor; a static one does not.
- **Publishing a folder.** `chartbook build` writes every page of a
  project's site into `docs/`. Any host that serves files can publish it.
  GitHub Pages serves a branch's root or its `docs/` folder. Done live: a new
  public repository with the HW 2 site copied in, and Pages turned on.
- **`.nojekyll`.** Pages was built for Jekyll sites, and an empty
  `.nojekyll` file says this one is plain HTML.

## Agenda Item 4: Keeping Output Out of `main`

Serving `docs/` from `main` fails, because `docs/` is in `.gitignore`, and it
should be. `main` is for source code. A rebuilt site is hundreds of files
nobody reads, many of them change on every rebuild even when nothing
meaningful did, and committing them each time bloats the history.

- **A `gh-pages` branch, by hand.** Branch from `main`, remove `docs/` from
  that branch's `.gitignore`, commit the site, push the branch, and point
  Pages at it.
- **Keeping it current.** After each change to `main`, rebase `gh-pages`
  onto it, rebuild, amend the site's commit, and force-push. The branch stays
  two commits ahead of `main` and never accumulates history. A force-push
  overwrites the remote, which is safe only on a branch no one else works on.
- **Every one of those steps is manual,** which is the case for automating
  them. In [week 5](./overview_w5.md) a workflow does it, and HW 3 hands you
  that workflow.

## Agenda Item 5: GitHub Actions, Read from the Autograder

Open `.github/workflows/tests.yml` in your HW repository, the workflow that
has graded every push since HW 1. Turning on Pages added a second workflow,
*pages build and deployment*, which GitHub wrote for you.

- **`on`:** what starts a workflow. Every push, the *Run workflow* button
  (`workflow_dispatch`), or a schedule written in
  [cron](./Week9/cron_jobs.md) syntax, always in UTC.
- **`permissions`, `jobs`, and `if`:** what the workflow may change, the
  jobs it runs, themselves a small DAG, and when a job is skipped. The
  grader skips the instructor's pushes and the first commit of a new copy.
- **`runs-on`:** where a job runs. GitHub's hosted runners are small virtual
  machines, created and destroyed for every run, with free minutes for
  public repositories. The grader runs on a self-hosted runner instead: a
  Mac mini that keeps the course's cached data.
- **`steps`:** each one `uses` a published action, such as
  `actions/checkout`, or `run`s commands in a shell. The grader's steps build
  the environment, find the cached data, run `pytest`, and turn the results
  into a score.
- **Secrets:** a repository secret (*Settings* > *Secrets and variables* >
  *Actions*) reaches a step through `env:` and `${{ secrets.NAME }}`, and the
  pipeline reads it from the environment just as it reads `.env` locally.
  Databento's key works this way. WRDS's `.pgpass` would not, on a runner
  rebuilt for every run.

## Agenda Item 6: Where CI/CD Lives

Every open-source package runs workflows like these. Open
[pandas' Actions tab](https://github.com/pandas-dev/pandas/actions): tests on
every pull request, so nothing regresses, and builds published
continuously, nightly ones included. That is continuous integration and
continuous deployment. Packaging and its CI come back in
[week 8](./overview_w8.md).

## Final Projects

Assignments go out Wednesday morning. Each group books one
[consultation](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile) in weeks 4 through 7.
The rubric requires every pull request to be reviewed by a teammate; the
pull-request workflow is covered in [week 7](./overview_w7.md).

## Looking ahead to Week 5

Week 5 automates what this week did by hand: a live monitor that a workflow
updates every day, deployed in class, then the math of HW 3. It also catches
up on what this week did not reach: modules and packages, and `uv`.
