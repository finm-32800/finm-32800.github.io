# Homework 3: SVIX and the Probability of a Crash

```{toctree}
:maxdepth: 1
notebooks/_01_es_options_data_ipynb.ipynb
notebooks/_02_svix_and_crash_probability_ipynb.ipynb
notebooks/_03_daily_monitor_ipynb.ipynb
notebooks/_04_single_name_crash_bounds_ipynb.ipynb
```

- **Launched:** week 5. **Due:** Tuesday, November 10, at the start of the
  week 7 session, two weeks after it launches. Also posted on Canvas.
- **Link to Assignment:** a private copy is created for you in the
  [hw-finm-32800](https://github.com/hw-finm-32800) organization, named
  `hw3-` followed by your GitHub username. The copies are created in the
  week of October 27. If you have accepted the invitation to the
  organization, yours simply appears there.
- **Goes with:** weeks 4 and 5

## Overview

Two replications and one deployment.

Ian Martin's SVIX index measures the variance the options market expects
for the S&P 500, the way the VIX does but weighting every strike equally,
and the same option prices give the probability that the market falls 20%
within a month. Martin publishes both series on
[his website](https://personal.lse.ac.uk/martiniw/), and CME computes the
same construction as its
[CVOL](https://www.cmegroup.com/market-data/cme-group-benchmark-administration/cme-group-volatility-indexes.html)
family of volatility indexes for its interest-rate, currency, energy,
metals, and agricultural options. The first replication rebuilds both
numbers from the settlement prices of E-mini S&P 500 options on CME Globex,
pulled from Databento, sets SVIX next to the Cboe VIX, and checks the SVIX
integral against Martin's own published series.

The deployment is the point of the assignment. The CME FedWatch tool you
rebuilt in HW 2 updates itself every day; your replication did not. This
time it will: a GitHub Actions workflow runs your pipeline every morning,
pulls the previous session, adds it to a history kept on a branch of its
own, and republishes your ChartBook site to GitHub Pages, with your
Databento key stored as a secret.
That is continuous deployment, and the autograder that has scored your
pushes since HW 1 is the same machinery.

The second replication is offline. Martin and Shi (2025) extend the crash
probability to single stocks, with bounds on the probability that Apple or
AIG falls 20%, built from the OptionMetrics volatility surface on WRDS. You
complete the query that pulls the surface and run their chain.

As in HW 1 and HW 2, everything runs as a pipeline and the unit tests are
the specification. New this time: the pipeline is run with
[`uv`](https://docs.astral.sh/uv/). `uv run doit` installs Python 3.12 and
the locked dependencies into `.venv/` the first time and runs the pipeline;
there is no environment to create or activate. The same command runs on
the GitHub Actions runner. Run `uv run pytest`, read the first failing
test, and write the code that passes it. Do not edit the test files.

The assignment has four parts, A to D, worth 19 points in total, one per
test. Every push to your private copy runs the suite on the course runner
and posts your score to the repository's Actions tab. Search the repository
for `TODO` to find the blanks.

**Budget your time.** Parts A, B, and D are a few hours together. Part C is
where the hours go, because every run of the workflow takes about ten
minutes and the first ones fail. Finish Part B and run `uv run doit` locally until
it is green before you touch Part C.

## Before you start

- **`uv`.** Install it once from
  [docs.astral.sh/uv](https://docs.astral.sh/uv/getting-started/installation/)
  (one command on macOS, Linux, or Windows). Then, in the repository,
  `uv run doit`. In VS Code, select the interpreter in `.venv/`. If you
  would rather keep working in the `finm` conda environment,
  `requirements.txt` is exported from the lockfile and
  `pip install -r requirements.txt` gives the same versions.
- **Your Databento API key** in `.env`, as in HW 2, and later as a GitHub
  secret. Everything this assignment pulls is on the CME Globex feed and
  free under the course license; the pull checks the cost before
  downloading anything.
- **Your WRDS username** in `.env`, for Part D. The OptionMetrics pull is
  about a minute.
- **A public repository in your own GitHub account**, for Part C. The
  workflow needs free Actions minutes and GitHub Pages, which private
  repositories in the course organization do not have. The README explains
  how to push the same code to both.

## Part A (graded, 2 pts): GitHub Skills

Complete two tutorials from the [GitHub Skills](https://skills.github.com/)
page, using **public repositories** in your own GitHub account, and record
the links in `src/github_skills.py` as in HW 1 and HW 2.

- [Hello GitHub Actions](https://github.com/skills/hello-github-actions):
  write and run your first workflow. Part C asks you to write a real one.
- [Introduction to secret scanning](https://github.com/skills/introduction-to-secret-scanning):
  what GitHub does when a key reaches a public repository anyway. In Part
  C your Databento key goes into a public repository's secret store, and
  it must never go anywhere else.

## Part B (graded, 5 pts): SVIX and the probability of a crash

From one session's settlement prices for the two end-of-month expirations
that bracket 30 days. The pull, the parity forward, the implied
volatilities, and the smile on a fine grid are written for you. Three
formulas are not:

- [E-mini S&P 500 Options Data from Databento](notebooks/_01_es_options_data_ipynb.ipynb)
- [Replicating SVIX and the Probability of a Crash](notebooks/_02_svix_and_crash_probability_ipynb.ipynb)

The blanks, each a formula the second guide derives:

- **SVIX²** (`src/svix.py`). The simple variance swap rate is an integral
  of out-of-the-money option prices across strikes, Martin's equation (12).
  Two tests: a flat smile, where the answer has a closed form, and
  Martin's published SVIX, below.
- **The risk-neutral distribution** (`src/rnd.py`). The slope of the call
  curve in the strike is the risk-neutral CDF of the return, Breeden and
  Litzenberger's result. The isotonic fix that follows it is given. Two
  tests: the result is a valid CDF, and a flat smile gives the lognormal.
- **The log investor's crash probability** (`src/crash_prob.py`). Martin
  turns the risk-neutral probability of a 20% drop into the belief of an
  investor who holds the market, with one more integral of the same
  distribution. One test against the closed form.

Four of the five tests run offline on synthetic smiles and need no data.

**Does the replication match?** Martin publishes his one-month SVIX for
1996 to 2012. The fifth test runs your SVIX formula on the OptionMetrics
S&P 500 surface at every month-end in that period and requires it to stay
within a fraction of a vol point of his series, so it needs the
OptionMetrics pull from Part D. No exchange publishes an SVIX for the
S&P 500: CME's CVOL indexes use the same construction, but not for equity
options. The fourth guide shows the comparison day by day, and the second
guide sets SVIX next to the VIX.

## Part C (graded, 8 pts): the daily monitor

- [The Daily Monitor](notebooks/_03_daily_monitor_ipynb.ipynb)

Four things to complete, then one to do.

- **The monitor task** (`dodo.py`, 1 pt). `task_monitor` pulls the latest
  session, adds its row to `_output/svix_monitor_history.csv` if the
  history does not have it, and redraws the chart. Unlike every other task
  it must run every time it is invoked. `task_backfill`, written for you,
  does the same over a range of dates; that is how the history behind the
  chart is filled in, once.
- **The ChartBook manifest** (`chartbook.toml`, 1 pt). Register the monitor
  notebook and the history dataframe.
- **The workflow** (`.github/workflows/deploy_pages.yml`, 5 pts). The file
  is given as a skeleton with five blanks: the schedule, the secret, the
  permission the job needs to push, the step that runs the pipeline, and
  the branch the site is deployed to. The tests read the YAML. The other
  steps are written for you; read their comments. One restores the history
  from the `monitor-history` branch and another records the day's row
  there, so no output ever lands on `main`; a workflow that pushed to
  `main` on every run would be a workflow that triggers itself. Another
  tells a data-source outage from a bug: if Databento or FRED is still down
  after the pull's retries, the run ends with a warning and yesterday's
  site stays up. See
  [GitHub Actions and an Interactive Dashboard](./Week9/github_actions_interactive_dashboard.md),
  [cron](./Week9/cron_jobs.md), and the
  [`github_actions/` examples](https://github.com/finm-32800/inclass_examples/tree/main/github_actions)
  in the in-class repository, which deploy a daily pipeline with a
  Databento secret the same way.
- **The live site** (`src/published_site.py`, 1 pt). Push the completed
  repository to a **public** repository in your own account, add your
  Databento key as a repository secret, run the deploy workflow once from
  the Actions tab, run the backfill workflow once with a start date so the
  chart has a history behind it, turn on GitHub Pages from the `gh-pages`
  branch, and record the address of the site and of the repository. The
  steps are in the
  README. Your private copy in the course organization keeps getting your
  pushes for grading; the workflow does not run there.

When it works, the chart on your site gains one point every morning.

## Part D (graded, 4 pts): single names, from the OptionMetrics surface

- [Crash Bounds for Single Names (Martin and Shi 2025)](notebooks/_04_single_name_crash_bounds_ipynb.ipynb)

Martin and Shi (2025) bound the probability that a single stock crashes
using the risk-neutral distributions of the stock and of the market. Their
code, from two of last summer's students, is given; so are the pulls of
prices and rates. The blank:

- **The surface query** (`src/pull_optionmetrics.py`). Complete the
  `WHERE` clause that selects the S&P 500, Apple, and AIG at the 30- and
  91-day maturities over the sample window from the OptionMetrics surface
  table. Two tests run the query against a small in-memory database with
  the WRDS layout, as HW 1 tested its queries.

Then `uv run doit single_names` pulls the surface and runs the chain, with
your Breeden-Litzenberger step from Part B inside it. The third test checks
that the chain ran across the sample, and the fourth runs the index
probability from the surface against the monthly series Martin publishes,
on the 1996 to 2012 overlap.

**Does the replication match?** The fourth guide shows Apple's and AIG's
bounds next to the paper's Figure 1, and our index probability next to
Martin's series.

## Credits

The engine that recovers the risk-neutral distribution and computes the
bounds is the work of Riley Haas and Marija Jovicic, from their Summer 2026
replication of Martin and Shi, used with their permission. The SVIX
integral follows Andrew Heekin and Chandler Bird's Summer 2026 replication
of Martin (2017).
