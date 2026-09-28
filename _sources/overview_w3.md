# Week 3: Reproducible Reports --- ChartBook, GitHub Pages, and LaTeX

```{toctree}
:maxdepth: 1
Week4/reports_with_jupyter_notebooks.md
Week3/github_pages_preview.md
Week4/intro_to_LaTeX.md
Week4/latex_essentials.md
Week6/GitHub_pull_requests.md
```

A pipeline's output has to land somewhere people can see it. This week is the
publication end of the pipeline: notebooks as reports, ChartBook as a catalog of
a project's dataframes and charts, GitHub Pages as the host, and LaTeX for the
PDF. The habit to build is treating a report not as a document you write once but
as a **build target** the pipeline regenerates whenever the data changes.

## Announcements

- **[HW 1](./HW1.md) is due at the end of this week.** The date is on Canvas.
- **[HW 2](./HW2.md) launches today,** due in week 5: the Treasury yield curve
  and the policy path, published as a ChartBook site on GitHub Pages.
- **Project assignments.** Each group is emailed its assigned paper from the
  [Potential Final Projects](./FinalProject/potential_final_projects.md) list. If
  you have not submitted your preferences, do it today.
- **Proposal presentations begin in week 5.** The schedule is posted on Canvas.
  Your **instructor consultation must happen at least one week before you
  present**, so if you present in week 5, book it now:
  [youcanbook.me](https://finm-32800.youcanbook.me/).

## Objectives

- Use a notebook for what it is good at, reporting, and keep pipeline logic out
  of it: [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md).
  Execute a notebook from the command line with `nbconvert` and wire it into
  `doit`.
- Register a project's dataframes and charts in a `chartbook.toml` catalog and
  build a browsable site from it.
- Publish a `docs/` folder as a live website:
  [Publishing to GitHub Pages](./Week3/github_pages_preview.md).
- Write and compile LaTeX, and automate the inclusion of a figure or table so a
  PDF rebuilds itself from the pipeline's outputs:
  [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md).
- Use the pull-request workflow: branch, PR, review, revise, merge
  ([GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md)).
- Explain how fed funds futures prices imply an expected policy path, and how
  that relates to the forward curve you fit from Treasury quotes.

## Agenda Item 1: Notebooks as Reports

[Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md): why
notebooks are for *reporting* and not for pipeline steps, how `nbconvert`
executes one from the command line and exports it to HTML, and how a notebook
becomes a `doit` task (`task_run_notebooks` in the HW 2 repo).

## Agenda Item 2: ChartBook

[ChartBook](https://pypi.org/project/chartbook/) is a catalog for a data
project's pipelines, dataframes, and charts that generates a searchable static
site, bundling the notebook reports above with the pipeline's data into one
place.

- **Where it sits in the pipeline.** `chartbook build` is just another `doit`
  task. The site is a build target, regenerated from the catalog whenever the
  data changes, not a manual afterthought.
- **The catalog, `chartbook.toml`.** Register each `[dataframes.*]` (its sources,
  how it is pulled, the path to the parquet) and each `[charts.*]` (name,
  description, the dataframe it comes from, the path to the output). We demo this
  on the HW 2 pipeline's registered dataframes, and adding one chart live is a
  good exercise.
- **The CLI and the Python API.** `chartbook build` generates the site;
  `chartbook ls` lists every pipeline, dataframe, and chart; `chartbook data
  get-path` returns a parquet path; and `data.load(pipeline=..., dataframe=...)`
  loads it straight into pandas or polars.
- **Pipeline vs. catalog.** A single project is a *pipeline*; many pipelines
  aggregate into one *catalog*. We build the catalog side in week 6, when the
  class report needs data other repositories already produce.

## Agenda Item 3: Publishing to GitHub Pages

[Publishing to GitHub Pages](./Week3/github_pages_preview.md): how a `docs/`
folder of static HTML becomes a live website (Settings → Pages → Deploy from a
branch → `main` / `/docs`), and why your assignment repo must stay private while
the site is public. The two steps compose: `chartbook build` produces `docs/`,
Pages serves it. HW 2 Part 3 is exactly this, into a separate public repo.

## Agenda Item 4: LaTeX for PDF Reports

ChartBook is the HTML half of report generation; LaTeX is the PDF half.

- [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md). Compile the report templates
  from the [project template](./Week3/project_structure.md); scaffold a fresh
  project with `chartbook init` if you do not have one.
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

## Agenda Item 5: Issues and Pull Requests

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

## Agenda Item 6: Launch HW 2, the Yield Curve and the Policy Path

[HW 2](./HW2.md) is a multi-source pipeline that ends in a published website.
You estimate the U.S. Treasury yield curve with the Nelson-Siegel-Svensson
model, following
[Gürkaynak, Sack, and Wright (2006)](https://www.federalreserve.gov/pubs/feds/2006/200628/200628abs.html),
the methodology behind the Fed's daily published curve, using the
[`finm`](https://jeremybejarano.com/finm/) package for the fixed-income
functions:

- [CRSP Treasury Overview](notebooks/_01_CRSP_treasury_overview_ipynb.ipynb)
- [Replicating GSW (2006)](notebooks/_02_replicate_GSW2005_ipynb.ipynb)

Then you read the expected path of the policy rate out of 30-Day Fed Funds
futures, the way the CME FedWatch tool does:

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication.ipynb)

The last task puts them on the same axes on the same date and asks you to
explain the gap. The pipeline pulls from four sources (CRSP via WRDS, the Fed's
published GSW series, ZQ futures from Databento, and FRED), and the deliverable
is a ChartBook site on GitHub Pages. We launch it in class; you finish the
`TODO`s and publish on your own.

## Proposal Presentations: Procedure and Grading

Presentations begin in week 5. The full procedure and rubric are in the
[Proposal Presentation Rubric](./FinalProject/proposal_presentation_rubric.md);
we walk it in class. Three separate events, not to be confused:

1. **Instructor consultation.** One-on-one with me,
   [booked via youcanbook.me](https://finm-32800.youcanbook.me/), at least one
   week before your proposal presentation.
2. **Proposal presentation** (15% of the course grade), in class on your assigned
   date. *Advertise the product you will build*: what the paper is about, your
   data sources, and the new table or figure you will produce. Classmates submit
   a peer-feedback survey on every group.
3. **Final presentation and oral defense**, week 10: the completed project, with
   each member individually quizzed.

**Proposal attendance and feedback** is a further 5% of the grade: you must
attend every proposal session and submit the survey for every group, with no
allowed misses.

## Looking ahead to Week 4

Week 4 turns the code itself into something installable and documented: **Python
packaging and Sphinx**. [HW 3](./HW3.md) launches, an installable package that
recovers option-implied crash probabilities from the volatility smile.
