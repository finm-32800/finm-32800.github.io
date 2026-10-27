# Week 5: Live Monitors

```{toctree}
:maxdepth: 1
notebooks/_01_modules_and_packages_ipynb.ipynb
Week3/uv_and_pixi.md
Week5/live_monitor.md
Week5/study_guide.md
```

Last week ended before three things it promised: how Python finds and runs
your modules, a pipeline that runs on its own, and the math of HW 3. This
week does all three. We deploy a live monitor
together, each in your own GitHub account: a job that wakes up every day,
pulls fresh data with a secret API key, remembers what it computed
yesterday, checks itself, and republishes a web page. It is the same
machinery as HW 3's SVIX monitor, on a pipeline simple enough that nothing
gets in the way, and like HW 3 it runs with `uv`, which we finally take up
properly. Then we walk through HW 3's guides, one interactive figure at a
time.

## Announcements

- **[HW 2](./HW2.md) is due today at 9:30 am,** the start of class.
- **[HW 3](./HW3.md) launches today,** due Tuesday, November 10, at the
  start of the week 7 session. Your private copy is created this week.
- **Bring a FRED API key to class.** It is free and takes about two minutes:
  make an account at
  [fredaccount.stlouisfed.org](https://fredaccount.stlouisfed.org/apikeys)
  and request a key. The monitor we deploy together needs one.
- **[HW 4](./HW4.md) and [HW 5](./HW5.md):** dates to be announced on
  Canvas.
- **Project consultations continue** through week 7. Every group needs one.

## Objectives

- Explain what a module and a package are, how `sys.path` decides what an
  `import` finds, and what `if __name__ == "__main__":` does in a module
  that is also a script:
  [Modules, Packages, and `if __name__ == "__main__"`](notebooks/_01_modules_and_packages_ipynb.ipynb).
- Run a project with `uv run`, say what a lockfile records that a
  hand-written `requirements.txt` does not, and why that matters on a machine
  that starts empty: [uv and pixi](./Week3/uv_and_pixi.md).
- Deploy a live monitor that pulls data with a secret key on a schedule,
  keeps its state on a branch of its own, and publishes to GitHub Pages:
  [Building a Live Monitor with GitHub Actions](./Week5/live_monitor.md).
- Explain where a pipeline's state can live between runs, and when a
  pipeline needs state at all.
- Read a row of futures-option settlement data, and say why a crash
  probability needs the deep strikes the exchange settles:
  [E-mini S&P 500 Options Data from Databento](notebooks/_01_es_options_data_ipynb.ipynb).
- Read option prices as a probability distribution: Breeden and
  Litzenberger, SVIX against the VIX, and the probability of a crash under
  three sets of beliefs:
  [Replicating SVIX and the Probability of a Crash](notebooks/_02_svix_and_crash_probability_ipynb.ipynb).

## Agenda Item 1: Catching Up --- Modules, Packages, and `if __name__ == "__main__"`

Last week did not reach this. HW 3's daily workflow runs `uv run doit latest`,
and that task runs `python ./src/pull_es_options.py --latest` as a script.
The guide notebooks import the same file and call `load_es_options()`, and
nothing is pulled. How can one file be both a script and a library? And what
turns a folder of such files into a package that someone else can install?

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

## Agenda Item 2: uv, and Why the Homework Uses It

[Modern Environment Tools: uv and pixi](./Week3/uv_and_pixi.md). Every
repository this week, HW 3 and the monitor alike, runs with `uv run doit`.

- **One command on an empty machine.** What `uv run` does the first time:
  Python, `.venv/`, the locked packages, then the command.
- **The lockfile.** Open the monitor's `uv.lock` next to its
  `pyproject.toml`: what it pins that a hand-written `requirements.txt`
  leaves floating.
- **Why the runner cares.** It starts empty every morning, so the whole
  environment is two steps of YAML, rebuilt in seconds.
- **The way back.** `uv export` writes the `requirements.txt` the `finm`
  conda environment reads, and `pixi` brings the same idea to the conda
  world.

## Agenda Item 3: Lab --- Deploy a Live Monitor

We deploy the
[Yield Spread Monitor](https://github.com/finm-32800/yield_spread_monitor)
together, a copy in each of your accounts, following
[Building a Live Monitor with GitHub Actions](./Week5/live_monitor.md). It
publishes the 10-year minus 2-year Treasury spread every day; the finance
is one subtraction, so all the attention goes to the machinery. While the
runs go:

- **A day in the life.** Six steps on a runner that starts empty: check
  out, restore the history, test, update, record, publish.
- **Where the state lives.** The runner forgets everything overnight, so the
  history lives on a branch of its own, one small commit a day. Why not on
  `main`, on `gh-pages`, or in the Actions cache.
- **Does a monitor need state at all?** FRED can rebuild this one's history
  in a second; Databento cannot rebuild HW 3's in less than hours. Keep
  state when recomputing is expensive or impossible.
- **`doit` and `doit latest`.** The backfill and the daily update.
- **Secrets.** Where the key lives, and what a run without it looks like.
- **Copyright, terms of use, and licenses.** Why the monitor stores the
  spread it computes and never the yields it pulls.

Last week you kept a `gh-pages` branch by hand: rebase onto `main`,
rebuild, amend, force-push. Today the workflow does it, and more simply:
each run publishes one fresh orphan commit, so there is no history to
rebase at all.

By the end your monitor is live at `https://<your-username>.github.io/<repository>/`,
and tomorrow its history has one more line. HW 3's Part C is the same steps
on a harder pipeline.

## Agenda Item 4: HW 3 --- the Guides, Interactive

[HW 3](./HW3.md) launches today: SVIX and the probability of a crash, from
the settlement prices of E-mini S&P 500 options. We walk through its
guides, one interactive figure at a time:

- **The data**
  ([guide 01](notebooks/_01_es_options_data_ipynb.ipynb)): how to read a
  futures option's symbol and its settlement, the smirk, the volatility
  surface, and how far the exchange's strikes reach.
- **Option prices are a distribution**
  ([guide 02](notebooks/_02_svix_and_crash_probability_ipynb.ipynb)): the
  slope of the call curve is the risk-neutral CDF; SVIX against the VIX;
  three beliefs about a crash, and what risk aversion does to them.
- **If time allows: thirty years, and single names**
  ([guide 04](notebooks/_04_single_name_crash_bounds_ipynb.ipynb),
  optional reading): the history of crash fear since 1996, and the bounds
  for Apple and AIG.

[The Daily Monitor](notebooks/_03_daily_monitor_ipynb.ipynb), guide 03,
walks through Part C: the machinery of today's lab, on HW 3's pipeline.

A suggested order: Part D's query first, because the last test of Part B
needs the OptionMetrics pull it makes; then Part B; then Part C, which is
today's lab again.

## Looking ahead to Week 6

Next week picks up the topics this week made room for: unit tests and data
checks, using the monitor you deployed today; cleaning FINRA TRACE, where there is no ground truth and every filter is a convention you
must defend, and rebuilding a limit order book, where there is a ground
truth and validation becomes exact.
