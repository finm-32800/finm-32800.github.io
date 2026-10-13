# Homework 2: The Yield Curve and the Policy Path

```{toctree}
:maxdepth: 1
HW2_guide_A_github_skills.md
notebooks/_01_CRSP_treasury_overview_ipynb.ipynb
notebooks/_02_replicate_GSW2005_ipynb.ipynb
notebooks/_01_fed_funds_futures_data_ipynb.ipynb
notebooks/_02_fedwatch_replication_ipynb.ipynb
```

- **Launched:** week 3. **Due:** Tuesday, October 27, at the start of the
  week 5 session, two weeks after it launches. Also posted on Canvas.
- **Link to Assignment:** a private copy is created for you in the
  [hw-finm-32800](https://github.com/hw-finm-32800) organization, named
  `hw2-` followed by your GitHub username. If you have accepted the
  invitation to the organization, your copy simply appears there. If you have
  not, see [HW 1](./HW1.md) for how the invitations work.
- **Goes with:** weeks 3 and 4

## Overview

Two replications and three reports. The first replication estimates the U.S.
Treasury yield curve from CRSP quotes, following Gürkaynak, Sack, and Wright
(2006), and checks it against the curve the Federal Reserve publishes every
day. The second reads the expected path of the policy rate out of 30-Day Fed
Funds futures, the way the CME FedWatch tool does. The pipeline then reports
what it found three ways: as notebooks that `doit` executes, as a website
built by ChartBook, and as a two-page PDF market brief compiled from LaTeX.

The first two are the finance. The third is the subject of week 3: a report
is not a document you write once but a build target that the pipeline
regenerates whenever the data changes.

As in HW 1, everything runs as a pipeline, and the unit tests are the
specification. Run `doit`. It pulls the CRSP Treasury files and then stops at
the first step that depends on a blank, which is expected. Run `pytest`, read
the first failing test, and write the code that passes it. Each blank you fill
lets `doit` get one step further. You are done when `doit` finishes without an
error and a second `doit` reports that every task is up to date. Do not edit
the test files.

The assignment has four parts, A to D, worth 19 points in total, one per
test. Every push runs the suite on the course runner and posts your score to
the repository's Actions tab. Search the repository for `TODO` to find the
blanks.

## Before you start

- **A Databento API key.** The futures data in Part C comes from Databento's
  CME Globex feed, and the pull needs a key in your `.env`. See
  [Databento](./Week7/databento.md). The
  [`databento/` examples](https://github.com/finm-32800/inclass_examples/tree/main/databento)
  in the in-class examples repository show the same kind of pull in small
  steps: the Python client, the raw HTTP API, and how to check what a query
  costs before you run it. The data this assignment pulls is free under the
  course's subscription, and the pull script checks the cost before it
  downloads anything.
- **A LaTeX installation.** Part D compiles a PDF with `latexmk`, which comes
  with [TeX Live](https://tug.org/texlive/). This is the large download that
  [Getting Set Up](./Week0/getting_set_up.md) asked you to start in week 0.
  If LaTeX is new to you, work through the
  [`latex/` examples](https://github.com/finm-32800/inclass_examples/tree/main/latex)
  in the in-class examples repository. They go from a five-line document to
  `08_handout`, a brief built the same way as the one in Part D.
- **The `finm` environment.** `pip install -r requirements.txt` adds the
  Databento client, Plotly, and IPython to the environment you built for
  HW 0 and HW 1.

## Part A (graded, 3 pts): GitHub Skills

Complete three more tutorials from the
[GitHub Skills](https://skills.github.com/) page, using **public
repositories** in your own GitHub account, and record the links in
`src/github_skills.py`.

- [HW 2 Guide A: GitHub Skills](./HW2_guide_A_github_skills.md)

## Part B (graded, 4 pts): the Treasury yield curve

The Federal Reserve publishes a Treasury yield curve every day. It is a
Nelson-Siegel-Svensson curve fit to the prices of Treasury notes and bonds,
with the method of
[Gürkaynak, Sack, and Wright (2006)](https://www.federalreserve.gov/pubs/feds/2006/200628/200628abs.html).
You will fit the same curve to CRSP Treasury quotes and compare yours with
theirs.

- [CRSP Treasury Data Overview](notebooks/_01_CRSP_treasury_overview_ipynb.ipynb)
- [Replicating the Gürkaynak, Sack, and Wright (2006) Treasury Yield Curve](notebooks/_02_replicate_GSW2005_ipynb.ipynb)

The blank:

- **The curve** (`src/gsw2006_yield_curve.py`). Complete `spot`, the
  Nelson-Siegel-Svensson formula for the zero-coupon yield at each maturity,
  and `discount`, the discount factor it implies. Each docstring states the
  formula and the second guide explains it. The rest of the file, including
  the fit and the security filter, is complete and calls these two.

`doit` then downloads the Fed's published curve and fits yours on three
validation dates and on the most recent date. Both tasks are written for
you. Read them in `dodo.py`: you will write two like them in Part D.

**Does the replication match?** Two of the tests fit your curve to the CRSP
quotes on four dates and check it against the Fed. One compares the prices
your fit implies, bond by bond, with actual prices and with the prices the
Fed's parameters imply. The other compares your fitted yields with the yields
the Fed publishes at every maturity from 1 to 30 years and requires them to be
within 25 basis points. The comparison is in prices and yields, not in the six
parameters: two quite different sets of parameters can describe nearly the
same curve. The second guide shows the comparison as a table and a chart.

## Part C (graded, 5 pts): the policy path

A 30-Day Fed Funds futures contract settles at 100 minus the average
effective fed funds rate over its month, so its price is the market's forecast
of that average. From the contract for the month of the next FOMC meeting you
can back out the rate the market expects after the meeting, and from that the
probability of a hike or a cut. This is what the
[CME FedWatch tool](https://www.cmegroup.com/markets/interest-rates/cme-fedwatch-tool.html)
reports.

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data_ipynb.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication_ipynb.ipynb)

- **The pull** (`src/pull_fed_funds_futures.py`). Set the four parameters of
  the Databento query: the dataset, the schema, the symbol, and the kind of
  symbol it is. The first guide explains each one, and the
  [`databento/` examples](https://github.com/finm-32800/inclass_examples/tree/main/databento)
  show more. The cost check and the rest of the pull are written.
- **The math** (`src/fedwatch.py`). Complete `implied_rate`,
  `solve_post_meeting_rate`, and `move_probability`. The second guide derives
  the math, and your job is to turn it into code.

These tests run offline, on hand-computed numbers, so you can do this part
before the futures data is pulled.

**Does the replication match?** One more test runs your three functions on
the inputs for a day on which we recorded what the CME tool displayed, and
requires your probability to land within two price ticks of CME's. The second
guide shows the comparison and explains why the two are close and not
identical: CME reads live prices and we read the daily close, and CME takes
the pre-meeting rate from a futures contract where we take it from the
published EFFR.

## Part D (graded, 7 pts): reports

Each piece below is a build target: `doit` regenerates it whenever the code or
the data behind it changes.

- **Notebooks as tasks** (`dodo.py`, 1 pt). The notebooks only present
  results, and `doit` executes them. Add the two FedWatch notebooks to
  `notebook_tasks`, each with the modules it imports and the data files it
  loads as its file dependencies. See
  [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md)
  and [PyDoit Examples](./Week3/doit_examples.md).
- **The ChartBook manifest** (`chartbook.toml`, 1 pt).
  [ChartBook](https://pypi.org/project/chartbook/) builds a website from the
  executed notebooks and documents the dataframes the pipeline pulls. Register
  the two FedWatch notebooks and the fed funds futures dataframe.
- **The LaTeX market brief** (3 pts). `reports/market_brief.tex` is a two-page
  brief on the Treasury market. Its charts and tables are written by a Python
  script and pulled into the document with `\includegraphics` and `\input`,
  so the PDF rebuilds itself from the data. You complete the function that
  writes one of those tables as a `.tex` file, the line of the document that
  pulls it in, and `task_compile_market_brief`, which compiles the PDF again
  whenever anything it pulls in changes. See
  [Introduction to LaTeX](./Week4/intro_to_LaTeX.md),
  [LaTeX Essentials](./Week4/latex_essentials.md), and the
  [`latex/` examples](https://github.com/finm-32800/inclass_examples/tree/main/latex).
  The tests read your code and your `.tex` file, so your points do not depend
  on your LaTeX installation, but compile the brief and read it.
- **The published site** (`src/published_site.py`, 2 pts). Publish the
  ChartBook site with GitHub Pages, from a **separate public repository**. Your
  assignment repository stays private. The steps are in the assignment's
  README, and [GitHub Pages and Tearsheets](./Week3/github_pages_preview.md)
  explains what Pages is. Do the GitHub Pages tutorial in Part A first.
