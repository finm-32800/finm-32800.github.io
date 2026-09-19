
# Virtual Environments

When developing software, particularly in data science and quantitative finance, managing dependencies and ensuring consistent environments across different systems are crucial. 
A common tool that you will encounter are Conda environments, often using `environment.yml` files, or sometimes paired with  `pip` and its `requirements.txt` files.
This short note will explain the importance of these tools and files in creating reproducible and reliable software environments.


## Examples: Streamlit Finance Dashboards

These two examples demonstrate examples of end products that might inspire you.
These are very simple examples that are easy to construct, but they are here to give you
ideas for what you might want to build.

Reproducing these example is made easy with the use of software environments.

### Example 1: Streamlit Finance Dashboard

This app is a simple example of using Streamlit to create a financial data web app.
This demo use streamlit, pandas and yfinance modules.

Allows you to select one of the 500 companies that compose the S&P 500 and
display a updated chart of adjusted closing prices, as well as add a pair of
moving averages.

![Sample Animation](assets/streamlit_finance_chart.gif "Sample Animation")


 - Clone the repository located here: https://github.com/jmbejara/streamlit_finance_chart
 - Navigate the command line to the source directory. Or, open the cloned repo in VS Code and then open the VS Code terminal.
 - In the terminal, use the following commands to create and install the associated environment.

```
conda create -n streamlit python=3.12
conda activate streamlit
pip install -r requirements.txt
```
You can then run the dashboard with
```
streamlit run app.py
```

After this, you can remove the conda environment if you like: `conda remove --name streamlit --all`

### Example 2: Another Streamlit Dashboard, except that it requires API keys for data service

FinStockDash is a web application that allows users to analyze stock data using various financial ratios, balance sheet, income statement, and other financial metrics. User can enter a stock ticker symbol to retrieve relevant financial information, view stock prices over time, and generate visualizations to gather insights on the company performance. The application makes use of APIs provided by Financial Modeling Prep and Alpha Vantage to retrieve real-time stock data.


![Demo](assets/web_app_demo.gif)

This example requires API keys for data service. API keys are an example of a **secret**.
Secrets are a type of information that should not be shared with others. In this example, the
secrets are stored in a file called `.env`. We'll learn more about this in today's lecture.

 - Clone the repository located here: https://github.com/jmbejara/FinStockDash
 - Navigate the command line to the source directory. Or, open the cloned repo in VS Code and then open the VS Code terminal.
 - In the terminal, use the following commands to create and install the associated environment.

To run the app locally, follow these steps:

1. Clone the repository: <br>

       $ git clone https://github.com/abeltavares/financial_dashboard_app.git 

2. Create and activate a virtual environment: <br>

       $ python3 -m venv .venv
       $ source .venv/bin/activate

3. Install the required libraries:<br>

       $ pip install -r requirements.txt

4. Create a `.env` file with the proper API keys. Use the `.env.example` file as a template.

5. Run the app:

       $ streamlit run app.py

You can deactivate the environment with
```
deactivate
```
You can delete the virtual environment with
```
rm -rf .venv
```
or on Windows command prompt:
```
rmdir .venv
```
(or `rmdir /S /Q .venv` to remove the directory and all its contents)

## Understanding Dependency Management

Dependencies are external libraries or packages that your project needs to function correctly. In data science, examples include NumPy for numerical operations, pandas for data manipulation, and Matplotlib for plotting.

**Why Manage Dependencies?**

1. **Consistency:** Ensures that all developers and users work with the same versions of libraries, avoiding the "it works on my machine" problem.
2. **Isolation:** Protects system-level Python and prevents conflicts between project dependencies.

## Pip and requirements.txt

<img src="assets/logos/pip_logo.svg" alt="PyPI logo" class="tool-logo pip-logo"/>

`pip` is the standard package manager for Python, allowing for the installation of packages from the Python Package Index (PyPI). A `requirements.txt` file is 

- a text file listing the dependencies needed for a project. 
- Each line in `requirements.txt` represents a package and its version.
- Use `pip install -r requirements.txt` to install all dependencies at once.

