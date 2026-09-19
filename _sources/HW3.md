# Homework 3

```{toctree}
:maxdepth: 1
notebooks/_01_CRSP_treasury_overview_ipynb.ipynb
notebooks/_02_replicate_GSW2005_ipynb.ipynb
```

- **Due Date:** Friday, July 24, 2026 at 11:59 pm CT.
- **Link to Assignment:** https://classroom.github.com/a/p8tcNbUG

## Overview

In this homework you build a complete, reproducible data pipeline end to end: pull raw U.S. Treasury data, fit the term structure of interest rates, and **publish the results as a browsable website**. It is the same shape as every serious replication in this course — pull, clean, model, test, report — but here the *report* is the point. You will turn your pipeline's outputs into a [ChartBook](https://pypi.org/project/chartbook/) site and deploy it to GitHub Pages, exactly the workflow your final-project chartbook will use.

The financial content is the U.S. Treasury yield curve. You will estimate it with the Nelson-Siegel-Svensson (NSS) model, following [Gurkaynak, Sack, and Wright (2006)](https://www.federalreserve.gov/pubs/feds/2006/200628/200628abs.html) — the same methodology the Federal Reserve uses to publish its daily yield-curve series. Fitting a smooth curve to the cross-section of noisy Treasury quotes is a small, self-contained modeling problem with a clean data pipeline behind it, which is what makes it a good vehicle for the publishing tools this week.

## Learning Outcomes

- Estimate the U.S. Treasury yield curve using the Nelson-Siegel-Svensson model following Gurkaynak, Sack, and Wright (2006).
- Use the [`finm`](https://jeremybejarano.com/finm/) package to implement the yield-curve functions (spot rates, discount factors, cashflow construction, and model fitting).
- Build and verify a full data pipeline with `doit`, pulling from WRDS (CRSP Treasuries) and the Federal Reserve.
- Generate a **ChartBook** site from your pipeline's registered dataframes and charts, and **publish it to GitHub Pages**.
- Use GitHub to scan a repository for secrets and to revise Git commit history.

## Part 1 (graded): The Yield-Curve Pipeline

Please refer to the HW repo `README.md` for full details. The graded tasks are:

- **Task 1: GSW Yield Curve Module (2 pts)** — Replace the `TODO`/`NotImplementedError` placeholders in `src/gsw2006_yield_curve.py` with imports from the [`finm`](https://jeremybejarano.com/finm/) package's `fixedincome` module (`from finm.fixedincome import spot, discount, calc_cashflows, fit, gurkaynak_sack_wright_filters`). The unit tests in `src/test_gsw2006_yield_curve.py` are the specification.
- **Task 2: Data Pipeline (3 pts)** — Make the full pipeline run with `doit`, so that the data pull, model fit, and notebook execution complete and produce their targets.
- **Task 3: Federal Reserve Yield Curve Data (1 pt)** — Implement `task_pull_fed_yield_curve_data` in `dodo.py`, which runs `src/pull_yield_curve_data.py` (downloading the Fed's published GSW series) and produces `fed_yield_curve.parquet`.
- **Task 4: Notebooks (1 pt)** — Ensure the two Jupyter notebooks execute successfully as part of the pipeline.
- **Task 5: GitHub Skills (2 pts)** — Complete the [Change Commit History](https://github.com/skills/change-commit-history) and [Introduction to Secret Scanning](https://github.com/skills/introduction-to-secret-scanning) GitHub Skills exercises, then set the flags and paste your completed-repo URLs in `src/github_skills.py`. (Secret scanning matters here: your WRDS credentials live in a `.env` file that must never be committed.)

## Part 2 (graded): Publish Your ChartBook Site to GitHub Pages (2 pts)

The pipeline registers its outputs — the consolidated CRSP Treasury data and the Fed's published GSW yield-curve series — as **dataframes** in `chartbook.toml`. [ChartBook](https://pypi.org/project/chartbook/) assembles those, together with your executed notebooks, into a static documentation website.

1. Build the site (this is the `task_build_chartbook_site` task in `dodo.py`):
   ```bash
   chartbook build -f
   ```
   This generates the `docs/` folder — a full static site (`index.html`, the executed notebooks, and dataframe pages).

2. Create a **new, separate public repository under your personal GitHub account** (for example, `finm32800-hw3-site`) to host the site. Do **not** make your assignment repo public, and do not try to enable Pages on it — it stays private inside the class organization. On GitHub's free tier, Pages only works on public repositories, and your assignment repo contains your graded solutions, tests, and git history, none of which should ever be public. The `docs/` folder is deliberately gitignored in the assignment repo.

3. Copy the built site into the new repo and push it. From the directory containing your assignment repo:

   ```bash
   git clone https://github.com/<your-username>/finm32800-hw3-site.git
   cp -R <your-hw3-repo>/docs/. finm32800-hw3-site/
   cd finm32800-hw3-site
   git add .
   git commit -m "Publish ChartBook site"
   git push
   ```

   The `docs/.` form (with the trailing `/.`) matters: it copies hidden files such as `.nojekyll`, which the site needs to render correctly.

4. Enable **GitHub Pages** on the *new* repo (Settings → Pages → Deploy from a branch → `main` / `/ (root)`). After a few minutes, verify the site is live at `https://<your-username>.github.io/finm32800-hw3-site/`, then record the published URL where the autograder expects it (see the HW repo `README.md`).

Note the separation this gives you: the rendered site is public — that is the point of publishing — but your source code, unit tests, git history, and credentials never leave the private assignment repo. Whenever you rebuild the site, just re-copy `docs/` into the site repo and push again.

For what ChartBook is and how the pieces fit together, see [Project Structure: "Chartbook" Template](./Week3/project_structure.md); for the GitHub Pages mechanics, see [Publishing to GitHub Pages](./Week3/github_pages_preview.md). We cover both in class this week.

**Total: 11 points.**

## Part 3 (not graded, but required): Schedule Your Final-Project Proposal Consultation

This is separate from the pipeline above — it is part of the **final-project proposal** process, not this homework's grade. Before your assigned proposal-presentation date, you must meet 1-on-1 with the instructor.

- **Booking link:** [https://finm-32800.youcanbook.me/](https://finm-32800.youcanbook.me/)
- You must schedule it to occur **at least one week before** your group's proposal presentation. Booking it before the HW deadline is what's required now — the meeting itself can be later.
- Come prepared with your assigned paper, your data sources, a rough plan for the product you'll build, and how you'll divide the work.

See the [Proposal Presentation Rubric](./FinalProject/proposal_presentation_rubric.md) for how the proposal and consultation fit together.

## Reminder

Do not make any changes to the unit test files. If an edit is made, you will be required to edit the history of your commits to remove any trace of the edits to these files.
