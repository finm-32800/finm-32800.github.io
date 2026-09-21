# Data Pipelines for Quantitative Research

**By Jeremy Bejarano**

Last updated: {sub-ref}`today` 


This set of notes is designed to accompany _FINM 32800: Data Pipelines for Quantitative Research_. 

**Course Description**
*Data Pipelines for Quantitative Research* is a hands-on course centered on building reproducible analytical pipelines: automated and fully reproducible workflows that carry a quantitative analysis from raw data to published result. This course examines every stage of the pipeline, from data extraction and cleaning (extract, transform, load, or ETL), through data validation, exploratory analysis, visualization, modeling, and finally to publication and deployment. In industry, this work is sometimes called *productionizing* research, pairing developers with researchers to translate ideas into practice. The course teaches the core set of tools used to build such pipelines, tools which are common across computing and data science: build automation and CI/CD, dependency management, SQL, unit testing and automated data-quality checks, the Linux command line, SSH and working with remote machines, Git for version control, peer review through GitHub pull requests, and experiment tracking and model monitoring (basic MLOps).

These skills are taught through a series of case studies, each of which introduces students to a new set of tools and a new key financial data set: pricing and fundamentals from CRSP and Compustat, options data from OptionMetrics, corporate bond transactions from FINRA TRACE, intraday trades and quotes from NYSE TAQ, and order book data from CME Globex.

*Prior experience at an intermediate level with Python and the PyData stack is assumed.*


## Table of Contents

```{toctree}
:maxdepth: 1
README.md
Appendix/why_data_pipelines_for_quantitative_research_toc.md
acknowledgments.md
appendix.md
```

```{toctree}
:maxdepth: 1
:caption: Lectures 📖
overview_w0.md
overview_w1.md
overview_w2.md
overview_w3.md
overview_w4.md
overview_w5.md
overview_w6.md
overview_w7.md
overview_w8.md
overview_w9.md
<!-- subject_to_change_after_this_week.md -->
```

```{toctree}
:maxdepth: 1
:caption: Homework 📝
HW0.md
HW1.md
HW2.md
HW3.md
HW4.md
HW5.md
midterm_report.md
final_project.md
```


<!-- 
# Table of contents
# Learn more at https://jupyterbook.org/customize/toc.html

format: jb-book
root: intro.md
chapters:
- file: README.md # This is the syllabus. It is designed this way 
# so that it can be viewed on GitHub as well.
- file: lectures/Week1/HW0.md
# ----------------------------
# Week 1: GitHub, GitHub Classroom, and Virtual Environments
# Data: IPUMS CPS
# HW: Wage growth during the recession
# TA Session: Setting up VS Code for HW
- file: lectures/Week1/overview_w1.md
  sections:
  - file: lectures/Week1/what_is_this_course_about.md
  - file: lectures/Week1/reproducible_analytical_pipelines.md
  - file: lectures/Week1/case_study_reproducibility_in_finance.md
  - file: lectures/Week1/virtual_environments.md
- file: lectures/Week1/HW1.md
  sections:
  - file: lectures/Week1/case_study_atlanta_fed_wage_growth_tracker.md
  - file: output/_01_wage_growth_during_the_recession.ipynb
# ----------------------------
# Week 2: Env Files, and Secrets
# Data: CRSP and yfinance Data
# Handling Jupyter Notebooks in PyDoit
# CRSP for historical data, yfinance for real-time data
# Tear sheet for individual stocks
# CRSP Market Portfolio and HW constructs SP 500 index
# TA Session: Pull requests on GitHub. GitHub skills page.
- file: lectures/Week2/overview_w2.md
  sections:
  - file: lectures/Week2/WRDS_intro_and_web_queries.md
  - file: output/_01_wrds_python_package.ipynb
  - file: lectures/Week2/env_files.md
- file: lectures/Week2/HW2.md
  sections:
  - file: output/_02_CRSP_market_index.ipynb
  - file: output/_03_SP500_constituents_and_index.ipynb
