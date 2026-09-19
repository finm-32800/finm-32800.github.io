# Week 3: Automating the Pipeline with PyDoit, featuring Fama-French 1993

```{toctree}
:maxdepth: 1
Week3/what_is_a_task_runner.md
Week3/doit_examples.md
Week3/project_structure.md
Week3/uv_and_pixi.md
Week3/ftsfr.md
```

## Announcements

- **Final project survey**: This week I will email a survey where your group indicates which projects interest you. Find your project partner now---each group must have **exactly 2 people**, and only **one person per group** submits the survey. I will do my best to assign each group a project it ranked highly. Project assignments will be emailed shortly after the survey closes.
- **HW 1 is due Friday, July 10.** We'll take questions at the start of class.

## Objectives

The core of this week is the **PyDoit task runner**, and the case study that motivates it is the **Fama-French 1993 replication** that is **[HW 2](./HW2.md)** (due Friday, July 17): merge **CRSP with Compustat**, construct the size and value factors, and reproduce the sorted portfolios. A replication like this is a *pipeline*---pull, clean, merge, construct, test, report---and once a project has more than two steps, running them by hand in the right order becomes the main source of irreproducibility. The task runner is the fix, and it's the glue for every repository you'll touch for the rest of the quarter, including your final project.

- **Why task runners?** [Build Systems and Task Runners](./Week3/what_is_a_task_runner.md)---what problems they solve, and where `doit` sits relative to Make and friends.
  - *→ HW 2:* the assignment repo is organized as a `dodo.py` pipeline; understanding the dependency graph is understanding the assignment.
- **PyDoit hands-on**: [PyDoit Examples Walkthrough](./Week3/doit_examples.md), using the `pydoit/` directory of the [in-class examples repo](https://github.com/finm-32800/inclass_examples). Tasks, file dependencies, targets, and why `doit` only re-runs what changed.
- **Multifactor asset pricing models**: it's hard to appreciate the construction details of the Fama-French factors without first understanding the model they serve. The CAPM says market beta is the only priced risk---every portfolio's alpha should be zero. We'll test that claim on portfolios sorted by size, book-to-market, and investment in the [CAPM analysis notebook](./notebooks/_06_CAPM_analysis_ipynb.ipynb), find the significant alphas the CAPM leaves behind, and see in the [Fama-French 3-factor notebook](./notebooks/_07_Fama_French_3_factor_ipynb.ipynb) how adding SMB and HML absorbs them. That is the whole reason the factors exist---and why we're about to build them.
  - *→ HW 2:* these two notebooks run on the exact portfolios your HW 2 pipeline produces.
- **The case study**: with the model in hand, walk through the Fama-French pipeline end to end---automated CRSP and Compustat pulls feeding factor construction, with unit tests at the end. This is the same automated-query pattern from Week 2, now embedded in a pipeline that rebuilds itself with one command.
  - *→ HW 2:* we'll launch the assignment together in class; you'll finish the factor construction on your own.
- **Project structure**: [Introduction to the "Chartbook Project" Template](./Week3/project_structure.md)---the directory layout your final project will use. (My older template is now deprecated; we'll return to the chartbook's pipelines and catalogs in Week 4.)
- **Modern environment tools**: [`uv` and `pixi`](./Week3/uv_and_pixi.md)---the promised hands-on follow-up to the Week 1 preview, if time permits.

## Final Projects: Preview and Pitch

This is the week you meet the final projects.

- **What does a replication look like?** Before the list, we'll walk through the [Project Previews](./FinalProject/project_previews.md) page---three of this quarter's papers with the actual figures and tables you'd reproduce.
- **The full list**: [Potential Final Projects](./FinalProject/potential_final_projects.md), organized by topic. Skim it before the survey goes out; each entry states the exact tables and figures to reproduce and the data sources involved (all verified to be available to you).
- **Where this can lead---the FTSFR story**: past final projects from this course grew into the [Financial Time Series Forecasting Repository](./Week3/ftsfr.md), which is now a paper: ["An Open Benchmark for Evaluating Time Series Forecasting Methods across Financial Markets"](https://jeremybejarano.com/downloads/draft_ftsfr.pdf)---with former students as coauthors. I'll demo the repository ([github.com/jmbejara/ftsfr](https://github.com/jmbejara/ftsfr)) and make the key point: every dataset in it is **fully reproducible from source with one command**, and that reproducibility---not any single forecast---is the paper's key innovation. The skills in this course (automated pulls, pinned environments, task runners, tests) are exactly what made it possible.

## Looking ahead to Week 4

Once HW 2 gives you factor loadings worth reporting, Week 4 turns to **reports and publishing**: Jupyter notebooks as final reports, LaTeX, and tearsheets published to the web with **GitHub Pages**. Project assignments will be out by then, and [HW 3](./HW3.md) will have you stand up your final-project repository using the chartbook template.
