# Week 3: Reports --- Notebooks, ChartBook, and LaTeX

```{toctree}
:maxdepth: 1
Week4/reports_with_jupyter_notebooks.md
Week4/intro_to_LaTeX.md
Week4/latex_essentials.md
Week3/project_structure.md
Week3/study_guide.md
```

A pipeline's output has to land somewhere people can see it. Last week `doit`
learned to run a pipeline's steps in order. Tonight the last steps are the
reports: the pipeline executes its own notebooks, ChartBook turns them into a
website, and LaTeX makes the PDF. The habit to build
is treating a report not as a document you write once but as a **build target**
the pipeline regenerates whenever the data changes.

## Announcements

- **From the Career Development Office.** Two events this week; scan a code
  to register. Peers & Pizza is Thursday, October 15, 12:00 to 1:30 pm. The
  Technical Labs run Tuesday, October 13, and Wednesday, October 14, 6:00 to
  7:00 pm; register for one of the two, not both.
  <!-- Shown at the start of class at the CDO's request. The slide itself is
  _announcements/Upcoming CDO Events.pptx in the textbook repo. -->

  ![Upcoming CDO events: Peers & Pizza, October 15, 12:00 to 1:30 pm;
  Technical Labs, October 13 or 14, 6:00 to 7:00 pm](./Week3/assets/upcoming_cdo_events.png)

- **[HW 1](./HW1.md) is due next Tuesday, October 20,** at the start of class.
  This is the week to finish it: ask your questions tonight, because from here
  on the material is HW 2.
- **[HW 2](./HW2.md) launches today,** due Tuesday, October 27, two weeks from
  tonight: the Treasury yield curve and the policy path, published as a
  ChartBook site on GitHub Pages and summarized in a PDF market brief. Your
  private copy is created after class tonight.
- **Databento API key.** HW 2 pulls futures data from Databento, and you
  should already have a key. If you do not, email the program administrator,
  Hope Kennedy, at hkennedy1@uchicago.edu.
- **Project survey, due next Tuesday.** The preference survey is open, and the
  link is in the Canvas announcement. It closes Tuesday, October 20 at
  11:59 pm. Each group is emailed its assigned paper from the
  [Potential Final Projects](./FinalProject/potential_final_projects.md) list
  on Wednesday morning, October 21.
- **Book your project consultation.** Each group meets with me once, in weeks 4
  through 7, for feedback on its plan:
  [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile).

## Objectives

- Use a notebook for what it is good at, reporting, and keep pipeline logic out
  of it: [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md).
  Execute a notebook from the command line with `nbconvert` and wire it into
  `doit`.
- Register a project's notebooks and dataframes in a `chartbook.toml` and build
  a browsable site from it, as one more `doit` target.
- Write and compile LaTeX, and automate the inclusion of a figure or table so a
  PDF rebuilds itself from the pipeline's outputs:
  [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md).
- Explain how the Nelson-Siegel-Svensson model fits a yield curve to Treasury
  quotes, and how fed funds futures prices imply an expected policy path.
- Pull market data from a vendor's API with a key kept in `.env`, and check
  what a query costs before running it: [Databento](./Week7/databento.md).
  Taught in [week 4](./overview_w4.md).
- Publish a `docs/` folder as a live website, which HW 2 asks you to do:
  [Publishing to GitHub Pages](./Week3/github_pages_preview.md). Taught in
  [week 4](./overview_w4.md).

## Agenda Item 1: HW 1 Questions

[HW 1](./HW1.md) is due in a week. Bring what is stuck. If your WRDS pull or
your first push is not working yet, we fix that first.

## Agenda Item 2: Notebooks Are for Reporting

Last week ended on a point worth repeating. I do not recommend doing your
analysis in a notebook. The work belongs in `.py` files that `doit` runs as
tasks: the pulls, the cleaning, the calculations. A notebook is for reporting.
It loads what the tasks produced and presents it, to a coauthor, to a reader,
and most often to yourself, with little or no computation of its own.

