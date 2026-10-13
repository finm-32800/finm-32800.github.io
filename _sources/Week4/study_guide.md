# Study Guide: Week 4

Open-ended questions on the material from week 4. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

The sections below are the ones that moved here from week 3. Questions on
packaging and documentation are added after the week 4 session.

## Starting a Project

*Read: [Project Structure](../Week3/project_structure.md) and
[Modern Environment Tools: uv and pixi](../Week3/uv_and_pixi.md).*

1. What does Cookiecutter do? What does Cruft add on top of it?
2. What is a lockfile? What does `uv.lock` record that a hand-written
   `requirements.txt` does not?
3. `uv` installs from PyPI and `pixi` installs from conda-forge. When would
   that difference make you choose `pixi`?
4. `uv run` and `pixi run` both run a command inside the project's
   environment. What does `doit` do that they do not?

## Issues and Pull Requests

*Read: [GitHub Issues and Pull Requests](../Week6/GitHub_pull_requests.md).*

5. What is a pull request? What is the difference between pushing a commit
    to `main` and opening a pull request?
6. `git pull` and a pull request sound alike. What does each one do?
7. What is a GitHub issue, and what does writing `fixes #123` in a pull
    request do?
8. Why do maintainers protect `main` and require a review before merging?

