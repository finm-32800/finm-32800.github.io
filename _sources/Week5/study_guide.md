# Study Guide: Week 5

Open-ended questions on the material from week 5. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Modules, Packages, and `if __name__ == "__main__"`

*Read: [Modules, Packages, and `if __name__ == "__main__"`](../notebooks/_01_modules_and_packages_ipynb.ipynb)
and the [`python_imports/` examples](https://github.com/finm-32800/inclass_examples/tree/main/python_imports)
in the in-class repository.*

1. What is the difference between a Python module and a Python package?
2. What does `python greet.py` print? What does a different script print
    when it runs `import greet`?

    ```python
    # greet.py
    print(f"__name__ is {__name__!r}")


    def say_hello():
        print("Hello!")


    if __name__ == "__main__":
        say_hello()
    ```

3. A module has a function that downloads data from a vendor, a function
    that reads the saved copy, and an `if __name__ == "__main__":` block that
    runs the download and saves the result. Why is the download under that
    block rather than at the top level of the file?


## Environments with uv and pixi

*Read: [Modern Environment Tools: uv and pixi](../Week3/uv_and_pixi.md).*

4. `uv run doit` works in a fresh clone with no environment created or
   activated. What does `uv run` do before it runs `doit`, and what two
   files does it read?
5. What is a lockfile? What does `uv.lock` record that a hand-written
   `requirements.txt` does not?
6. `uv` installs from PyPI and `pixi` installs from conda-forge. When would
   that difference make you choose `pixi`?
7. `uv run` and `pixi run` both run a command inside the project's
   environment. What does `doit` do that they do not?


## Live Monitors

*Read: [Building a Live Monitor with GitHub Actions](./live_monitor.md).*

8. A GitHub-hosted runner starts empty on every run. Where can a daily
   monitor keep what it computed yesterday, and why not commit it to `main`?
9. When does a pipeline need to keep state between runs at all? Compare a
   monitor whose source returns its whole history in one request with one
   that needs minutes of downloading for each day of data.
10. The monitor publishes its page as a single orphan commit that every run
   force-pushes to `gh-pages`. What does that keep out of the repository's
   history?
11. The monitor's daily task has `uptodate: [False]`. What does that tell
   `doit`, and why does a daily update need it?
12. What does a workflow run look like when a repository secret it needs is
   missing, and why does nothing get published?


## Options Data on Databento

*Read: [E-mini S&P 500 Options Data from Databento](../notebooks/_01_es_options_data_ipynb.ipynb)
and [Databento](../Week7/databento.md).*

13. Why does a pipeline that needs an option price at every listed strike
    use the exchange's settlement prices rather than daily trade bars?
14. A vendor's volatility surface has no strikes below about 92% of the spot
    at a one-month maturity. What does a one-month probability of a 20% drop,
    computed from that surface, depend on?


## SVIX and the Probability of a Crash

*Read: [Replicating SVIX and the Probability of a Crash](../notebooks/_02_svix_and_crash_probability_ipynb.ipynb).*

15. SVIX and the VIX are both built from the prices of out-of-the-money
    options across strikes. How do they weight the strikes differently?
16. Option prices across strikes reveal the market's risk-neutral
    distribution of the return. What feature of the call price curve gives
    the probability that the return ends below a given strike?
17. Why is the risk-neutral probability of a crash larger than the
    probability that an investor who holds the market assigns to it?
