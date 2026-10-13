# Week 3: Reproducible Reports --- ChartBook, GitHub Pages, and LaTeX

```{toctree}
:maxdepth: 1
Week4/reports_with_jupyter_notebooks.md
Week7/databento.md
notebooks/_01_databento_ipynb.ipynb
Week3/github_pages_preview.md
Week3/project_structure.md
Week3/uv_and_pixi.md
Week4/intro_to_LaTeX.md
Week4/latex_essentials.md
Week6/GitHub_pull_requests.md
Week3/study_guide.md
```

A pipeline's output has to land somewhere people can see it. Last week `doit`
learned to run a pipeline's steps in order. Tonight the last steps are the
reports: the pipeline executes its own notebooks and builds its own ChartBook
site, GitHub Pages hosts that site, and LaTeX makes the PDF. The habit to build
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
  ChartBook site on GitHub Pages and summarized in a PDF market brief.
- **Project survey, due next Tuesday.** The preference survey is open, and the
  link is in the Canvas announcement. It closes Tuesday, October 20 at
  11:59 pm. Each group is emailed its assigned paper from the
  [Potential Final Projects](./FinalProject/potential_final_projects.md) list
  on Wednesday morning, October 21.
- **Book your project consultation.** Each group meets with me once, in weeks 4
  through 6, for feedback on its plan:
  [youcanbook.me](https://finm-32800.youcanbook.me/).

## Objectives

- Use a notebook for what it is good at, reporting, and keep pipeline logic out
  of it: [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md).
  Execute a notebook from the command line with `nbconvert` and wire it into
  `doit`.
- Register a project's notebooks and dataframes in a `chartbook.toml` and build
  a browsable site from it, as one more `doit` target.
- Publish a `docs/` folder as a live website:
  [Publishing to GitHub Pages](./Week3/github_pages_preview.md).
- Write and compile LaTeX, and automate the inclusion of a figure or table so a
  PDF rebuilds itself from the pipeline's outputs:
  [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md).
- Know the layout a data project should have, and why, and start one with
  `chartbook init`: [the ChartBook project template](./Week3/project_structure.md).
- Know the alternatives to conda for pinning an environment, and set up a
  project with one: [`uv` and `pixi`](./Week3/uv_and_pixi.md).
- Use the pull-request workflow: branch, PR, review, revise, merge
  ([GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md)).
- Explain how the Nelson-Siegel-Svensson model fits a yield curve to Treasury
  quotes, and how fed funds futures prices imply an expected policy path.
- Pull market data from a vendor's API with a key kept in `.env`, and check
  what a query costs before running it: [Databento](./Week7/databento.md).

## Agenda Item 1: Notebooks as Tasks

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

## Agenda Item 2: ChartBook, the Site as a Build Target

[ChartBook](https://pypi.org/project/chartbook/) catalogs a project's
notebooks, dataframes, and charts and generates a browsable static site from
them.

- **The manifest, `chartbook.toml`.** Each `[notebooks.*]`, `[dataframes.*]`,
  and `[charts.*]` entry says what an output is, where it comes from, and where
  it lives. Read HW 1's as the example.
- **The CLI and the Python API.** `chartbook build` generates the site;
  `chartbook ls` lists what is registered; `chartbook data get-path` returns a
  parquet path; and `data.load(pipeline=..., dataframe=...)` loads it straight
  into pandas or polars.
- **Where it sits in the pipeline.** `chartbook build` is just another `doit`
  task (`task_generate_pipeline_site` in HW 1), so the site is regenerated
  whenever the data changes, not assembled by hand. Publishing it is the next
  item; in week 4, many projects' manifests aggregate into one catalog.

## Agenda Item 3: Publishing to GitHub Pages

[Publishing to GitHub Pages](./Week3/github_pages_preview.md): how a `docs/`
folder of static HTML becomes a live website (Settings → Pages → Deploy from a
branch → `main` / `/docs`), and why your assignment repo must stay private while
the site is public. The two steps compose: `chartbook build` produces `docs/`,
Pages serves it. HW 2 Part D is exactly this, into a separate public repo.

## Agenda Item 4: Starting a Project with `chartbook init`

Your final project starts here, so we start one live.

- [The ChartBook project template](./Week3/project_structure.md): the directory
  layout, and the
  [Cookiecutter Data Science](https://drivendata.github.io/cookiecutter-data-science/)
  principles behind it (data is immutable, analysis is a DAG, secrets stay out
  of version control).
- `chartbook init` is a Cookiecutter template. Created with
  [Cruft](https://cruft.github.io/cruft/) instead, the project can pull in later
  improvements to the template with `cruft update`.
- The template asks which environment manager to use. That is the cue for
  [`uv` and `pixi`](./Week3/uv_and_pixi.md), the follow-up to week 1's conda
  material: we scaffold one project with each and compare the lockfiles they
  write.

## Agenda Item 5: LaTeX for PDF Reports

ChartBook is the HTML half of report generation; LaTeX is the PDF half.

- [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md). Compile the report templates
  from the [project template](./Week3/project_structure.md), in the project you
  just scaffolded with `chartbook init`.
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
  on `reports/market_brief.tex`.

## Agenda Item 6: Issues and Pull Requests

[GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md): how
contribution works in the open-source world, and the workflow you will use for
the collaborative report and for your final project.

- Issues as the coordination layer: claiming work, scoping it, linking fixes with
  `fixes #123`.
- The PR loop, demoed live: branch → commit → open a pull request → review
  comments → revise → merge.
- Why maintainers protect `main` and require review, and how these mechanics let
  strangers collaborate safely on the packages we depend on. This is the "peer
  code review" that shows up in the job postings.

## Agenda Item 7: Launch HW 2, the Yield Curve and the Policy Path

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

The futures come from [Databento](./Week7/databento.md), a market data vendor
whose CME Globex feed the course subscribes to. It is the pattern you used
for WRDS in week 1, with a different kind of credential: an API key in
`.env`, a `pull_` function that calls the vendor, and a `load_` function that
reads what the pull cached. The
[`databento/` examples](https://github.com/finm-32800/inclass_examples/tree/main/databento)
in the in-class repo take it in small steps, through the Python client, the
raw HTTP API, and a free call that says what a query costs before you run it.
[Pulling Market Data From Databento](notebooks/_01_databento_ipynb.ipynb) is
the same material as a notebook.

Both replications are checked against the published numbers. The tests
compare your fitted yields with the Fed's at every maturity from 1 to 30
years, and your FedWatch probability with a recorded reading of the CME tool,
and each guide shows how closely they agree.

The pipeline pulls from four sources (CRSP via WRDS, the Fed's
published GSW series, ZQ futures from Databento, and FRED), and the
deliverables are a ChartBook site on GitHub Pages and a two-page market brief
on the Treasury curve, compiled from LaTeX. We launch it in class; you finish the
`TODO`s and publish on your own.

## Final Project: The Two Steps

The full procedure and rubric are in the
[Final Project Instructions and Rubric](./FinalProject/final_project_rubric.md);
we walk it in class. Two steps:

1. **Consultation.** One-on-one with me,
   [booked via youcanbook.me](https://finm-32800.youcanbook.me/), in weeks 4
   through 6. Come ready to say what the paper is about, your data sources, and
   the product you will build: the new table or figure, and who would use it.
   The consultation is ungraded.
2. **Final project presentation**, week 10. Your project is due 24 hours before
   a 30-minute meeting with me. There are no slides and no demo, because I will
   already have run your code. I question each member individually, and those
   answers are graded.

## Looking ahead to Week 4

Week 4 turns the code itself into something installable and documented: **Python
packaging and Sphinx**. [HW 3](./HW3.md) launches, an installable package that
recovers option-implied crash probabilities from the volatility smile.