For example, these are the contents of the `requirements.txt` file used to generate this textbook:
```
altair==5.1.2
beautifulsoup4==4.12.2
colorama
doit==0.36.0
ipython==8.17.2
jupyter==1.0.0
jupyterlab==4.5.7
jupyter-book==0.15.1
linearmodels==5.3
matplotlib==3.8.1
notebook==7.5.6
numpy==1.26.0
openpyxl==3.1.2
pandas==2.1.2
pandas-datareader==0.10.0
pandas-market-calendars==4.3.1
plotly==5.18.0
plotnine==0.12.4
polars==0.19.12
python-decouple==3.8
python-dotenv==1.0.0
pytest==7.4.3
pyxlsb==1.0.10
quantstats
requests==2.33.0
scikit-learn==1.5.0
scipy==1.11.3
seaborn==0.13.0
statsmodels==0.14.0
streamlit==1.54.0
vega_datasets==0.9.0
weightedstats==0.4.1
wrds==3.1.6
xbbg==0.7.7
xlrd==2.0.1
xlsxwriter
yfinance
zstandard==0.22.0
```

This allows us to install all of the needed packages at once. However, this does not provide **isolation** from other projects. This is where a virtual environment becomes useful.

## What is a virtual environment?

A virtual environment is an isolated workspace that allows you to manage separate sets of dependencies and Python versions for different projects, preventing conflicts and maintaining a clean and manageable development setup. Some examples of software within the Python ecosystem that provide such environments are `venv`, `virtualenv`, or conda environments. Here, I'll focus on conda environments.

## Conda environments

<img src="assets/logos/conda_logo.svg" alt="conda logo" class="tool-logo"/>

`conda` is a package manager that handles Python packages and also binaries for other languages. It is particularly useful in data science due to its ability to manage complex dependencies and environments. A major advantage of `conda`, at least in the past, was its ability to handle dependencies on non-Python packages. `conda` as a package manager can install non-Python dependencies. 

**Conda's `environment.yml` files**

Besides functioning as a package manager to handle dependencies, it also provides virtual environments. An advantage of conda environments is that they can easily manage different versions of Python as well as Python packages. This must be done in a more manual fashion with something like `venv`.

The description of the software environment is defined by the `environment.yml` file. It is

- a YAML file that specifies not just Python packages but also the Python version and other binaries.
- `conda env create -f environment.yml` creates an isolated environment with specified dependencies.
- `conda activate <env_name>` to activate the environment.

The following is an example of an `environment.yml` file:
```
name: finm
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.12
  - altair>=5.1.2
  - beautifulsoup4>=4.12.2
  - doit>=0.36.0
  - ipython>=8.17.2
  - jupyter>=1.0.0
  - jupyterlab>=4.0.11
  - linearmodels>=5.3
  - matplotlib>=3.8.1
  - notebook>=7.0.6
  - numpy>=1.26.0
  - openpyxl>=3.1.2
  - pandas>=2.1.2
  - pandas-datareader>=0.10.0
  - pandas-market-calendars>=4.3.1
  - plotly>=5.18.0
  - plotnine>=0.12.4
  - pytest>=7.4.3
  - python-decouple>=3.8
  - python-dotenv>=1.0.0
  - pyxlsb>=1.0.10
  - requests>=2.31.0
  - scikit-learn>=1.3.2
  - scipy>=1.11.3
  - seaborn>=0.13.0
  - statsmodels>=0.14.0
  - streamlit>=1.28.1
  - vega_datasets>=0.9.0
  - xbbg>=0.7.7
  - xlrd>=2.0.1
  - zstandard>=0.22.0
  - pip
  - pip:
    - polars-lts-cpu # This is for compatibility with older CPUs, like the one the autograder uses.
    - wrds
```


**Benefits of `conda` Environments**

1. **Cross-Language:** Manages dependencies for languages other than Python.
2. **System Independence:** Works across different operating systems.
3. **Environment Cloning:** Allows for easy replication of environments.

### Performance Issues with Conda

For years, `conda` was known for persistent performance issues,
such as slow package installation, dependency resolution taking a long time, and a sluggish environment creation or update process.
This was primarily due to its comprehensive package management system that handles both Python and non-Python packages.
The Anaconda team has since done a lot of work here: recent versions of `conda` ship with a much faster dependency solver by default, which dramatically speeds up environment creation and updates.
For more information, see here:

