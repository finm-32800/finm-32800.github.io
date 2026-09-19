# PyDoit Examples Walkthrough

These examples are available in the [in-class examples repository](https://github.com/finm-32800/inclass_examples). Clone the repository and navigate to the [`pydoit/`](https://github.com/finm-32800/inclass_examples/tree/main/pydoit) directory to follow along.

## Quick Start

```bash
pip install doit
cd pydoit/01_simplest
doit
```

## The Examples

### 01_simplest

The most basic PyDoit example: three tasks that run separate Python scripts in sequence (`create_data.py` → `process_data.py` → `create_report.py`). Demonstrates `actions`, `file_dep`, and `targets`. See the README in this directory for details.

### 02_example_project

A realistic data science workflow with proper project structure. Pulls GDP and unemployment data from FRED, processes it, and creates charts. Demonstrates how to organize a project with a `src/` folder and `settings.py`. See the README in this directory for details.

### 03_handling_notebooks

Shows how to handle regular `.ipynb` Jupyter notebooks in a doit workflow. Uses a two-stage approach: convert notebooks to `.py` files for change detection, then execute only when the code (not metadata) changes. This is a clone of `blank_project_simple`. See the README in this directory for details.

### 04_jupytext_notebooks

Demonstrates Jupytext percent-format notebooks (stored as `.py` files) which are version-control friendly. Includes Sphinx documentation generation. This is a copy of `fred_charts`. See the README in this directory for details.

## Learning Progression

| Example | Key Concept |
|---------|-------------|
| 01 | Basic task definitions and dependencies |
| 02 | Project structure and external data |
| 03 | Two-stage notebook workflow |
| 04 | Jupytext and documentation |
