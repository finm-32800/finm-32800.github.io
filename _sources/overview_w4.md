# Week 4: GitHub Pages, GitHub Actions, and Python Packaging

```{toctree}
:maxdepth: 1
Week3/uv_and_pixi.md
Week6/python_packaging_with_hatch.md
notebooks/_01_modules_and_packages_ipynb.ipynb
Week5/sphinx.md
Week6/GitHub_pull_requests.md
Week4/study_guide.md
```

Last week ended with a replication of the CME FedWatch tool that nobody but
you can see, and that was stale the moment you ran it. Tonight is about
fixing both. A `docs/` folder becomes a website with GitHub Pages, and a
workflow on GitHub Actions rebuilds that website every morning, with no one
at the keyboard. That is continuous deployment, and the autograder that has
scored your pushes since HW 1 is the same machinery. Where else does CI/CD
live? In every Python package you have installed this quarter, which is the
cue for a short tour: what a package is, starting from a single `.py` file,
and how one is built, tested, and released.
[HW 3](./HW3.md) then launches: a daily monitor of the S&P 500's
option-implied crash probability, deployed with a workflow you write.

## Announcements

- **[HW 1](./HW1.md) is due tonight,** at the start of class.
- **[HW 2](./HW2.md) is due next Tuesday, October 27.** Questions at the start
  of class, and the FedWatch half is tonight's first item.
- **[HW 3](./HW3.md) launches tonight,** due Tuesday, November 3, in week 6.
  Your private copy is created later this week, by Friday, October 23.
- **Project assignments go out Wednesday morning.** The preference survey
  closes tonight at 11:59 pm. **Consultations start this week** and run
  through week 7; if your group has not booked its
  [consultation](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile), do it now.
- **Databento API keys.** HW 3 uses the key you set up for HW 2, and asks you
  to store it as a GitHub Actions secret. Everything we pull is on the CME
  Globex feed (`GLBX.MDP3`) and free under the course license.

## Objectives

- Explain how fed funds futures prices imply the probability of a rate
  move, and complete the FedWatch half of HW 2.
- Publish a `docs/` folder as a live website with GitHub Pages:
  [Publishing to GitHub Pages](./Week3/github_pages_preview.md).
- Read and write a GitHub Actions workflow: triggers, jobs, steps, a
  schedule, a secret, and a deploy to Pages:
  [GitHub Actions and an Interactive Dashboard](./Week9/github_actions_interactive_dashboard.md)
  and [cron](./Week9/cron_jobs.md).
- Say what the homework autograder has been doing all quarter, and why a
  workflow you cannot read is a workflow you cannot trust.
- Run a project with `uv run`, and say what a lockfile records that a
  hand-written `requirements.txt` does not:
  [`uv` and `pixi`](./Week3/uv_and_pixi.md).
- Explain what a module and a package are, how `sys.path` decides what an
  `import` finds, and what `if __name__ == "__main__":` does in a module that
  is also a script:
  [Modules, Packages, and `if __name__ == "__main__"`](notebooks/_01_modules_and_packages_ipynb.ipynb).
- Explain the anatomy of a published Python package, `pyproject.toml`, a
  build backend, versioning, a test matrix in CI, and a release
  ([Writing and Publishing Your Own Python Packages](./Week6/python_packaging_with_hatch.md)).
- Know the options data on offer and their quirks: the OptionMetrics
  volatility surface via WRDS, and E-mini options on CME Globex via
  [Databento](./Week7/databento.md).
- Explain what SVIX measures and how option prices give the probability of
  a crash.

## Agenda Item 1: HW 2 Questions, then FedWatch

[HW 2](./HW2.md) is due in a week. Bring what is stuck.

Then the half of HW 2 that week 3 did not reach. Open the
[CME FedWatch tool](https://www.cmegroup.com/markets/interest-rates/cme-fedwatch-tool.html).
A 30-Day Fed Funds futures contract settles at 100 minus the month's average
fed funds rate, so its price is the market's forecast of that average; from
the contract for the month of the next FOMC meeting you back out the rate
the market expects after the meeting, and from that the probability of a
move.

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data_ipynb.ipynb):
  the contract, and the four parameters of the query you set in Part C.
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication_ipynb.ipynb):
  the three functions you complete, and the "does it match CME?" check.

## Agenda Item 2: Publishing with GitHub Pages

We built the tool. Nobody can see it. [Publishing to GitHub Pages](./Week3/github_pages_preview.md):
how a `docs/` folder of static HTML becomes a live website (Settings →
Pages → Deploy from a branch), and why your assignment repo must stay
private while the site is public. `chartbook build` produces `docs/`, Pages
serves it. HW 2 Part D is exactly this, done live, into a separate public
repo.

And the moment we push it, it is stale.

## Agenda Item 3: CI/CD with GitHub Actions

- **Start with the autograder.** Open the workflow that has been grading
  your homework since HW 1: `.github/workflows/tests.yml`. It checks out
  your code, builds the environment, runs `pytest`, and posts a score.
  Nothing magic, and now you can read it: `on`, `jobs`, `runs-on`,
  `steps`, and where the YAML lives.