# ----------------------------
# Week 3: Task Runners, Automating Queries and the Basics of SQL
# Data: CRSP
# Pushing large computations to the cloud
# TA Session: No TA session because of the holiday. Lecture uses TA session as makeup day.
- file: lectures/Week3/overview_w3.md
  sections:
    - file: lectures/Week3/what_is_a_task_runner.md
    - file: lectures/Week2/project_structure.md
    - file: output/_05_basics_of_SQL.ipynb
- file: lectures/Week3/HW3.md
  sections:
  - file: output/_04_Fama_French_1993.ipynb
# ----------------------------
# Week 4: Publishing Reports: Markdown, LaTeX, and Github Pages
# Data: CRSP and Compustat Data, Merged
# Data: Datastream??
# Publishing charts to ChartBook. Individual pipeline page. Publishing to GitHub Pages
# Everybody contributes their own new chart to the ChartBook. Each student must contribute
# a unique chart to the ChartBook. Use GitHub Issue tracker to claim a chart BEFORE
# starting work on it. The issue must be approved by the instructor before you can start.
# Then, the repo that collects charts simply has a URL that points to the student's GitHub
# repo that contains the pipeline.
# TA Session: LaTeX essentials
- file: lectures/Week4/overview_w4.md
  sections:
  - file: lectures/Week4/reports_with_jupyter_notebooks.md
  - file: lectures/Week4/intro_to_LaTeX.md
  - file: lectures/Week4/latex_essentials.md
# ----------------------------
# Week 5: Medium Data
# Python Polars Package: Lazy evaluation, parallel execution, and streaming
# Data: Corporate TRACE Data
- file: lectures/Week5/overview_w5.md
  sections:
  - file: lectures/Week5/sphinx.md
  - file: lectures/Week5/unit_tests.md
# ----------------------------
# Week 6: Creating your own Python Package, plus CI/CD and GitHub Actions
# Hatch and Hatchling, Sphinx, Click CLI
# Yield Curve Estimation
# CRSP Treasury Data
- file: lectures/Week6/overview_w6.md
  sections:
  - file: lectures/Week6/GitHub_pull_requests.md
  - file: lectures/Week6/python_packaging_with_hatch.md
- file: lectures/Week6/HW4.md
  sections:
  - file: output/01_CRSP_treasury_overview_ipynb.ipynb
  - file: output/02_replicate_GSW2005_ipynb.ipynb
# ----------------------------
# Week 7: Working with Remotes, HPC Scheduler, GNU Parallel
# SSH, SCP, SFTP
# Data: SEC Filings
# Jupyter Notebooks running remotely, port forwarding. Connect traditional way and via VS Code
# Try Remote Tunnels: https://code.visualstudio.com/docs/remote/tunnels and https://www.youtube.com/watch?v=SyLHXdXhE1U
# TA Session: Bloomberg Terminal
- file: lectures/Week7/overview_w7.md
  sections:
  - file: lectures/Week7/bloomberg_terminal.md
  - file: lectures/Week7/LSEG_datastream.md
- file: lectures/subject_to_change_after_this_week.md
# ----------------------------
# Week 8: Big Data
# Data: NYSE Trade and Quote Data (TAQ)
# NBBO Calculation
# TA Session: SAS
- file: lectures/Week8/overview_w8.md
  sections:
  - file: lectures/Week8/github_actions_interactive_dashboard.md
  - file: output/_01_repo_spikes.ipynb
  - file: output/_corporate_hedging.ipynb
  - file: output/_spx_hedging.ipynb
- file: lectures/Misc/final_project.md
  sections:
  - file: lectures/Misc/final_project_rubric.md
  - file: lectures/Misc/potential_final_projects.md
- file: lectures/Misc/appendix.md
# ----------------------------
# Week 9: Publishing a live dashboard to the web
# IPUMS CPS Data  -->
