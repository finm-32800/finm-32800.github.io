# Study Guide: Week 1

Open-ended questions on the material from week 1. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Why Reproducibility

*Read: [Is There a Reproducibility Crisis in Finance?](./case_study_reproducibility_in_finance.md)
and [Reproducible Analytical Pipelines](./reproducible_analytical_pipelines.md).*

1. Reinhart and Rogoff's paper "Growth in a Time of Debt" contained a
   spreadsheet error. What went wrong, and which practice from this course
   would have caught it?
2. A corporate bond factor paper in the *Journal of Financial Economics* was
   retracted after a doctoral student tried to replicate it. What went wrong,
   and which practice from this course would have caught it?
3. What is a reproducible analytical pipeline? What does it mean for a number
   in a report to have provenance?

## Git and GitHub

*Read: [HW 1 Guide A: GitHub Skills](../HW1_guide_A_github_skills.md) and its
three tutorials, and the Git and GitHub entries under "Which Tools?" in
[What is this course about?](./what_is_this_course_about.md)*

4. What is the difference between Git and GitHub?
5. What does each of these do: `git clone`, `git add`, `git commit`,
   `git push`? Which of them change only your computer, and which change the
   copy on GitHub?
6. What is a branch, and why make one instead of committing straight to
   `main`?
7. What is the difference between `git fetch` and `git pull`?
8. Why does a pull request passing on GitHub Actions say more than `pytest`
   passing on your laptop? Name two things the Actions run checks that your
   laptop cannot.

## Software Environments

*Read: [Virtual Environments](./virtual_environments.md). For `PATH`, work
through
[`env_vars/04_path_exploration`](https://github.com/finm-32800/inclass_examples/tree/main/env_vars/04_path_exploration)
in the in-class examples repository.*

9. What is a virtual environment, and what problem does it solve?
10. `pip` and `conda` both install packages. Name two things `conda` can do
    that `pip` cannot.
11. Compare a `requirements.txt` with a conda `environment.yml`. What does
    each file record, and which command reads each one?
12. The course uses `conda` and `pip` together. What job does each one do in
    that combination?
13. What is the `PATH` environment variable? When you type `python`, how does
    the shell decide which Python to run, and what does `conda activate finm`
    do to `PATH`?

## Configuration and Secrets

*Read: [Env Files](../Week2/env_files.md) and the
[`env_vars/`](https://github.com/finm-32800/inclass_examples/tree/main/env_vars)
examples in the in-class repository.*

14. What is an environment variable? Give one example that is not a password.
15. What does "separate configuration from code" mean? Give one setting that
    should differ between your laptop and the autograder, and say where it
    lives.
16. What is a `.env` file, and how does a Python script read a value from it?
17. The course's pipelines read WRDS credentials from `.env` and `.pgpass`
    rather than from a variable in the code. Explain why, what goes wrong if
    `.env` is committed, and what role `.gitignore` plays.

## WRDS and CRSP

*Read: [Introduction to WRDS](../Week2/WRDS_intro_and_web_queries.md),
[Connecting to the WRDS Platform With Python](../notebooks/_01_wrds_python_package_ipynb.ipynb),
and [A Tour of the Data](../Week0/data_tour.md).*

18. What is WRDS? Is it a data vendor? Name three datasets you reach through
    it.
19. Why is CRSP the standard source for US stock returns in research? Give
    two features.
20. What is survivorship bias, and why would a database that drops delisted
    companies overstate returns?
21. When would you use CRSP through WRDS, and when Bloomberg? Give one
    strength of each.
22. In the `wrds` Python package, what is the difference between
    `db.get_table` and `db.raw_sql`?

## The CAPM

*Read: [From Mean-Variance to the CAPM](../notebooks/_07_mean_variance_to_CAPM_ipynb.ipynb)
and [HW 1 Guide B: The CRSP Market Index](../notebooks/_03_CRSP_market_index_ipynb.ipynb).*

23. The CAPM says market beta is the only priced risk. What is the market
    portfolio in the theory, what stands in for it in practice, and how is
    the CRSP value-weighted index built from constituent returns and market
    caps?
