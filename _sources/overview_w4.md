# Week 4: ChartBook, GitHub Pages, and Reproducible Reports

```{toctree}
:maxdepth: 1
Week4/reports_with_jupyter_notebooks.md
Week3/github_pages_preview.md
```

## Announcements

- **Project assignments go out Friday morning.** Each group will be emailed its assigned paper from the [Potential Final Projects](./FinalProject/potential_final_projects.md) list. If you haven't submitted your project preferences yet, please do so by tonight.
- **Proposal presentations begin next week, Thursday July 16.** The schedule of who presents when, and your assigned date, will be posted on Canvas.
- [HW 3](./HW3.md) is assigned, due **Friday, July 24**: the yield-curve pipeline, published as a ChartBook site on GitHub Pages.

## Agenda Item 1: Final Project Proposal Schedule and Requirements

The main new logistics this week. Full procedure and grading are in the [Proposal Presentation Rubric](./FinalProject/proposal_presentation_rubric.md); walk through it in class.

### The three separate events (don't confuse them)

1. **Instructor consultation**: 1-on-1 with me, [booked via youcanbook.me](https://finm-32800.youcanbook.me/). Must be scheduled at least one week before your proposal presentation. Booking it is [HW 3](./HW3.md) Part 3.
2. **Proposal presentation**: in-class over Zoom, on your assigned date (posted on Canvas). *Advertise the product you'll build*: what the paper is about, your data sources, and the new table/figure you'll produce. Classmates fill out a peer-feedback survey on every group.
3. **Final project presentation**: Week 10, completed project + individual oral defense.

### How the proposal is graded (20% of course grade)

- **Ambition (8 pts)**: is the proposed product meaningfully more than a mechanical replication?
- **Usefulness & interest (8 pts)**: judged partly from classmates' peer-feedback surveys. Make the case for why someone would clone your repo.
- **Clarity of data sources & analysis (4 pts)**: clearly explain your data and what your new table/figure will show.

Plus Proposal Attendance & Feedback (10% of course grade): you must attend *every* proposal session and submit the peer-feedback survey for *every* group; no allowed misses.

### Resources to point students to

- [Proposal Presentation Rubric](./FinalProject/proposal_presentation_rubric.md): the procedure and grading above.
- [Final Project Instructions and Rubric](./FinalProject/final_project_rubric.md): how the *project itself* is graded.
- [Potential Final Projects](./FinalProject/potential_final_projects.md): every assigned paper, its tasks, and its data sources.
- [Project Previews](./FinalProject/project_previews.md): what a finished replication looks like (papers #1, #2, #4).

## Agenda Item 2: Notebooks as Reports

Start with the report itself. [Reports with Jupyter Notebooks](./Week4/reports_with_jupyter_notebooks.md): why notebooks are for *reporting*, not pipeline steps; using `nbconvert` to execute a notebook from the command line and export it to HTML; and how a notebook fits into the `doit` workflow (as in the course [project template](./Week3/project_structure.md) and the yield-curve repo's `task_run_notebooks`).

## Agenda Item 3: ChartBook Deep Dive

This is the follow-on to Week 3's [project-structure discussion](./Week3/project_structure.md) that we didn't get to. [ChartBook](https://pypi.org/project/chartbook/) is a catalog for a data project's pipelines, dataframes, and charts that generates a searchable static website from them, bundling the notebook reports above with the pipeline's data into one site.

- **Where it sits in the pipeline**: `chartbook build` is just another `doit` task (`task_build_chartbook_site` in the yield-curve repo). The site is a *build target*, regenerated from the catalog whenever the data changes; it is part of the automated project structure, not a manual afterthought.
- **The catalog, `chartbook.toml`**: register each `[dataframes.*]` (its data sources, how it's pulled, path to the parquet) and each `[charts.*]` (name, description, the dataframe it comes from, path to the HTML/PDF). Demo this with the yield-curve pipeline's two registered dataframes (consolidated CRSP Treasuries, the Fed's published GSW series); the `[charts.*]` section is where a registered chart would go; a good live exercise is to add one.
- **The CLI**: `chartbook build` generates the site (it's the `task_build_chartbook_site` task); `chartbook ls` lists every pipeline, dataframe, and chart in the catalog; `chartbook data get-path` returns a dataframe's parquet path; and the Python API `data.load(pipeline=..., dataframe=...)` loads it straight into pandas or polars. (Run `chartbook ls` against your `~/.chartbook/chartbook.toml` catalog to see the FTSFR aggregation in action.)
- **Pipeline vs. catalog**: a single project is a *pipeline*; multiple pipelines aggregate into one *catalog* site, the pattern behind the [FTSFR repository](./Week3/ftsfr.md).
- **A note for next week**: ChartBook is my own package, published to PyPI. Building on it now sets up our discussion in two weeks on how you publish and contribute to an open-source package.

## Agenda Item 4: Publishing to GitHub Pages

- [Publishing to GitHub Pages](./Week3/github_pages_preview.md): how a `docs/` folder of static HTML becomes a live website (Settings → Pages → Deploy from a branch → `main` / `/docs`), and why the repo can stay private while the site is public.
- The ChartBook `docs/` site is exactly this kind of folder, so the two steps compose: `chartbook build` produces `docs/`, GitHub Pages serves it.

## Agenda Item 5: Launch HW 3, the Yield-Curve Pipeline as a Published Site

Now put it together on real data. [HW 3](./HW3.md) (due Fri July 24) is a complete, reproducible pipeline that ends in a *published website*. Students estimate the U.S. Treasury yield curve with the Nelson-Siegel-Svensson model (Gurkaynak, Sack & Wright 2006), using the [`finm`](https://jeremybejarano.com/finm/) package for the yield-curve functions and `doit` to orchestrate the WRDS/Fed data pulls, model fit, and notebooks. The deliverable is a ChartBook site published to GitHub Pages, the same publishing workflow their final-project chartbook will use.

We launch the case study together in class; students finish the `TODO`s and publish on their own. Everything above (notebook reports, ChartBook, GitHub Pages) is what makes this final step work.

% INSTRUCTOR-ONLY: Deferred to Week 5 (see overview_w5.md): LaTeX / PDF reports, the
% midterm report (single shared repo, GitHub pull requests), and publishing a PyPI
% package. Do NOT introduce the midterm report this week.