- https://www.anaconda.com/blog/understanding-and-improving-condas-performance
- https://www.anaconda.com/blog/conda-is-fast-now

## My recommendation
In my experience, it has been easier to work with a combination of `conda` and `pip`. I have found that many others have gone this route as well.
This involves creating a new blank conda environment for each project and then using `pip install -r requirements.txt` *into* the conda environment.

**For this course, use `conda` + `pip` so that everyone's setup matches.** This is what the homework instructions assume, and it keeps us all on the same page when we debug environment issues together.

## A note on newer tools: uv and pixi

The Python tooling landscape is moving fast, and two newer tools are worth knowing about now---even though we'll stick with `conda` + `pip` for the homework in this course. We'll come back to these in a later week and work through hands-on examples.

- <img src="assets/logos/uv_logo.svg" alt="uv logo" class="inline-logo"/>**`uv`** is an extremely fast (often 10--100x faster) drop-in replacement for `pip` and `venv`, written in Rust. The key point for now: it reads the very same `requirements.txt` you already have. For example, `uv pip install -r requirements.txt` does what `pip install -r requirements.txt` does, just much faster. See https://docs.astral.sh/uv/.

- <img src="assets/logos/pixi_logo.png" alt="pixi logo" class="inline-logo"/>**`pixi`** is a modern, `conda`-compatible environment manager (also written in Rust). It pulls packages from conda-forge---like `conda`---but with a faster solver and a lockfile-first workflow that records exact versions for fully reproducible environments. See https://pixi.sh/.

The thing to take away today is that your `requirements.txt` (and, for conda-style packages, your `environment.yml`) is the *portable interchange format*. The same file works whether you install it with `pip`, `uv`, or `conda`. So the habits you build now carry over directly when we explore these newer tools later.

For now, though: **stick with `conda` + `pip`.** Just know that `uv` and `pixi` exist---we'll go deeper on `uv` in particular, with a smaller example of `pixi`, in a later week.

If you'd like a preview, you can see all four approaches---`conda` only, `conda` + `pip`, `uv`, and `pixi`---running the *same* script side by side in the [`software_environments/`](https://github.com/finm-32800/inclass_examples/tree/main/software_environments) directory of the [in-class examples repository](https://github.com/finm-32800/inclass_examples), which we'll use throughout the course.

### Cheat sheet

 - A short tutorial can be found here: https://edcarp.github.io/introduction-to-conda-for-data-scientists/02-working-with-environments/index.html
 - The official `conda` documentation can be found here: https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment

**Working with `pip` requirements**

`conda` allows for a lot of flexibility, but can often be slow. `pip`, however, is fast for what it does.  You can install the requirements for this project using the `requirements.txt` file specified here. Do this with the following command:
```
pip install -r requirements.txt
```

**Working with `conda` environments**

The dependencies used in this environment (along with many other environments commonly used in data science) are stored in the conda environment called `finm` which is saved in the file called `environment.yml`. To create the environment from the file (as a prerequisite to loading the environment), use the following command:

```
conda env create -f environment.yml
```

Now, to load the environment, use

```
conda activate finm
```

Note that an environment file can be created with the following command:

```
conda env export > environment.yml
```

However, it's often preferable to create an environment file manually, as was done with the file in this project.

Also, these dependencies are also saved in `requirements.txt` for those that would rather use pip. Also, GitHub actions work better with pip, so it's nice to also have the dependencies listed here. This file is created with the following command:

```
pip freeze > requirements.txt
```

**Other helpful `conda` commands**

- Create conda environment from file: `conda env create -f environment.yml`
- Activate environment for this project: `conda activate finm`
- Remove conda environment: `conda remove --name finm --all`
- Create blank conda environment: `conda create --name finm --no-default-packages`
- Create blank conda environment with different version of Python: `conda create --name finm --no-default-packages python` Note that the addition of "python" will install the most up-to-date version of Python. Without this, it may use the system version of Python, which will likely have some packages installed already.

