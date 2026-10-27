# Study Guide: Week 8

Open-ended questions on the material from week 8. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Python Packages and CI

*Read: [Writing and Publishing Your Own Python Packages](../Week6/python_packaging_with_hatch.md),
the installable example in
[Modules, Packages, and `if __name__ == "__main__"`](../notebooks/_01_modules_and_packages_ipynb.ipynb),
and [Semantic Versioning](https://semver.org/).*

1. What does each of the two tables in this `pyproject.toml` declare?

    ```toml
    [build-system]
    requires = ["hatchling"]
    build-backend = "hatchling.build"

    [project]
    name = "myproj"
    version = "0.1.0"
    dependencies = ["pandas"]
    ```

2. Under semantic versioning, what does a change in each of the three parts
    of a version number such as `2.4.1` tell the package's users?
3. What is TestPyPI, and why rehearse a release there before publishing to
    PyPI?
4. A package's test workflow contains the lines below. What does GitHub
    Actions do with them, and what kind of bug do they catch?

    ```yaml
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]
    ```
