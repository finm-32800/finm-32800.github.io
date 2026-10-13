# Study Guide: Week 3

Open-ended questions on the material from week 3. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Notebooks as Reports

*Read: [Reports with Jupyter Notebooks](../Week4/reports_with_jupyter_notebooks.md)
and examples 03 and 04 of the
[PyDoit Examples Walkthrough](./doit_examples.md).*

1. The notes say notebooks are for reporting, not for pipeline steps. Give
   two concrete things that go wrong when computation lives in the notebook,
   and say how running the notebook as a `doit` task turns it into a build
   target.
2. What does `jupyter nbconvert --execute` do? Why run a notebook from the
   command line instead of clicking "Run All"?
3. Why are `.ipynb` files hard to track in Git? What does storing a notebook
   as a `.py` file with Jupytext fix?

## Publishing

*Read: [GitHub Pages and Tearsheets](./github_pages_preview.md), and Agenda
Item 2 of the [week 3 chapter](../overview_w3.md) for ChartBook.*

4. What is GitHub Pages? What kind of site can it host, and what can it not
   run?
5. What is ChartBook, and what does `chartbook build` produce?

## Starting a Project

*Read: [Project Structure](./project_structure.md) and
[Modern Environment Tools: uv and pixi](./uv_and_pixi.md).*

6. What does Cookiecutter do? What does Cruft add on top of it?
7. What is a lockfile? What does `uv.lock` record that a hand-written
   `requirements.txt` does not?
8. `uv` installs from PyPI and `pixi` installs from conda-forge. When would
   that difference make you choose `pixi`?
9. `uv run` and `pixi run` both run a command inside the project's
   environment. What does `doit` do that they do not?

## LaTeX

*Read: [Introduction to LaTeX](../Week4/intro_to_LaTeX.md) and
[LaTeX Essentials](../Week4/latex_essentials.md).*

10. What is LaTeX? How is writing in it different from writing in Word?
11. Why is LaTeX a good fit for a report that a pipeline regenerates?
12. In the document below, where does `example_table.tex` come from, and what
    happens to the PDF when the data changes?

    ```latex
    \begin{table}
    \centering
    \input{example_table.tex}
    \end{table}
    ```

13. What is the difference between the `tabular` and `table` environments?
14. What do `\includegraphics` and `\input` each bring into a document?

## Issues and Pull Requests

*Read: [GitHub Issues and Pull Requests](../Week6/GitHub_pull_requests.md).*

15. What is a pull request? What is the difference between pushing a commit
    to `main` and opening a pull request?
16. `git pull` and a pull request sound alike. What does each one do?
17. What is a GitHub issue, and what does writing `fixes #123` in a pull
    request do?
18. Why do maintainers protect `main` and require a review before merging?

## The Yield Curve and the Policy Path

*Read: [Replicating the Gürkaynak, Sack, and Wright (2006) Treasury Yield Curve](../notebooks/_02_replicate_GSW2005_ipynb.ipynb)
and [Replicating the CME FedWatch Tool](../notebooks/_02_fedwatch_replication_ipynb.ipynb).*

19. Why does the Treasury yield curve have to be fitted with a model instead
    of read directly off bond prices?
20. Gürkaynak, Sack, and Wright leave some Treasury securities out of the
    fit. Give two kinds they exclude and the reason for each.
21. A 30-Day Fed Funds futures contract trades at 95.50. What average fed
    funds rate does the market expect for that month, and why?
22. What does the CME FedWatch tool report, and what market data is it
    computed from?