- **A pipeline on a schedule.** The
  [`github_actions/` examples](https://github.com/finm-32800/inclass_examples/tree/main/github_actions)
  in the in-class repo: `01_fred_chartbook` runs a `doit` pipeline every
  morning on a [cron](./Week9/cron_jobs.md) schedule and publishes the
  ChartBook site to Pages from a single orphan commit on `gh-pages`;
  `02_databento_secret` adds the one thing most real pipelines need, an API
  key that works locally from `.env` and in CI from the secret store
  without ever being committed.
- **The FedWatch monitor.** The FedWatch case study deploys itself the same
  way every morning:
  [finm-32800.github.io/case_study_fedwatch](https://finm-32800.github.io/case_study_fedwatch/).
  Its workflow is the model for the one you write in HW 3.
- **`uv run doit` as the step.** The new homework is run with
  [`uv`](./Week3/uv_and_pixi.md): one `pyproject.toml`, one `uv.lock`, and
  `uv run doit` installs Python and the locked dependencies and runs the
  pipeline, on your laptop and on the runner alike. Ten minutes on what the
  lockfile records that `requirements.txt` does not, and where `pixi` fits.

## Agenda Item 4: Modules, Packages, and `if __name__ == "__main__"`

The workflow you just read runs `uv run doit monitor`, and that task runs
`python ./src/pull_es_options.py` as a script. The guide notebook imports the
same file and calls `load_es_options()`, and nothing is pulled. How can one
file be both a script and a library? And what turns a folder of such files
into a package that someone else can install?

[Modules, Packages, and `if __name__ == "__main__"`](notebooks/_01_modules_and_packages_ipynb.ipynb)
answers both with the small examples in
[`python_imports/`](https://github.com/finm-32800/inclass_examples/tree/main/python_imports)
in the in-class repo. We run them live:

- **A module is a file, and a package is a folder.** `01_simple_module` and
  `02_package_directory`: the same `import mypackage`, two layouts.
- **Where Python looks.** `03_name_shadowing`: `sys.path` puts the script's
  own folder first. That is why a module in `src/` can `import settings`, why
  `dodo.py` inserts `src/` into the path, and why a file of yours named
  `numpy.py` breaks NumPy.
- **An installable package.** `project_paths/install_approach`: a
  `pyproject.toml` and `pip install -e .`, and `import myproj` works from any
  folder. `finm` and `chartbook` have the same shape.
- **`if __name__ == "__main__"`.** `04_name_main`: run `python greet.py`, then
  `python main.py`, and the block runs the first time and not the second. In
  the course's `pull_` modules that block is the pull. `doit` runs the file,
  so the pull runs; a notebook imports it, so nothing touches the network.

## Agenda Item 5: Where Else CI/CD Lives: Python Packages

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

Reading, not covered tonight: [Sphinx](./Week5/sphinx.md), which builds the
documentation for pandas and for this book, and
[GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md), which
your final project requires and which we demo next week.

## Agenda Item 6: Options Data on Databento

- [Databento](./Week7/databento.md) for options on futures: the E-mini
  S&P 500 end-of-month options (root `EW`), the `definition` schema that
  says what each contract is, the `statistics` schema that carries the
  exchange's settlement price for every listed strike, and the user-defined
  spreads to drop.
- The options case study,
  [finm-32800/case_study_options](https://github.com/finm-32800/case_study_options),
  through [SPX Hedging](notebooks/_02_spx_hedging_ipynb.ipynb), five minutes
  for the vocabulary: strikes, expirations, implied volatility, the smile.
- [LSEG Datastream](./Week7/LSEG_datastream.md), as a reading, for the
  vendor landscape.

## Agenda Item 7: Launch HW 3, SVIX and the Probability of a Crash

Two live pages to open first:

- **[Ian Martin's data page](https://personal.lse.ac.uk/martiniw/).** SVIX,
  his option-implied volatility index, and the probability of a 20% drop in
  the S&P 500, published from 1996 to 2012.
- **[CME's CVOL indexes](https://www.cmegroup.com/market-data/cme-group-benchmark-administration/cme-group-volatility-indexes.html).**
  The same simple-variance construction, computed by the exchange every day
  from the settlement prices we pull.

[HW 3](./HW3.md) rebuilds both numbers from E-mini S&P 500 option
settlements on Databento, and then does what HW 2 did not: deploys the
replication as a daily monitor on GitHub Actions, with your Databento key as
a secret, publishing to GitHub Pages every morning.

- [E-mini S&P 500 Options Data from Databento](notebooks/_01_es_options_data_ipynb.ipynb)
- [Replicating SVIX and the Probability of a Crash](notebooks/_02_svix_and_crash_probability_ipynb.ipynb):
  the simple variance swap, the risk-neutral distribution by
  Breeden-Litzenberger, and Martin's crash probability.
- [The Daily Monitor](notebooks/_03_daily_monitor_ipynb.ipynb)

The offline half follows
[Martin and Shi (2025)](https://personal.lse.ac.uk/martiniw/oib_martin_shi_latest.pdf),
*Forecasting Crashes with a Smile*: bounds on the probability that a single
stock crashes, for Apple and AIG, from the OptionMetrics surface on WRDS.
Your blank there is the query.

- [Crash Bounds for Single Names](notebooks/_04_single_name_crash_bounds_ipynb.ipynb)

## Final Projects

Assignments go out Wednesday morning. Each group books one
[consultation](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile) in weeks 4 through 7.
The rubric requires every pull request to be reviewed by a teammate; the
pull-request workflow is demoed next week.

## Looking ahead to Week 5

Week 5 is **unit tests and data validation**: how a pipeline checks its own
data before it publishes anything. The opening example is the cleaning of
FINRA TRACE, where every filter is a convention, and [HW 4](./HW4.md) then
moves to a case with a ground truth: rebuilding the E-mini order book from
the raw message feed and validating it against the vendor's own book.
