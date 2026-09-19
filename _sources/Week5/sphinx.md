# Sphinx

Sphinx is the standard documentation generator for the Python ecosystem. It transforms plain-text source files into polished HTML websites, PDF documents, and other formats. This course uses base Sphinx with a small set of extensions—MyST Parser for Markdown support, autodoc2 for automatic API documentation, and the sphinx-book-theme for a clean, modern look. Using base Sphinx (rather than a higher-level wrapper like Jupyter Book) gives you full control over your documentation setup and a deeper understanding of how the pieces fit together.

```{admonition} In-Class Examples
:class: tip
The examples discussed on this page are available in the `inclass_examples` repository: https://github.com/finm-32800/inclass_examples/tree/main/sphinx

Clone the repository and navigate to the `sphinx/` directory to follow along.
```

## Why Sphinx?

Sphinx was originally created to generate the official Python language documentation and has since become the de facto standard across the Python ecosystem. Projects like NumPy, pandas, scikit-learn, and statsmodels all use Sphinx for their documentation.

- **Extensible**: A rich plugin ecosystem lets you add Markdown support, automatic API docs, notebook rendering, and more.
- **Multi-format output**: Generate HTML, PDF (via LaTeX), ePub, and man pages from a single source.
- **Cross-referencing**: Link between documents, sections, functions, and classes with automatic resolution.
- **Indexing and search**: Built-in full-text search for your documentation website.


## Getting Started: `sphinx-quickstart`

