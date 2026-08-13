# Why "Data Pipelines for Finance"?

This course is named after the thing it teaches you to build. A data pipeline is the path from raw source data to a finished analytical product: extraction, cleaning, transformation, validation, documentation, and publication, automated end to end. Every case study in this course is one. And "data pipeline" is not academic coinage — it is the phrase financial firms themselves use when they hire for this work. The evidence is below, straight from the firms' own postings.

Concretely, this is a hands-on course built around a core set of tools common across financial computing and data science. It examines each element of the analytical pipeline — data extraction and cleaning, exploratory analysis, visualization, modeling, and finally publication and deployment — with the aim of teaching the tools and principles behind reproducible, scalable workflows: build automation, dependency management, unit testing, the command-line environment, Git for version control, and GitHub for team collaboration. These skills are taught through case studies, and each case study doubles as practical experience with a key financial data source: pricing and fundamentals from CRSP and Compustat, macroeconomic series from FRED, corporate bond transactions from FINRA TRACE, and intraday options data from Databento. Prior experience at an intermediate level with Python and the PyData stack is assumed.

## What the course aims to teach

The course draws its two objectives from the definition of data science itself:

1. **The "science" in data science.** Teach a core set of software tools and the principles behind creating modern data-analytic workflows that are reproducible and scalable from end to end. This computational toolkit serves as a technical foundation for whatever subfield of quantitative finance or data science a student goes on to pursue. It is born out of a simple conviction: reproducibility is fundamental to good science.

2. **The "data" in data science.** Give students hands-on experience with the financial data sets used across the industry. Each data set requires domain-specific knowledge to clean and interpret properly, and each poses its own computational challenges to use effectively: FINRA TRACE means working with transaction records at a scale where pandas begins to struggle, and Databento's intraday options feeds mean confronting exchange-specific quirks head on. Each case study is designed to give students the basic knowledge and tools to use these data sets well.

## Why these tools matter

Given the increasing complexity of the computational sciences, the ability to produce readable, reusable, and reproducible code and analyses matters more every year — for researchers who want to produce good science, and for anyone who wants to add value inside an organization. The skills taught in this course are the ones that bridge the gap between financial computing and data science.

