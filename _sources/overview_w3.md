# Week 3: Publishing --- GitHub Pages, LaTeX, and Pull Requests

```{toctree}
:maxdepth: 1
Week3/github_pages_preview.md
Week4/intro_to_LaTeX.md
Week4/latex_essentials.md
Week6/GitHub_pull_requests.md
```

A pipeline's output has to land somewhere people can see it. Last week the
pipeline learned to build its own notebooks and its own ChartBook site. This week
is the publication end: GitHub Pages as the host for that site, and LaTeX for
the PDF. The habit to build is treating a report not as a document you write once but
as a **build target** the pipeline regenerates whenever the data changes.

## Announcements

- **[HW 1](./HW1.md) is due next Tuesday, October 20,** at the start of class.
  This is the week to finish it: ask your questions tonight, because from here
  on the material is HW 2.
- **[HW 2](./HW2.md) launches today,** due Tuesday, October 27, two weeks from
  tonight: the Treasury yield curve and the policy path, published as a
  ChartBook site on GitHub Pages and summarized in a PDF market brief.
- **Project assignments.** Each group is emailed its assigned paper from the
  [Potential Final Projects](./FinalProject/potential_final_projects.md) list. If
  you have not submitted your preferences, do it today.
- **Book your project consultation.** Each group meets with me once, in weeks 4
  through 6, for feedback on its plan:
  [youcanbook.me](https://finm-32800.youcanbook.me/).

## Objectives

- Publish a `docs/` folder as a live website:
  [Publishing to GitHub Pages](./Week3/github_pages_preview.md).
- Write and compile LaTeX, and automate the inclusion of a figure or table so a
  PDF rebuilds itself from the pipeline's outputs:
  [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md).
- Use the pull-request workflow: branch, PR, review, revise, merge
  ([GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md)).
- Explain how the Nelson-Siegel-Svensson model fits a yield curve to Treasury
  quotes, and how fed funds futures prices imply an expected policy path.

## Agenda Item 1: Publishing to GitHub Pages

[Publishing to GitHub Pages](./Week3/github_pages_preview.md): how a `docs/`
folder of static HTML becomes a live website (Settings → Pages → Deploy from a
branch → `main` / `/docs`), and why your assignment repo must stay private while
the site is public. The two steps compose: `chartbook build` produces `docs/`,
Pages serves it. HW 2 Part 3 is exactly this, into a separate public repo.

## Agenda Item 2: LaTeX for PDF Reports

ChartBook is the HTML half of report generation; LaTeX is the PDF half.

- [Introduction to LaTeX](./Week4/intro_to_LaTeX.md) and
  [LaTeX Essentials](./Week4/latex_essentials.md). Compile the report templates
  from the [project template](./Week3/project_structure.md), in the project you
  scaffolded last week with `chartbook init`.
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

## Agenda Item 3: Issues and Pull Requests

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

## Agenda Item 4: Launch HW 2, the Yield Curve and the Policy Path

[HW 2](./HW2.md) is a multi-source pipeline that ends in a published website and
a PDF.
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

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data_ipynb.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication_ipynb.ipynb)

The pipeline pulls from four sources (CRSP via WRDS, the Fed's
published GSW series, ZQ futures from Databento, and FRED), and the
deliverables are a ChartBook site on GitHub Pages and a one-page market brief
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
2. **Final presentation and oral defense**, week 10: the completed project, with
   each member individually quizzed.

## Looking ahead to Week 4

Week 4 turns the code itself into something installable and documented: **Python
packaging and Sphinx**. [HW 3](./HW3.md) launches, an installable package that
recovers option-implied crash probabilities from the volatility smile.
