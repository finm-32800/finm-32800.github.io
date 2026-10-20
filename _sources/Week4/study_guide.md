# Study Guide: Week 4

Open-ended questions on the material from week 4. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Publishing with GitHub Pages

*Read: [GitHub Pages and Tearsheets](../Week3/github_pages_preview.md) and the
"Key Concepts" in the
[`github_actions/` examples](https://github.com/finm-32800/inclass_examples/tree/main/github_actions).*

1. What does an empty `.nojekyll` file at the root of a GitHub Pages site do?
   What can go wrong without it?

## CI/CD with GitHub Actions

*Read: the [`github_actions/` examples](https://github.com/finm-32800/inclass_examples/tree/main/github_actions)
in the in-class repository,
[The Daily Monitor](../notebooks/_03_daily_monitor_ipynb.ipynb), and
[Scheduling Recurring Tasks with Cron](../Week9/cron_jobs.md).*

2. What happens, step by step, when you push a commit to a repository that
   has the workflow below?

   ```yaml
   on: push
   jobs:
     test:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
         - run: pip install -r requirements.txt
         - run: pytest
   ```

3. A workflow file begins with the lines below. Name the three events that
   start the workflow, and say when each one happens.

   ```yaml
   on:
     push:
       branches: [main]
     schedule:
       - cron: '0 22 * * 0'
     workflow_dispatch: {}
   ```

4. A pipeline that runs on GitHub Actions needs an API key. Where should the
   key be stored, and why not in the workflow file or in a committed `.env`
   file?

## Environments with uv and pixi

*Read: [Modern Environment Tools: uv and pixi](../Week3/uv_and_pixi.md).*

5. `uv run doit` works in a fresh clone with no environment created or
   activated. What does `uv run` do before it runs `doit`, and what two
   files does it read?
6. What is a lockfile? What does `uv.lock` record that a hand-written
   `requirements.txt` does not?
7. `uv` installs from PyPI and `pixi` installs from conda-forge. When would
   that difference make you choose `pixi`?
8. `uv run` and `pixi run` both run a command inside the project's
   environment. What does `doit` do that they do not?

## Modules, Packages, and `if __name__ == "__main__"`

*Read: [Modules, Packages, and `if __name__ == "__main__"`](../notebooks/_01_modules_and_packages_ipynb.ipynb)
and the [`python_imports/` examples](https://github.com/finm-32800/inclass_examples/tree/main/python_imports)
in the in-class repository.*

9. What is the difference between a Python module and a Python package?
10. What does `python greet.py` print? What does a different script print
    when it runs `import greet`?

    ```python
    # greet.py
    print(f"__name__ is {__name__!r}")


    def say_hello():
        print("Hello!")


    if __name__ == "__main__":
        say_hello()
    ```

11. A module has a function that downloads data from a vendor, a function
    that reads the saved copy, and an `if __name__ == "__main__":` block that
    runs the download and saves the result. Why is the download under that
    block rather than at the top level of the file?

## Python Packages and CI

*Read: [Writing and Publishing Your Own Python Packages](../Week6/python_packaging_with_hatch.md),
the installable example in
[Modules, Packages, and `if __name__ == "__main__"`](../notebooks/_01_modules_and_packages_ipynb.ipynb),
and [Semantic Versioning](https://semver.org/).*

12. What does each of the two tables in this `pyproject.toml` declare?

    ```toml
    [build-system]
    requires = ["hatchling"]
    build-backend = "hatchling.build"

    [project]
    name = "myproj"
    version = "0.1.0"
    dependencies = ["pandas"]
    ```

13. Under semantic versioning, what does a change in each of the three parts
    of a version number such as `2.4.1` tell the package's users?
14. What is TestPyPI, and why rehearse a release there before publishing to
    PyPI?
15. A package's test workflow contains the lines below. What does GitHub
    Actions do with them, and what kind of bug do they catch?

    ```yaml
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    ```

## Issues and Pull Requests

*Read: [GitHub Issues and Pull Requests](../Week6/GitHub_pull_requests.md).*

16. What is a pull request? What is the difference between pushing a commit
    to `main` and opening a pull request?
17. `git pull` and a pull request sound alike. What does each one do?
18. What is a GitHub issue, and what does writing `fixes #123` in a pull
    request do?
19. Why do maintainers protect `main` and require a review before merging?

## Options Data on Databento

*Read: [E-mini S&P 500 Options Data from Databento](../notebooks/_01_es_options_data_ipynb.ipynb)
and [Databento](../Week7/databento.md).*

20. Why does a pipeline that needs an option price at every listed strike
    use the exchange's settlement prices rather than daily trade bars?

## SVIX and the Probability of a Crash

*Read: [Replicating SVIX and the Probability of a Crash](../notebooks/_02_svix_and_crash_probability_ipynb.ipynb),
[Crash Bounds for Single Names](../notebooks/_04_single_name_crash_bounds_ipynb.ipynb),
and [Martin and Shi (2025)](https://personal.lse.ac.uk/martiniw/oib_martin_shi_latest.pdf).*

21. SVIX and the VIX are both built from the prices of out-of-the-money
    options across strikes. How do they weight the strikes differently?
22. Option prices across strikes reveal the market's risk-neutral
    distribution of the return. What feature of the call price curve gives
    the probability that the return ends below a given strike?
23. Why is the risk-neutral probability of a crash larger than the
    probability that an investor who holds the market assigns to it?
24. Option prices on a single stock give the risk-neutral distribution of its
    return. Why do Martin and Shi bound the stock's crash probability instead
    of computing a single number?
25. A vendor's volatility surface has no strikes below about 92% of the spot
    at a one-month maturity. What does a one-month probability of a 20% drop,
    computed from that surface, depend on?
