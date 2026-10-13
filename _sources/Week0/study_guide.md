# Study Guide: Lecture 0

Open-ended questions on the material from Lecture 0. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Portfolio Selection (Markowitz 1952)

*Read: [Portfolio Selection: Markowitz (1952)](../notebooks/_01_markowitz.ipynb) and its
[appendix](../notebooks/_02_markowitz_derivation.ipynb).*

1. Expected return is linear in the portfolio weights and variance is not.
   Explain how that produces diversification, and why a portfolio can carry
   less risk than any single asset in it.
2. Start from the closed-form minimum-variance portfolio. Name the one change
   to the problem that removes the closed form, say how the problem is solved
   instead, and describe how the two frontiers differ.

## Clone, Install, Run

*Read: [Clone and Run](./clone_and_run.md).*

3. List the steps of the homework cycle (clone, install, run, edit, test,
   push). Say what a `requirements.txt` guarantees about getting my result on
   your machine and what it does not.
4. What is the difference between a pin (`pandas==2.2.3`) and a floor
   (`pandas>=2.0`) in `requirements.txt`? Why is a pin the reproducible
   choice?
5. Why does the course use one environment per project? What goes wrong when
   two projects' requirements are installed into the same environment?
6. What does running `doit` in a project do, and which file does it read to
   find out what to do?

## Tests

*Read: [Homework 0](../HW0.md), "Why are we doing it this way?", and the two
videos linked there.*

7. What is a unit test, and what does running `pytest` do?
8. What is test-driven development? How does the homework workflow (run
   `pytest`, read the failing test, write the code that passes it) imitate
   it?

## The Data

*Read: [A Tour of the Data](./data_tour.md).*

9. What is a PERMNO, and why do the course's CRSP pulls identify a stock by
   PERMNO rather than by ticker?
10. Why should the rest of a pipeline read a saved file instead of pulling
    from the data provider each time it runs?
11. The Bloomberg Terminal has the broadest coverage of any source we use.
    Why is it the hardest one to build an automated pipeline around?
