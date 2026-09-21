# Homework 4

- **Due Date:** Sunday, August 23, 2026 at 11:59 PM CT
- **Link to Assignment:** TBD

```{note}
Homework 4 has been reworked. An earlier version of this assignment was an
options case study; that assignment has been retired, and the options
notebooks remain class material in Week 7. Homework 4 is now a short, focused
assignment: **deploy a live, self-updating clone of the CME FedWatch tool.**
```

## Overview

Homework 4 turns the in-class FedWatch case study
([finm-32800/case_study_fedwatch](https://github.com/finm-32800/case_study_fedwatch))
into a live monitor. The pipeline pulls 30-Day Fed Funds futures (ZQ) from
Databento and the effective federal funds rate (EFFR) from FRED, computes the
market-implied probability of a hike, cut, or no change at the next FOMC
meeting, and renders the forecast chart into a chartbook site. You will
(1) fill in the small amount of math that has been removed and (2) publish the
site so that a GitHub Action rebuilds it every morning, unattended.

The two class notebooks from Week 8 walk through everything the pipeline does:

- [30-Day Fed Funds Futures Data from Databento](notebooks/_01_fed_funds_futures_data.ipynb)
- [Replicating the CME FedWatch Tool](notebooks/_02_fedwatch_replication.ipynb)

This assignment is the Week 8 material—GitHub Actions, cron scheduling, and
GitHub Pages—put into practice. It is also the course's themes in miniature:
a reproducible analytical pipeline that runs end-to-end with no human in the
loop, from raw data pull to published product.

## Learning Outcomes

- Schedule recurring jobs with cron in GitHub Actions
- Publish a static site with GitHub Pages
- Manage API keys with Actions repository secrets (in CI) and `.env` files (locally)
- Run a full `doit` pipeline unattended in CI
- Understand how fed funds futures prices imply FOMC meeting-outcome probabilities

## What You Do

The assignment repository's README spells out every step; in brief:

### Task 1: Fill in the FedWatch math (1 point)

Three functions in `src/fedwatch.py` have had their bodies replaced with
`raise NotImplementedError(...)`: `implied_rate`, `solve_post_meeting_rate`,
and `move_probability`. Each docstring specifies exactly what the function
must do, and the [replication notebook](notebooks/_02_fedwatch_replication.ipynb)
derives the same formulas step by step.

Your feedback loop runs offline, with no API key:

```bash
pytest -vv ./src/test_fedwatch.py ./src/test_fedwatch_monitor.py
```

Once those tests pass, run the full pipeline locally: copy `.env.example` to
`.env`, add your Databento API key (the pull is cost-guarded and free), and
run `doit`. The built site lands in `docs/index.html`.

### Task 2: Deploy your daily self-updating site (2 points)

The workflow in `.github/workflows/deploy_pages.yml` rebuilds the pipeline and
publishes the chartbook site to GitHub Pages—on every push to `main` and on a
daily cron (14:30 UTC, mid-morning Chicago, after the NY Fed's ~9 AM ET EFFR
print). You will:

1. Push your completed pipeline to a new **public** repo under your personal
   GitHub account (the assignment repo itself stays private).
2. Add your `DATABENTO_API_KEY` as an Actions repository secret—never commit
   it in code.
3. Run the workflow once by hand and watch it go green: it pulls the data,
   executes the notebooks, renders the forecast chart, builds the site, runs
   the tests, and pushes the result to the `gh-pages` branch.
4. Enable GitHub Pages on `gh-pages` and confirm your site is live at
   `https://<you>.github.io/<repo>/`.
5. Back in the assignment repo, record your attestation: set the flag to
   `True` and paste your live site URL in `src/monitor_self_attestation.py`,
   then commit and push.

From then on, the cron refreshes your forecast every morning with no action
from you. I will visit the URL you provide and check that the site is live
and current.

## Grading

3 points total, autograded on every push by GitHub Actions:

| Component | Points |
|---|---|
| FedWatch math tests pass (Task 1) | 1 |
| Monitor attestation: flag set (Task 2) | 1 |
| Monitor attestation: live site URL provided (Task 2) | 1 |

I will spot-check the attested URLs to confirm the sites are live and
updating on schedule.

## Warnings

- The assignment repo must stay **private**—it is your graded work. Your
  separate deploy repo will be public, including your completed Task 1 code;
  that is intended for this assignment.
- Your API key goes into `.env` locally and into the Actions secret in CI. If
  it ends up in a commit anywhere, revoke it and generate a new one.
- Do not edit the test files (`test_*.py`).