We pick up where week 2 stopped, in the `pydoit/` directory of the
[in-class examples repo](https://github.com/finm-32800/inclass_examples).
Example 02 is the same dependency graph on a real project, with a `src/`
folder and a `settings.py`. Examples 03 and 04 add notebooks.

[Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md):
why notebooks are for *reporting* and not for pipeline steps, and how
`nbconvert` executes one from the command line and exports it to HTML. A
notebook is then just another `doit` task, with file dependencies and targets.
*→ HW 1:* `task_run_notebooks` in your `dodo.py` does exactly this to the six
guide notebooks.

How the notebooks here plug into `dodo.py`. I do not keep a notebook as an
`.ipynb` file. The source of truth is a plain `.py` file in Jupytext's percent
format, which Git can diff, named so you can tell it is a notebook:
`01_markowitz.ipynb.py` in HW 0, `02_replicate_GSW2005_ipynb.py` in HW 1 and
HW 2. VS Code treats each `# %%` line in the file as a cell and runs it in the
Interactive Window, so you still work interactively. The `doit` task does the
rest:

1. `jupytext` converts the `.py` file to `.ipynb`.
2. `nbconvert` executes the notebook and exports it to HTML.
3. The `.ipynb` and the HTML land in `_output/`. They are build artifacts,
   regenerated from the source and never edited by hand.

The task's file dependencies are the modules the notebook imports and the
files it loads. Change one of those and `doit` runs the notebook again;
otherwise it leaves the rendered copy alone.
*→ HW 2:* you add two notebooks to `notebook_tasks` this way.

## Agenda Item 3: ChartBook, the Site as a Build Target

Say you have several notebooks on Treasury yields, with charts, tables, and
commentary, and you want colleagues to browse them as one website. On your own
you would set up notebook conversion, a site generator, navigation, and
publishing. [ChartBook](https://backofficedev.github.io/chartbook/)
([PyPI](https://pypi.org/project/chartbook/)) is a small Python package I
wrote to take that setup off your hands: it catalogs a project's notebooks,
dataframes, and charts and generates a browsable static site from them.

The workflow, start to finish:

1. Start a project with `chartbook init`.
2. Write the computation in Python modules.
3. Write the reports as Jupytext notebooks.
4. Register the outputs worth showing in `chartbook.toml`.
5. Generate the static site with `chartbook build`.
6. Publish the site with GitHub Pages.

`doit` and ChartBook do different jobs. `doit` runs the pipeline and tracks
its dependencies. ChartBook does not run your computation: it organizes and
publishes what the pipeline produced.

`chartbook init` builds the new project from a template.
[Cookiecutter](https://cookiecutter.readthedocs.io/) generates a project from
a template, as with the Cookiecutter Data Science template you have seen, and
[Cruft](https://cruft.github.io/cruft/) lets a project pull in later updates
to the template it came from.
[The ChartBook project template](./Week3/project_structure.md) has the
details, and we use it in week 4.

ChartBook also shows where next week is going. It is an ordinary Python
package, installed with `pip`, and you can write one like it: next week we
turn your own code into a reusable, installable package.

- **The manifest, `chartbook.toml`.** Each `[notebooks.*]`, `[dataframes.*]`,
  and `[charts.*]` entry says what an output is, where it comes from, and where
  it lives. Read HW 1's as the example.
- **The CLI and the Python API.** `chartbook build` generates the site;
  `chartbook ls` lists what is registered; `chartbook data get-path` returns a
  parquet path; and `data.load(pipeline=..., dataframe=...)` loads it straight
  into pandas or polars.
- **Where it sits in the pipeline.** `chartbook build` is just another `doit`
  task (`task_generate_pipeline_site` in HW 1), so the site is regenerated
  whenever the data changes, not assembled by hand. Publishing it is the last
  item tonight; in week 4, many projects' manifests aggregate into one catalog.

## Agenda Item 4: LaTeX for PDF Reports

ChartBook is the HTML half of report generation; LaTeX is the PDF half.

**LaTeX is code, so a PDF report can be one more target of the pipeline.**
Python computes the results and writes the figures and tables to files. The
`.tex` file pulls them in with `\includegraphics` and `\input`. `latexmk`
compiles the PDF. `doit` tracks the dependencies, so the report rebuilds when
its inputs change.

- [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md): what a `.tex` file is, how
  it compiles, and the handful of commands a report needs.
- Work through the
  [`latex/` examples](https://github.com/finm-32800/inclass_examples/tree/main/latex)
  in the in-class repo, a progression from a five-line document through figures
  and tables, bibliographies, and Beamer slides.
- **Demo:** the market brief in
  [`latex/08_handout`](https://github.com/finm-32800/inclass_examples/tree/main/latex/08_handout),
  built from a custom class file, whose charts and returns table are *generated
  by a Python script* and pulled in with `\includegraphics` and `\input`. The PDF
  rebuilds itself whenever the data changes. This is the same pattern the class
  report uses in week 6.
  *→ HW 2:* the same pattern on the yield curve. `task_generate_market_brief`
  writes the charts and tables, and `task_compile_market_brief` runs `latexmk`
  on `reports/market_brief.tex`. You write one of those tables and the task
  that compiles the PDF.
- **Which format when.** Three formats cover what this course writes:
  - *Markdown* for documentation, READMEs, and plain technical writing. You
    have been writing it all along: the text cells of a notebook, the pages of
    this book.
  - *Notebooks, kept as Jupytext `.py` files,* for interactive research and
    for reports made of charts, tables, and commentary, published as HTML.
  - *LaTeX* for academic papers, professional PDF reports, heavy math, and
    anything that needs precise control of the page.

## Agenda Item 5: Launch HW 2, the Yield Curve and the Policy Path

Two live tools to open first:

- **[The Fed's nominal yield curve](https://www.federalreserve.gov/data/nominal-yield-curve.htm).**
  You replicate it from CRSP Treasury data with the Nelson-Siegel-Svensson
  specification.
- **[CME FedWatch](https://www.cmegroup.com/markets/interest-rates/cme-fedwatch-tool.html).**
  You rebuild its market-implied probabilities of the Fed's policy decisions
  from fed funds futures.

These are real financial tools. The assignment is to reproduce the
calculations behind them with your own reproducible data pipeline.

[HW 2](./HW2.md) is a multi-source pipeline that ends in a published website and
a PDF.
You estimate the U.S. Treasury yield curve with the Nelson-Siegel-Svensson
model, following
[Gürkaynak, Sack, and Wright (2006)](https://www.federalreserve.gov/pubs/feds/2006/200628/200628abs.html),
the methodology behind the Fed's daily published curve:

- [CRSP Treasury Overview](notebooks/_01_CRSP_treasury_overview_ipynb.ipynb)
- [Replicating GSW (2006)](notebooks/_02_replicate_GSW2005_ipynb.ipynb)

Then you read the expected path of the policy rate out of 30-Day Fed Funds
futures, the way the CME FedWatch tool does:

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data_ipynb.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication_ipynb.ipynb)

### Databento, in Five Minutes

*Taught in [week 4](./overview_w4.md).*

- **The chapter:** [Databento](./Week7/databento.md)
- **The examples we run:**
  [`databento/` in the in-class repo](https://github.com/finm-32800/inclass_examples/tree/main/databento)
- **The same material as a notebook:**
  [Pulling Market Data From Databento](notebooks/_01_databento_ipynb.ipynb)

The futures come from Databento, a market data vendor whose CME Globex feed
the course subscribes to. Our access is to historical data, not the live
stream. It is the pattern you used for WRDS in week 1, with a different kind
of credential: an API key in `.env`, a `pull_` function that calls the vendor,
and a `load_` function that reads what the pull cached. The examples take it
in small steps, through the Python client, the raw HTTP API, and a free call
that says what a query costs before you run it.

What to take from the demo:

- What Databento provides, and how to pull historical market data with its
  Python SDK.
- How to keep the API key out of the code, in an environment variable read
  from `.env`.
- How a request is specified: a dataset, a schema, and the symbology that
  names the instrument.
- How to check what a query costs before you download anything.

### Checks and Deliverables

Both replications are checked against the published numbers. The tests
compare your fitted yields with the Fed's at every maturity from 1 to 30
years, and your FedWatch probability with a recorded reading of the CME tool,
and each guide shows how closely they agree.

The pipeline pulls from four sources (CRSP via WRDS, the Fed's
published GSW series, ZQ futures from Databento, and FRED), and the
deliverables are a ChartBook site on GitHub Pages and a two-page market brief
on the Treasury curve, compiled from LaTeX. We launch it in class; you finish the
`TODO`s and publish on your own.

## Agenda Item 6, If Time: Publishing to GitHub Pages

*Taught in [week 4](./overview_w4.md).*


[Publishing to GitHub Pages](./Week3/github_pages_preview.md): how a `docs/`
folder of static HTML becomes a live website (Settings → Pages → Deploy from a
branch → `main` / `/docs`), and why your assignment repo must stay private while
the site is public. The two steps compose: `chartbook build` produces `docs/`,
Pages serves it. HW 2 Part D is exactly this, into a separate public repo.

## Final Project: The Two Steps

The full procedure and rubric are in the
[Final Project Instructions and Rubric](./FinalProject/final_project_rubric.md);
we walk it in class. Two steps:

1. **Consultation.** One-on-one with me,
   [booked via Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile), in weeks 4
   through 7. Come ready to say what the paper is about, your data sources, and
   the product you will build: the new table or figure, and who would use it.
   The consultation is ungraded.
2. **Final project presentation**, week 10. Your project is due 24 hours before
   a 30-minute meeting with me. There are no slides and no demo, because I will
   already have run your code. I question each member individually, and those
   answers are graded.

## Looking ahead to Week 4

Week 4 picks up what moved out of tonight, FedWatch and publishing with
GitHub Pages, and then makes the site rebuild itself: **CI/CD with GitHub
Actions**, with a word on how Python packages are built and released.
[HW 3](./HW3.md) launches, a daily monitor of the S&P 500's option-implied
crash probability, deployed with a workflow you write.