**See Example [`01_quickstart/`](https://github.com/finm-32800/inclass_examples/tree/main/sphinx/01_quickstart)**

### Installation

```bash
pip install sphinx
```

### Running `sphinx-quickstart`

`sphinx-quickstart` is an interactive wizard that creates the scaffolding for a new Sphinx project:

```bash
mkdir my_sphinx_project
cd my_sphinx_project
sphinx-quickstart
```

When prompted, you can use these responses:

| Prompt | Suggested Response |
|--------|--------------------|
| Separate source and build directories | `n` (keep it simple) |
| Project name | `My First Docs` |
| Author name(s) | Your name |
| Project release | `0.1` |
| Project language | `en` (press Enter for default) |

### Files Created

The wizard generates the following structure:

```
my_sphinx_project/
├── _build/          # Generated HTML output (after building)
├── _static/         # Custom CSS, images, etc.
├── _templates/      # Custom Jinja2 templates
├── conf.py          # Sphinx configuration
├── index.rst        # Root document with toctree
├── Makefile         # Build shortcut (Unix)
└── make.bat         # Build shortcut (Windows)
```

### Building the Documentation

```bash
make html
```

Or equivalently:

```bash
sphinx-build -b html . _build/html
```

Then open the result in your browser:

```bash
open _build/html/index.html
```

The generated site uses the **alabaster** theme, which is Sphinx's default.


## The `conf.py` Configuration File

`conf.py` is the central configuration file for every Sphinx project. Almost all customization happens here. The key settings include:

- **Project metadata**: `project`, `author`, `release`
- **`extensions`**: A list of Sphinx plugins to load. This is how you add Markdown support, automatic API docs, and other features.
- **`html_theme`**: Controls the visual appearance of the generated site.
- **`templates_path`** and **`exclude_patterns`**: Control which directories hold custom templates and which files to skip during the build.

A minimal `conf.py` from `sphinx-quickstart` looks like this:

```python
project = "My First Docs"
copyright = "2025, Student Name"
author = "Student Name"
release = "0.1"

extensions = []

html_theme = "alabaster"
```

The sections below show how to extend this configuration with additional extensions and a different theme.


## Writing in Markdown with MyST

**See Example [`02_autodoc2_myst/`](https://github.com/finm-32800/inclass_examples/tree/main/sphinx/02_autodoc2_myst)**

By default, Sphinx uses reStructuredText (reST) as its markup language. reST is powerful, but its syntax is unfamiliar to most people who already know Markdown. [MyST (Markedly Structured Text)](https://myst-parser.readthedocs.io/) solves this by letting you write Sphinx documentation in Markdown while retaining access to all of Sphinx's directives and cross-referencing features.

### Enabling MyST

Add `myst_parser` to the `extensions` list in `conf.py`:

```python
extensions = [
    "myst_parser",
]
```

Install it with:

```bash
pip install myst-parser
```

### Syntax Comparison

Sphinx directives look different in reST and MyST. Here is a table-of-contents directive in both formats:

**reStructuredText** (`index.rst`):
```rst
.. toctree::
   :maxdepth: 2

   api
```

**MyST Markdown** (`index.md`):
````markdown
```{toctree}
:maxdepth: 2

api
```
````

MyST uses a fenced-code block with curly braces around the directive name. The key idea: any Sphinx directive you find in reST documentation can be written in MyST using this syntax.

### When to Use reST vs. MyST

Both formats are fully supported by Sphinx—you can even mix `.rst` and `.md` files in the same project. MyST is recommended for new projects and for teams that are already comfortable with Markdown. That said, many existing Sphinx projects use reST, so being able to read both is valuable.


## Automatic API Documentation with `autodoc2`

**See Example [`02_autodoc2_myst/`](https://github.com/finm-32800/inclass_examples/tree/main/sphinx/02_autodoc2_myst)**

One of Sphinx's most powerful features is the ability to automatically generate API reference pages from your Python source code's docstrings. The [autodoc2](https://sphinx-autodoc2.readthedocs.io/) extension handles this.

### Why `autodoc2`?

The older `sphinx.ext.autodoc` extension requires importing your Python code at build time, which can cause problems if your code has dependencies that are not installed in the documentation build environment. `autodoc2` avoids this by statically analyzing your source files without importing them.

### Configuration

Add `autodoc2` to your `extensions` and tell it where to find your Python code:

```python
extensions = [
    "myst_parser",
    "autodoc2",
]

autodoc2_packages = [
    "../sample_module.py",
]
autodoc2_render_plugin = "myst"
```

### Writing Good Docstrings

`autodoc2` works with any docstring format, but NumPy-style docstrings are recommended for readability. Here is an example from the in-class `sample_module.py`:

```python
def present_value(cash_flow, rate, periods):
    """Calculate the present value of a future cash flow.

    Parameters
    ----------
    cash_flow : float
        The future cash flow amount.
    rate : float
        The discount rate per period (e.g., 0.05 for 5%).
    periods : int
        The number of periods until the cash flow is received.

    Returns
    -------
    float
        The present value of the cash flow.

    Examples
    --------
    >>> present_value(100, 0.05, 2)
    90.70294784580498
    """
    return cash_flow / (1 + rate) ** periods
```

### Custom Docstring Parsing

Example `02_autodoc2_myst/` includes a custom `numpy_myst_parser.py` that converts NumPy-style docstrings into structured MyST Markdown for better rendering. This is configured in `conf.py`:

```python
autodoc2_docstring_parser_regexes = [
    (r".*", "numpy_myst_parser"),
]
```

This is an advanced detail, but it demonstrates Sphinx's extensibility—you can customize nearly every aspect of the build process.

### Building

After adding `autodoc2`, the build command generates API pages automatically:

```bash
sphinx-build -b html docs docs/_build/html
```

Navigate to the API Reference page to see the auto-generated documentation.


## Choosing a Theme: `sphinx-book-theme`

### The Default: Alabaster

Sphinx ships with the `alabaster` theme. It is functional but minimal—fine for getting started, but most projects switch to a more polished theme.

### The `sphinx-book-theme`

The [sphinx-book-theme](https://sphinx-book-theme.readthedocs.io/) provides a clean, modern, book-like layout with a sidebar, navigation, and responsive design. It is the theme used in the course projects and in this textbook.

Install it:

```bash
pip install sphinx-book-theme
```

Then set it in `conf.py`:

```python
html_theme = "sphinx_book_theme"
```

Build both Example `01_quickstart/` (alabaster) and Example `02_autodoc2_myst/` (sphinx-book-theme) to compare the visual difference side by side.

### Other Popular Themes

Several other themes are worth knowing about:

- **`furo`** — A clean, modern theme with excellent search and dark mode support.
- **`sphinx_rtd_theme`** — The Read the Docs theme, very common in open-source projects.
- **`pydata_sphinx_theme`** — Used by NumPy, pandas, and the PyData ecosystem.


## Integrating Jupyter Notebooks with `myst-nb`

[`myst-nb`](https://myst-nb.readthedocs.io/) is a Sphinx extension that can parse and execute Jupyter Notebooks, rendering them as pages within your documentation. It builds on `myst_parser`, so if you are already using MyST for Markdown support, adding notebook rendering is a natural extension. This is useful when you want to include executed notebook output—plots, tables, computation results—directly in your documentation site.


## Putting It All Together

The recommended documentation stack for this course combines a small set of Sphinx extensions:

| Component | Role |
|-----------|------|
| **Sphinx** | Documentation engine |
| **MyST Parser** | Write in Markdown instead of reST |
| **autodoc2** | Auto-generate API docs from docstrings |
| **sphinx-book-theme** | Clean, book-like visual theme |
| **myst-nb** (optional) | Render Jupyter Notebooks as doc pages |

The `requirements.txt` from Example `02_autodoc2_myst/` captures these dependencies:

```
sphinx>=7.3.7
sphinx-autodoc2
myst-parser>=2.0.0
sphinx-book-theme>=1.1.3
```

This stack gives you the flexibility and power of base Sphinx while achieving a polished, book-like result with full control over every extension and configuration option.


## Further Reading

- Sphinx Documentation: [https://www.sphinx-doc.org/en/master/](https://www.sphinx-doc.org/en/master/)
- MyST Parser Documentation: [https://myst-parser.readthedocs.io/](https://myst-parser.readthedocs.io/)
- autodoc2 Documentation: [https://sphinx-autodoc2.readthedocs.io/](https://sphinx-autodoc2.readthedocs.io/)
- sphinx-book-theme Documentation: [https://sphinx-book-theme.readthedocs.io/](https://sphinx-book-theme.readthedocs.io/)
- MyST-NB Documentation: [https://myst-nb.readthedocs.io/](https://myst-nb.readthedocs.io/)
- In-Class Sphinx Examples: [https://github.com/finm-32800/inclass_examples/tree/main/sphinx](https://github.com/finm-32800/inclass_examples/tree/main/sphinx)


## Appendix: Jupyter Book

[Jupyter Book](https://jupyterbook.org/) is an open-source tool that can be described as an "opinionated distribution" of Sphinx. It bundles Sphinx with a curated set of extensions—including MyST Parser, `myst-nb`, and the sphinx-book-theme—and provides a simplified command-line interface for creating and building book-like documentation from Jupyter Notebooks and Markdown files.

### Why This Course Uses Base Sphinx Instead

Jupyter Book is convenient for getting started quickly, but it makes many configuration decisions for you. This course teaches base Sphinx so that you understand the underlying tool, can select exactly the extensions you need, and are not locked into decisions made by a wrapper. As you have seen in the sections above, the course achieves the same result—Markdown, auto-API docs, book theme—by choosing individual extensions.

### Quick Reference

If you encounter Jupyter Book in other projects, here are the basic commands:

```bash
pip install jupyter-book
jupyter-book create mybookname
jupyter-book build mybookname/
```

### Further Reading

- Jupyter Book Documentation: [https://jupyterbook.org/](https://jupyterbook.org/)
