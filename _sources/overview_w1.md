# Week 1: Git, GitHub, and Virtual Environments

```{toctree}
:maxdepth: 1
Week1/what_is_this_course_about.md
Week1/reproducible_analytical_pipelines.md
Week1/case_study_reproducibility_in_finance.md
Week1/virtual_environments.md
Week2/WRDS_intro_and_web_queries.md
notebooks/_01_wrds_python_package_ipynb.ipynb
Week2/env_files.md
notebooks/_07_mean_variance_to_CAPM_ipynb.ipynb
```

The paper this week is Sharpe (1964) and the object is the CAPM's market
portfolio. The tools are the three that everything else in the course sits on:
Git, GitHub, and a pinned environment. By the end of class you will have cloned
a repository, built its environment, connected to WRDS, and pulled your first
data.

## Announcements

- **Apply for your WRDS account today.** Approval takes several days and
  [HW 1](./HW1.md) cannot run without it. The registration link and the
  UChicago contact are in [Getting Set Up](./Week0/getting_set_up.md).
- **Complete the GitHub username survey on Canvas today.** Your private copy
  of [HW 1](./HW1.md) is created from your answer, so you have no repository
  to work in until it is in. GitHub then emails you an invitation to the
  [hw-finm-32800](https://github.com/hw-finm-32800) organization. Accept it
  within seven days, after which it expires.
- **[HW 0](./HW0.md) is ungraded and due to nobody, but do it now.** It sets up
  your machine and walks the full cycle every later assignment repeats: clone,
  install, run, edit, test, push. HW 1 assumes all of it works.
- **[HW 1](./HW1.md) launches today** and is due Tuesday, October 20, at the
  start of the week 4 session. **Aim to be done by October 13,** when
  [HW 2](./HW2.md) launches. The third week is slack, not the plan.
- **Clone the in-class examples repo**:
  <https://github.com/finm-32800/inclass_examples>. It holds the small,
  self-contained demos we draw on all quarter (environments, env vars, PyDoit,
  SQL, LaTeX, Polars, Sphinx). We use `software_environments/` and `env_vars/`
  tonight.
- **Watch the course website repository** so you get discussion-board posts:
  <https://github.com/finm-32800/finm-32800.github.io>. Ask questions on the
  [discussion board](https://github.com/orgs/finm-32800/discussions) rather than
  by email.
- **Start thinking about a final-project group.** Projects are done in groups of
  four. The project list and the preference survey go out in week 2.

## Objectives

- Know what this course is and where it fits:
  [What is this course about?](./Week1/what_is_this_course_about.md) and the
  [course map](./Week0/course_map.md), which shows the job postings behind the
  skills and the schedule of papers.
- Be able to say what a reproducible analytical pipeline is and why it is the
  organizing idea of the course:
  [Reproducible Analytical Pipelines](./Week1/reproducible_analytical_pipelines.md).
- Understand the stakes:
  [Is There a Reproducibility Crisis in Finance?](./Week1/case_study_reproducibility_in_finance.md)
- Use Git and GitHub for the course's workflow: clone, branch, commit, push, and
  read a pull request.
- Create and pin a [virtual environment](./Week1/virtual_environments.md), and
  explain why an analysis that runs on your laptop and nowhere else is not a
  result.
- Connect to WRDS and pull CRSP data:
  [Introduction to WRDS and WRDS Web Queries](./Week2/WRDS_intro_and_web_queries.md),
  then automate it with the
  [WRDS Python package](notebooks/_01_wrds_python_package_ipynb.ipynb).
- Keep credentials out of Git: [Env Files](./Week2/env_files.md).
- State what the CAPM claims and what the market portfolio is in practice.

## Agenda

1. **Introduction.** Who I am, and the [syllabus](./README.md). Walk the
   [course map](./Week0/course_map.md): who the course is for, the postings
   behind its skills, and the nine papers.
2. **What we are building all quarter.**
   [What is this course about?](./Week1/what_is_this_course_about.md),
   [Reproducible Analytical Pipelines](./Week1/reproducible_analytical_pipelines.md),
   and the [reproducibility case study](./Week1/case_study_reproducibility_in_finance.md).
   Then the final project in outline, so you know what you are working toward.
3. **Git and GitHub, by doing.** Clone [HW 0](./HW0.md), make a change, commit,
   push. This is also how every assignment is submitted. Point at the three
   [GitHub Skills](https://skills.github.com/) tutorials that are HW 1 Part A:
   [introduction to GitHub](https://github.com/skills/introduction-to-github),
   [communicate using Markdown](https://github.com/skills/communicate-using-markdown), and
   [introduction to Git](https://github.com/skills/introduction-to-git).
4. **Virtual environments.** [Virtual Environments](./Week1/virtual_environments.md),
   worked through `software_environments/` in the in-class repo, which builds the
   same small app four ways: `conda`, `conda` + `pip`, `uv`, and `pixi`. Create
   the `finm` environment now; every repo this quarter installs into it.
   *Why we care:* you will be handed a series of repositories, each with pinned
   dependencies, and the pinning is the only reason my results and yours agree.
5. **First contact with WRDS.**
   [WRDS and web queries](./Week2/WRDS_intro_and_web_queries.md) to explore CRSP
   by hand, so the automated pull is not a black box. Then the
   [WRDS Python package notebook](notebooks/_01_wrds_python_package_ipynb.ipynb)
   to automate the same query, and
   [Env Files](./Week2/env_files.md) plus `env_vars/` in the in-class repo for
   where the credentials live. Creating a `.pgpass` file so the pulls
   authenticate without prompting is covered in that notebook, and HW 1 needs it.
6. **The CAPM, and what the market portfolio is in practice.** The theory says
   market beta is the only priced risk. The practical object is a
   value-weighted index of everything, which is what CRSP publishes and what you
   are about to rebuild. This is the framing for HW 1's first half; the alphas
   the CAPM leaves behind are next week's problem. For how the CAPM follows
   from the HW 0 tangency portfolio, see
   [From Mean-Variance to the CAPM](notebooks/_07_mean_variance_to_CAPM_ipynb.ipynb).
7. **Launch [HW 1](./HW1.md).** Clone it, build the environment, put credentials
   in `.env`, run `doit`, run `pytest`, watch it fail, and start filling in
   `calc_CRSP_indices.py` together.

## Homework

[HW 1](./HW1.md) launches today and is due Tuesday, October 20, though you
should aim to finish it by October 13, when HW 2 launches. It is one
assignment covering two papers. This week's half rebuilds the CRSP
value-weighted and equal-weighted market indices and reconstructs the S&P 500
from its constituents:

- [HW 1 Guide B: The CRSP Market Index](notebooks/_03_CRSP_market_index_ipynb.ipynb)
- [HW 1 Guide C: Reconstructing the S&P 500](notebooks/_04_SP500_constituents_and_index_ipynb.ipynb)

Next week's half merges CRSP with Compustat and replicates Fama and French
(1993). You do not need to wait for it to start Parts A to C.

The way to work is the same in every assignment: run `doit` so the data is
pulled, then run `pytest`, read the failing test, and write the code that
passes it. Do not edit the test files.

## Looking ahead to Week 2

Once you have a pull you trust, the next question is how to make a dozen of them
run in the right order with one command. That is the **PyDoit task runner**, and
the case study is the **Fama-French 1993** replication that completes HW 1. Week
2 is also when the final-project list and the group-preference survey go out.
