# Week 4: Python Packaging and Documentation with Sphinx

```{toctree}
:maxdepth: 1
Week6/python_packaging_with_hatch.md
Week5/sphinx.md
Week6/chartbook_catalog.md
Week7/databento.md
notebooks/_01_databento_ipynb.ipynb
Week7/LSEG_datastream.md
notebooks/_02_spx_hedging_ipynb.ipynb
```

So far your code has lived in repositories that only you run. A **package** is
code organized so that a teammate can `pip install` it and build on it, and
**documentation** is what makes that possible. This is the week that turns your
coursework into something you can hand a hiring manager, and the paper is
Martin and Shi (2025): the deliverable in [HW 3](./HW3.md) is an installable,
tested package that recovers the market's own probability distribution from
option prices.

## Announcements

- **[HW 1](./HW1.md) is due tonight,** at the start of class.
- **[HW 2](./HW2.md) is due next Tuesday, October 27.** Questions at the start
  of class.
- **[HW 3](./HW3.md) launches today,** due Tuesday, November 3, in week 6:
  option-implied crash probabilities.
- **Project consultations start this week** and run through week 6. If your group
  has not booked its [consultation](https://finm-32800.youcanbook.me/), do it
  now.
- **Databento API keys.** You need one for HW 3 and again for HW 4. Everything
  we use is on the CME Globex feed (`GLBX.MDP3`), which is free under the course
  license; the equity-options feed is metered, so do not point a pull at it.

## Objectives

- Explain the anatomy of a published Python package: `pyproject.toml`, a build
  backend, versioning, dependencies, and CLI entry points
  ([Writing and Publishing Your Own Python Packages](./Week6/python_packaging_with_hatch.md)).
- Build and publish a package with `hatch`, and know what TestPyPI is for.
- Explain what semantic versioning promises, and what a breaking change obliges
  you to do.
- Generate documentation from the code itself with [Sphinx](./Week5/sphinx.md),
  and say what role [docstrings](https://www.geeksforgeeks.org/python-docstrings/)
  play in it.
- Contribute to a package you do not own: fork, branch, PR, review, merge.
- Consume data another repository produces through a ChartBook catalog
  ([The ChartBook Catalog](./Week6/chartbook_catalog.md)).
- Know the options data on offer and their quirks: the OptionMetrics volatility
  surface via WRDS, and CME Globex via [Databento](./Week7/databento.md).

## Agenda Item 1: Anatomy of a Package

Work through
[Writing and Publishing Your Own Python Packages](./Week6/python_packaging_with_hatch.md):
`pyproject.toml` as the single source of truth, the Hatchling build backend,
dynamic versioning, and the one-line `[project.scripts]` entry that creates a
CLI command. Then the packages you can practice on:

- [`finm`](https://pypi.org/project/finm/) ([docs](https://jeremybejarano.com/finm/))
  is the course's own published package: `finm.fixedincome` (the yield-curve
  functions you imported in HW 2), `finm.data` (Fama-French, Federal Reserve,
  He-Kelly-Manela and other loaders), and `finm.analytics`. It is the low-stakes
  place to practice the contribution loop on a package you do not own, crossing
  a repository boundary you do not control.
- [`stockbeta`](https://github.com/finm-32800/stockbeta) is a practice package
  published to [TestPyPI](https://test.pypi.org/project/stockbeta/) and not to
  the real PyPI. TestPyPI is the rehearsal space: the full publish workflow with
  none of the consequences of claiming a real name.
- [`chartbook`](https://pypi.org/project/chartbook/) is the package you have been
  installing all quarter. Reading its [changelog](https://backofficedev.github.io/chartbook/changelog.html)
  is a live case study in how a release is actually cut: changelog entry, version
  bump, `hatch build` producing the wheel and sdist, publish. Semantic versioning
  as a contract, and why a breaking change in a `0.x` package bumps the minor
  version.

## Agenda Item 2: Documentation with Sphinx

[Sphinx](./Week5/sphinx.md) is the tool that builds the documentation for pandas,
and for this textbook. It generates browsable reference pages from your code, so
the docs sit next to the code and do not fall behind it.

- Docstrings as the source: what `autodoc` reads and what conventions make it
  readable.
- Forking a project and standing up its docs, featuring
  <https://github.com/jmbejara/quantstats_lumi>.
- Your final project is the natural first candidate for a documented, installable
  package.

## Agenda Item 3: The ChartBook Catalog

The question a package forces on you is the same one the class report will force
in week 6: **what should your code do when it needs data another repository
already produces?** Copying pull code duplicates work and drifts.
[The ChartBook Catalog](./Week6/chartbook_catalog.md) is the answer.

- Pipelines vs. catalogs: a catalog is a `chartbook.toml` with a `[pipelines]`
  registry. The [FTSFR organization](https://github.com/orgs/ftsfr/repositories)
  is the production-scale example: about twenty pipeline repos plus one catalog
  repo.
- **Hands-on:** clone a couple of FTSFR pipeline repos, register them in your
  global catalog (`chartbook catalog add`, or `members` auto-discovery), build
  and browse the combined site, and load another repo's dataframe by name with
  `data.load(pipeline=..., dataframe=...)`.
- The chapter lists which FTSFR repos run on free public data or WRDS alone. The
  Bloomberg-gated ones are off the menu.

## Agenda Item 4: Options Data and the Options Case Study

Two data vendors, then the options material the homework builds on.

- [Databento](./Week7/databento.md) and the hands-on
  [Pulling Market Data From Databento](notebooks/_01_databento_ipynb.ipynb):
  schemas, symbology, and cost control. CME Globex is free under our license,
  and it is where the E-mini S&P 500 options in HW 3 come from.
- [LSEG Datastream](./Week7/LSEG_datastream.md), briefly, for the vendor
  landscape.
- The options case study,
  [finm-32800/case_study_options](https://github.com/finm-32800/case_study_options),
  reviewed through [SPX Hedging](notebooks/_02_spx_hedging_ipynb.ipynb), which
  harmonizes the WRDS OptionMetrics panel with the Databento E-mini chain. This
  is class material, not the assignment; it establishes the vocabulary the
  assignment assumes.

## Agenda Item 5: Launch HW 3, Option-Implied Crash Probabilities

[HW 3](./HW3.md) follows
[Martin and Shi (2025)](https://personal.lse.ac.uk/martiniw/oib_martin_shi_latest.pdf),
*Forecasting Crashes with a Smile*. You recover the risk-neutral distribution of
returns from the volatility smile by the Breeden-Litzenberger method, turn it
into an option-implied crash probability, and apply the paper's corrections to
get a bound that forecasts out of sample.

The arc is the lesson. Build the density from the OptionMetrics volatility
surface and get a number. Discover that the surface's observed strikes stop well
short of the crash thresholds, so at short horizons the number rests on an
extrapolation convention. Rebuild the density from raw quotes on E-mini S&P 500
options from Databento, where every listed strike is quoted, and meet the
convexity violations the vendor's smoothed surface hid from you. Two data
sources, one seam between them, and the seam is the point.

The code you complete is a small, pip-installable package with tests, which is
why the assignment sits in this week. Smile fitting and density extraction is
daily work on an options market-making desk.

## Looking ahead to Week 5

Week 5 is **unit tests and data validation**: how a pipeline checks its own data
before it publishes anything. The opening example is the cleaning of FINRA TRACE,
where every filter is a convention, and [HW 4](./HW4.md) then moves to a case
with a ground truth: rebuilding the E-mini order book from the raw message feed
and validating it against the vendor's own book.