A common characterization of data science describes it as the combination of coding skills, mathematics and statistics, and domain expertise (see the figure below). The coding skills in question are colloquially called "hacking skills" precisely because they are rarely taught in a traditional computer science curriculum. A CS degree covers algorithms, operating systems, and programming languages; the practical skills of the computing ecosystem — the command line, shell scripting, data wrangling, version control — are usually left to the individual to pick up alone. (This gap was the motivation for a supplementary course at MIT, ["The Missing Semester of Your CS Education."](https://missing.csail.mit.edu/)) This course provides a structured overview of these tools and demonstrates, by example, how they are used to collect, clean, and analyze financial data, to collaborate within a team of researchers, and to create analyses that outside users can easily reproduce.

```{figure} ./assets/data_science_venn_diagram.png
:width: 55%
:alt: The Data Science Venn Diagram by Drew Conway, showing data science at the intersection of hacking skills, math and statistics knowledge, and substantive expertise

"The Data Science Venn Diagram," by Drew Conway. The "hacking skills" circle is this course's home territory.
```

Don't take my word for it. Within academic economics, here is how Matthew Gentzkow and Jesse Shapiro open their practitioner's guide, *Code and Data for the Social Sciences*:[^gentzkow]

> Though we all write code for a living, few of the economists, political scientists, psychologists, sociologists, or other empirical researchers we know have any formal training in computer science. Most of them picked up the basics of programming without much effort, and have never given it much thought since. Saying they should spend more time thinking about the way they write code would be like telling a novelist that she should spend more time thinking about how best to use Microsoft Word. Sure, there are people who take whole courses in how to change fonts or do mail merge, but anyone moderately clever just opens the thing up and figures out how it works along the way. This manual began with a growing sense that our own version of this self-taught seat-of-the-pants approach to computing was hitting its limits...
>
> Here is a good rule of thumb: If you are trying to solve a problem, and there are multi-billion dollar firms whose entire business model depends on solving the same problem, and there are whole courses at your university devoted to how to solve that problem, you might want to figure out what the experts do and see if you can't learn something from it.

Within official statistics, consider this statement by the UK's Office for Statistics Regulation:[^osr]

> In 2017 we championed the Reproducible Analytical Pipeline (RAP), a new way of producing official statistics... This approach involved using programming languages to automate manual processes, version control software to robustly manage code and code storage platforms to collaborate, facilitate peer review and publish analysis... We consider that RAP principles support all three pillars of the Code of Practice for Statistics: trustworthiness, quality and value. Trustworthiness, by increasing transparency; quality, by reducing the risk of manual errors; and value, by enabling analytical time to be spent adding value for users rather than on menial, repetitive tasks. It is for all of these reasons that we are so passionate about promoting the use of RAP.

And within the finance industry:[^efc]

> We have been in financial data science long enough, when it was called quantitative finance, to see some clear patterns that we believe are detrimental to the career of a financial data scientist: lack of fundamental core knowledge in computer science, lack of SQL knowledge, ... lack of visualization abilities to share business insights... Technical knowledge is, in itself, not what's most important; it's an understanding of technical functions within a production environment that truly prove a candidate's worth... Data scientists can be super smart, but if no lines of their code will go into production, it's a waste of talent.

## The jobs this course targets

The experts above make the case in principle; hiring desks make it in practice. Consider three current postings from some of the most selective firms in quantitative finance. The highlighted skills — pipelines that ingest and transform data, SQL and DataFrame libraries, Python on Linux, automated data-quality checks — are the skills this course teaches.

```{figure} ./assets/job_posting_citadel_securities_focus.png
:width: 90%
:alt: Citadel Securities' Senior Research Engineer (Data) posting, showing the company logo, the responsibilities, and the skills and qualifications

**Citadel Securities — Senior Research Engineer (Data), Miami.** "Design, create, automate, and maintain custom data pipelines"; "Experience with ETL dev"; "proficiency in Python, SQL DBs, Cloud, schedulers, containers, CI/CD, software packaging"; "Data Build Tool (DBT)." ([full archived posting](./job_posting_citadel_securities.md))
```

```{figure} ./assets/job_posting_jane_street_focus.png
:width: 90%
:alt: Jane Street's Data Engineer posting, showing the company logo and the About the Position and About You sections

**Jane Street — Data Engineer.** "...build pipelines that ingest and transform external data"; "Proficient with SQL or DataFrame libraries like pandas or Polars." ([full archived posting](./job_posting_jane_street.md))
```

```{figure} ./assets/job_posting_hudson_river_trading_focus.png
:width: 90%
:alt: Hudson River Trading's Data Production Engineer posting, showing the company logo, responsibilities, and qualifications

**Hudson River Trading — Data Production Engineer.** "...automate tasks using a modern Python data stack"; "Perform data reconciliations, validations, and quality checks"; "Experience managing ETL pipelines is a plus"; "Experienced in at least one SQL dialect." ([full archived posting](./job_posting_hudson_river_trading.md))
```

These three are not outliers. The same language appears at Jump Trading (whose Chicago campus internship asks for "data pipelining and database management"), IMC, DRW, Point72's Cubist Systematic Strategies, Akuna Capital, AQR, and JPMorganChase — all [archived here](./job_postings.md), and all quoted throughout the week-by-week breakdown below.

## A name that translates into job skills

A course title does work beyond the classroom. It shows up on a student's transcript and résumé, and a recruiter reading it should be able to tell, without any explanation, what the student learned. "Data pipelines" passes that test: it is one of the most common phrases in postings for data roles at trading firms, hedge funds, asset managers, and banks, and this course targets, quite deliberately, the skills those postings request.

Within the program's Core Programming sequence, the name also marks a clear division of labor with *Python for Financial Data Science*. That course is the place to build fluency in the Python language itself along with the core data-science toolkit. This course assumes you already have intermediate Python and concentrates on the layer around the analysis: the data sources, the automation, the testing, the packaging, and the deployment that turn analysis code into a maintained, reproducible product. In short: that course teaches you Python for data science; this one teaches you the pipeline around the data.

## The pipeline is the spine of the course

The pipeline is not a theme grafted onto the course; it is its organizing spine. One of the first readings of the quarter is on [reproducible analytical pipelines](../Week1/reproducible_analytical_pipelines.md). Week 3 is titled ["Automating the Pipeline with PyDoit"](../overview_w3.md). Every case study in the course is structured the same way: pull raw data from a real source (WRDS, FRED, Databento), clean and transform it in tested, documented Python, and publish an output that regenerates from scratch with a single command. That is a data pipeline.

For a fuller statement of what the course teaches and why, see [What is this course about?](../Week1/what_is_this_course_about.md) and the [syllabus](../README.md).

## The material, week by week — in the words of the job postings

In August 2026 I collected postings for data roles at ten financial firms: Jane Street, Citadel Securities, IMC Trading, DRW, Point72's Cubist Systematic Strategies, Jump Trading, Hudson River Trading, Akuna Capital, AQR, and JPMorganChase. This course is designed to target the skills these postings request. Below is the outline of the course, week by week. Each week begins with a short description of the tools and why they matter, followed by what the postings say — quoted verbatim, cited footnote-style. A single sentence in a posting often spans several of the course's topics, so some quotes appear under more than one week. Every footnote links to a [full archived copy](./job_postings.md) of the posting.

### Week 1: Git, GitHub, and virtual environments

Git is the version control system used essentially everywhere in industry: it records every change ever made to a codebase, lets a team work on the same code without overwriting each other, and makes any past version recoverable. GitHub is the platform where those repositories live and where teammates review each other's changes. A virtual environment (we use conda) pins the exact package versions a project depends on, so an analysis runs identically on every machine. This is the vocabulary behind the postings' phrases below: "GitOps" means driving deployments entirely through Git, and "peer code reviews" happen through the GitHub workflow you will use every week of this course.

- "Demonstrated experience working on an Agile team employing software engineering best practices, such as GitOps and CI/CD, to deliver complex software projects"[^akuna]
- "Strong command of engineering best practices, including code quality, design documentation, peer code reviews, automated testing, and test coverage"[^aqr]
- "you write clear, correct, and maintainable code"[^janestreet]

### Week 2: Financial data sources and SQL — WRDS and CRSP

SQL is the standard language for pulling data out of databases, and pandas and Polars are the Python "DataFrame libraries" for reshaping that data once you have it — when a posting names any of these, it is describing the first mile of a pipeline. We practice on the real thing: WRDS (Wharton Research Data Services), the gateway to the databases used across the industry, starting with CRSP, the standard historical database of US stock prices. Notice that the postings also treat knowledge of the data vendors themselves — who sells which dataset and what its quirks are — as a skill in its own right; the course's case studies (CRSP, Compustat, FINRA TRACE, OptionMetrics, Databento) are chosen to build exactly that.

- "Proficient with SQL or DataFrame libraries like pandas or Polars"[^janestreet]
- "Experienced in at least one SQL dialect (PostgreSQL, MSSQL, MYSQL) and able to use others as needed"[^hrt]
- "An understanding of the financial data vendor landscape and product offerings"[^drw]
- "Experience with financial datasets (e.g. Refinitiv, S&P, Bloomberg) is a big plus"[^hrt]

### Week 3: Automating the pipeline with PyDoit — Fama–French 1993

ETL — extract, transform, load — is the industry's name for the core pattern of data work: pull raw data from its source, clean and reshape it, and store the result where analysis can use it. A build-automation tool (we use PyDoit) chains those steps into a dependency graph of tasks, so the entire pipeline reruns in the correct order with a single command and skips any step whose inputs haven't changed. Our case study rebuilds the Fama–French (1993) portfolios from raw CRSP and Compustat data this way. When Citadel asks for "ETL dev" and DRW asks for "ingestion pipelines," this pattern — not any single tool — is what they mean.

- "Design, create, automate, and maintain custom data pipelines"[^citadel]
- "build pipelines that ingest and transform external data"[^janestreet]
- "Improve data ETL pipeline and build tools to analyze new data efficiently."[^point72]
- "3+ years of demonstrated experience designing and implementing ingestion pipelines"[^drw]
- "Experience with ETL dev"[^citadel]

### Week 4: ChartBook, GitHub Pages, and reproducible reports

A pipeline's output has to land somewhere people can actually see it. GitHub Pages publishes a website directly from a repository, and ChartBook organizes a project's charts and tables into a browsable, always-current catalog — this is what the postings call "dashboards" and "analytical infrastructure." The habit this week builds is treating a report not as a one-off document but as a product the pipeline regenerates automatically every time the data updates.

- "Setup analytical infrastructure to facilitate exploration and visualization of datasets"[^citadel]
- "design and run scalable platforms and pipelines, define knowledge architecture, build intuitive dashboards, and help ensure strong governance, privacy, and data quality standards"[^jpmc]
- "Develop dashboards and reporting solutions that enable leaders to make better, faster decisions."[^jpmc]

### Week 5: Reports, LaTeX, and collaboration through pull requests

LaTeX is the typesetting system behind professional-quality quantitative reports and nearly every academic paper in finance. A pull request is how real teams change code: you propose an edit, a teammate reads it line by line and comments, and only after review does it merge — that is the "peer code review" in AQR's posting. The postings below make a point worth taking seriously: firms ask for communication skills in the same breath as technical ones, because a pipeline's results only matter if the researcher, trader, or client on the other end understands them.

- "Clear written and verbal communication skills to translate complex technical work for business stakeholders and collaborate in agile, cross functional teams."[^jpmc]
- "Strong ability to communicate with other stakeholders (e.g., data vendors, QRs, etc.)"[^citadel]
- "A clear and concise communicator"[^janestreet]
- "Strong command of engineering best practices, including code quality, design documentation, peer code reviews, automated testing, and test coverage"[^aqr]

### Week 6: Python packages

A Python package is code organized for reuse and installation — the difference between a folder of scripts only you can run and a library a teammate can `pip install` and build on. Packaging forces the habits employers list under "software packaging": a clean project layout, explicitly declared dependencies, and versioned releases. It is also the step that turns your coursework into something you can hand a hiring manager.

- "Strong coding skills: proficiency in Python, SQL DBs, Cloud, schedulers, containers, CI/CD, software packaging"[^citadel]
- "gain experience with our full-cycle process for development, testing, and release"[^jump]
- "Produce clean, well-tested, and documented code with a clear design to support mission critical applications"[^akuna]

### Week 7: Unit tests and documentation with Sphinx

A unit test is a small program that checks your code automatically: for a data pipeline, tests answer questions like *did every date parse? are there duplicate tickers? do the portfolio weights sum to one?* This is precisely what the postings call "data checks," "data validation," and "data quality control" — the difference between noticing bad data yourself and having a customer notice it for you. We write tests with pytest and generate browsable documentation from the code itself with Sphinx, the same tool that builds the documentation for pandas and this very website.

- "Setup 'data checks' and alerts to determine when the data is 'bad'"[^citadel]
- "Proven expertise in developing data quality control processes to detect gaps or inaccuracies"[^drw]
- "Build automated data validation test suites that ensure that data is processed and published in accordance with well-defined Service Level Agreements (SLA's) pertaining to data quality, data availability and data correctness"[^akuna]
- "Perform data reconciliations, validations, and quality checks"[^hrt]
- "Strong command of engineering best practices, including code quality, design documentation, peer code reviews, automated testing, and test coverage"[^aqr]
- "Produce clean, well-tested, and documented code with a clear design to support mission critical applications"[^akuna]

### Week 8: GitHub Actions and publishing

CI/CD stands for continuous integration and continuous deployment: every time code is pushed, an automated service checks out the change, runs the full test suite, and — if everything passes — rebuilds and republishes the output, with no human in the loop. GitHub Actions is GitHub's built-in CI/CD service, and it is what we use to run each case study's tests and publish its results automatically. When the postings below say "CI/CD" — and notice how many of them do — this machinery is exactly what they mean.

- "Strong coding skills: proficiency in Python, SQL DBs, Cloud, schedulers, containers, CI/CD, software packaging"[^citadel]
- "Demonstrated experience working on an Agile team employing software engineering best practices, such as GitOps and CI/CD, to deliver complex software projects"[^akuna]
- "Ability to work with a team in a fast-paced environment, deploying new software daily"[^jump]
- "Build, deploy, and monitor our data processing pipelines (Java, Python, Spark, Flink)"[^imc]
- "they engineer data, build and deploy models, and monitor them in production"[^jpmc]

### Week 9: Medium-sized data and remote machines

When data outgrows a laptop, the toolkit changes: Polars is a modern DataFrame library built for speed, Parquet is a compressed columnar file format designed for analytics, and the computation moves to remote Linux servers that you operate entirely from the command line — which is why so many postings insist on Linux and "Unix scripting." We practice on FINRA TRACE, the regulatory feed of corporate bond transactions, at a scale where these tools genuinely matter, and we schedule recurring jobs with cron, the classic Unix scheduler behind the "schedulers" in Citadel's list.

- "Comfortable with the Linux command line"[^hrt]
- "Unix scripting experience (bash, python, etc.)"[^imc]
- "Familiarity with the Linux environment."[^point72]
- "Strong understanding of financial point-in-time and time-series data and analysis"[^drw]
- "Strong coding skills: proficiency in Python, SQL DBs, Cloud, schedulers, containers, CI/CD, software packaging"[^citadel]

### What the course does not claim

The course does not cover everything in these postings. Kafka, Spark, Kubernetes, and warehouse platforms like Snowflake and Databricks appear in several and are beyond our scope, though the course's progression (pandas, then Polars and Parquet, then remote machines) is deliberately pointed in that direction. The postings also reflect how fast expectations move — Jump Trading's internship posting lists "Comfortability with Claude Code"[^jump] among its required skills. What the course does claim is the layer beneath all of it: the habits of building pipelines that are versioned, tested, documented, automated, and reproducible. Those habits are what every posting above is asking for.

## The archived postings

Each posting quoted in this chapter is preserved in full — text, screenshot, PDF snapshot, and Wayback Machine link — because postings disappear once roles are filled. See [Archived Job Postings](./job_postings.md).

[^gentzkow]: Matthew Gentzkow and Jesse M. Shapiro, *Code and Data for the Social Sciences: A Practitioner's Guide*, 2014. [web.stanford.edu/~gentzkow/research/CodeAndData.pdf](https://web.stanford.edu/~gentzkow/research/CodeAndData.pdf)
[^osr]: Office for Statistics Regulation, *Reproducible Analytical Pipelines: Overcoming barriers to adoption*, 2021. [Full report](https://osr.statisticsauthority.gov.uk/wp-content/uploads/2021/03/Reproducible-Analytical-Pipelines-Overcoming-barriers-to-adoption.pdf).
[^efc]: ETNA Research, ["The ultimate guide for financial data science"](https://etnaresearch.notion.site/The-ultimate-guide-for-financial-data-science-931c693a3ff1420688aafbf91ebd1828); the closing line is from eFinancialCareers, ["Data science jobs in finance: 'super smart' people wasting talent"](https://www.efinancialcareers.com/news/2023/07/data-science-jobs-in-finance) (2023).
[^janestreet]: Jane Street, *Data Engineer* (London), collected August 10, 2026. [Full archived copy](./job_posting_jane_street.md).
[^citadel]: Citadel Securities, *Senior Research Engineer (Data)* (Miami), collected August 10, 2026. [Full archived copy](./job_posting_citadel_securities.md).
[^imc]: IMC Trading, *Data Engineer* (Chicago), collected August 10, 2026. [Full archived copy](./job_posting_imc.md).
[^drw]: DRW, *Data Engineer, Cumberland/FICCO* (Chicago), collected August 10, 2026. [Full archived copy](./job_posting_drw.md).
[^point72]: Point72, Cubist Systematic Strategies, *Data Engineer* (New York), collected August 10, 2026. [Full archived copy](./job_posting_point72.md).
[^jump]: Jump Trading, *Campus Data Engineer (Intern)* (Chicago), collected August 10, 2026. [Full archived copy](./job_posting_jump_trading.md).
[^hrt]: Hudson River Trading, *Data Production Engineer* (London, New York, Singapore), collected August 10, 2026. [Full archived copy](./job_posting_hudson_river_trading.md).
[^akuna]: Akuna Capital, *Software Engineer — Data Engineering* (Chicago), collected August 10, 2026. [Full archived copy](./job_posting_akuna_capital.md).
[^aqr]: AQR Capital Management, *Portfolio Analytics Engineer — Vice President* (Greenwich, CT), collected August 10, 2026. [Full archived copy](./job_posting_aqr.md).
[^jpmc]: JPMorganChase, *Data & AI Internship Program*, collected August 10, 2026. [Full archived copy](./job_posting_jpmorganchase.md).
