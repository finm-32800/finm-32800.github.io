# Lecture 0: Portfolio Selection and a First Look at the Course

```{toctree}
:maxdepth: 1
Week0/course_map.md
Week0/tour_of_the_course.md
Week0/getting_set_up.md
Week0/clone_and_run.md
notebooks/_01_markowitz.ipynb
notebooks/_02_markowitz_derivation.ipynb
Week0/data_tour.md
```

Lecture 0 is a preview. It is meant to feel like a real first lecture, so that
you can decide whether this is the computing course you want to take. If you
missed it, the recording and this chapter cover the same ground, and
[Homework 0](./HW0.md) lets you try everything yourself.

## Objectives

- Understand what this course is, who it is for, and how it differs from the
  program's other computing courses. The [course map](./Week0/course_map.md)
  says who the course is for and shows the job postings behind the skills it
  teaches.
- Take a [tour of the course](./Week0/tour_of_the_course.md): this textbook, the
  course's GitHub organization, the discussion board, and a few projects from
  past quarters.
- [Get set up](./Week0/getting_set_up.md). In particular, apply for your WRDS
  account today, because approval takes a few days.
- [Clone a repository and run it](./Week0/clone_and_run.md). See what a
  `requirements.txt` file does and why it is the first step toward a
  reproducible result.
- Work through the week's paper, Markowitz (1952), in the
  [portfolio selection notebook](notebooks/_01_markowitz.ipynb), using real
  stock returns from CRSP. The mean-variance problem has a closed-form solution
  until you forbid short sales, and then it does not. The
  [appendix](notebooks/_02_markowitz_derivation.ipynb) derives the formulas.
- Take a [tour of the data](./Week0/data_tour.md) that we will use this quarter.

## In class

1. Who am I, and what is this course? Walk through the
   [course map](./Week0/course_map.md): who the course is for, the job postings
   behind its skills, and the schedule of papers.
2. Tour the textbook, the GitHub organization, and the discussion board.
3. Clone [HW 0](https://github.com/finm-32800/hw0), create the `finm`
   environment, install from `requirements.txt`, run `doit`, and launch the
   dashboard.
4. Portfolio selection: returns are linear in the portfolio weights, and risk is
   not. Work through the notebook and the dashboard together.
5. No short sales: one inequality constraint, and the formula is gone. Solve
   it numerically and compare the two frontiers.
6. Where did the data come from? Read `pull_crsp.py`, then see WRDS live.
7. The catch: the optimizer is an "error maximizer." Why that makes
   reproducibility a research problem and not only an engineering one.
8. Move the notebook's logic into tested functions and watch the tests pass on
   GitHub Actions. This is [Homework 0](./HW0.md).

## Homework 0

[HW 0](./HW0.md) is ungraded. It sets up your computer and your accounts for the
rest of the quarter, and it walks you through the full cycle that every later
homework repeats: clone, install, run, edit, test, push.
