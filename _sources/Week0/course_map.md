# Course Map: Is This the Right Course for You?

## Who this course is for

The program offers three courses that satisfy the computing requirement. They
overlap in tools, but they are aimed at different jobs.

- **This course, Data Pipelines for Quantitative Research,** is built for
  students headed toward quantitative research. The work product is an
  analysis: a replicated result from a finance paper, produced by an automated
  pipeline that anyone can rerun from raw data to finished report.
- **The other two computing courses** are built for students headed toward
  quantitative development: writing and shipping software for trading and
  finance.

If you are unsure, ask yourself which question you would rather answer at work.
"Does this signal survive out of sample, and can I prove how I computed it?"
points here. "How do I make this system fast, correct, and maintainable?" points
to the other two. Many of the tools are the same: Git, testing, automation,
packaging. The difference is what you build with them.

The course is organized around research papers. Each week we replicate a result
from a well-known finance paper, usually with a new data set. The list runs from
Markowitz (1952) and the CAPM, through Fama and French (1993) and the Treasury
yield curve, to corporate bond factors and intraday measures of liquidity. The
{ref}`full schedule <course-map-schedule>` is below.

## Why a research course teaches data pipelines

This is still a computing course. Every student in the program is required to
learn a certain amount of computing, and students headed toward research are no
exception. Quantitative research is done in code and on data, and it depends on
a core set of data science skills: pulling and cleaning data, automating an
analysis from end to end, testing it, and publishing a result that someone else
can rebuild. A result that cannot be rebuilt from its raw data cannot be
trusted, in a journal or on a trading desk.

These skills are also sought after in industry, and the evidence is in the job
postings below. The postings are for research engineers and data engineers, the
people who work alongside quantitative researchers. Read them in two ways. If
you become a quantitative researcher, these are the skills that let you carry
your own idea from raw data to a finished result, and they are the skills of
the people you will work with every day. If you end up in a role next to
research instead, these are the skills you will be hired for.

Consider this excerpt from Citadel Securities' posting for a Senior Research
Engineer (Data).

> "Partner with researchers to produce high-value datasets"
>
> "Translate high-level market research concepts into scalable processes that
> further transform the data"

This is the partnership that the course is built around. Different companies
assign different titles to the engineering side of it. Commonly used titles
include research engineer, data engineer, and quantitative developer on a data
team, but the work is the same: take research that lives in a researcher's head
or in a published paper and turn it into a production data pipeline that runs
reliably every day. Industry's word for this is *productionizing* research. In
this course you sit on both sides of the partnership. You work through the
research in a published paper, and you build the pipeline that reproduces it.

The same skills appear across firms. Consider three recent postings from some
of the most selective firms in quantitative finance. The skills they ask for
are the skills this course teaches: pipelines that ingest and transform data,
SQL and DataFrame libraries, Python on Linux, and automated data-quality
checks.

```{figure} ../Appendix/assets/job_posting_citadel_securities_focus.png
:width: 90%
:alt: Citadel Securities' Senior Research Engineer (Data) posting, showing the company logo, the responsibilities, and the skills and qualifications

**Citadel Securities, Senior Research Engineer (Data), Miami.** ([Full archived posting](../Appendix/job_posting_citadel_securities.md))
```

In Citadel's posting, look for these lines.

- "Partner with researchers to produce high-value datasets"
- "Design, create, automate, and maintain custom data pipelines"
- "Setup 'data checks' and alerts to determine when the data is 'bad'"
- "Experience with ETL dev"
- "Strong coding skills: proficiency in Python, SQL DBs, Cloud, schedulers, containers, CI/CD, software packaging"
- "Data Build Tool (DBT)"

```{figure} ../Appendix/assets/job_posting_jane_street_focus.png
:width: 90%
:alt: Jane Street's Data Engineer posting, showing the company logo and the About the Position and About You sections

**Jane Street, Data Engineer.** ([Full archived posting](../Appendix/job_posting_jane_street.md))
```

In Jane Street's posting, look for these lines.

- "build pipelines that ingest and transform external data"
- "Proficient with SQL or DataFrame libraries like pandas or Polars"

```{figure} ../Appendix/assets/job_posting_hudson_river_trading_focus.png
:width: 90%
:alt: Hudson River Trading's Data Production Engineer posting, showing the company logo, responsibilities, and qualifications

**Hudson River Trading, Data Production Engineer.** ([Full archived posting](../Appendix/job_posting_hudson_river_trading.md))
```

In Hudson River Trading's posting, look for these lines.

- "automate tasks using a modern Python data stack"
- "Perform data reconciliations, validations, and quality checks"
- "Experience managing ETL pipelines is a plus"
- "Experienced in at least one SQL dialect (PostgreSQL, MSSQL, MYSQL) and able to use others as needed"

These three are not outliers. The same language appears at many other firms.
Postings from ten firms in all are archived on this website, each with its
complete text, a screenshot, a PDF snapshot, and a Wayback Machine link. The
{ref}`full list <course-map-archived-postings>` is at the bottom of this page,
and the section
{ref}`What the postings ask for, week by week <course-map-postings-by-week>`
matches their language to the course schedule.

(course-map-schedule)=
## One paper and one tool each week

Every week pairs a tool for building data pipelines with a well-known finance
paper and, usually, a new data set. Replicating the paper gives you repetitions
with the tool. It also gives you a working knowledge of the papers and the data
that quantitative researchers are expected to know.

Replicating a paper also mimics the task described in the job posting above. The
paper plays the role of the researcher. It hands you a high-level research
concept, and you translate it into a scalable, automated process that carries
raw data to a finished, reproducible result.

The plan for this quarter is below. The second half of the schedule may still
change.

| Week | Pipeline tool | Paper | Data |
|---|---|---|---|
| 0 | `requirements.txt`; clone and run | Markowitz (1952), portfolio selection | CRSP extract |
| 1 | Git, GitHub, virtual environments | Sharpe (1964), the CAPM: build the market portfolio and the S&P 500 | CRSP |
| 2 | Task runners (PyDoit) | Fama and French (1993), the three-factor model | CRSP, Compustat |
| 3 | Reproducible reports: notebooks, GitHub Pages, LaTeX | Gürkaynak, Sack, and Wright (2006), the Treasury yield curve | CRSP Treasuries |
| 4 | Python packaging and documentation | Constantinides, Jackwerth, and Savov (2013), index option returns | OptionMetrics, CME Globex |
| 5 | Unit tests and data validation | Dickerson, Robotti, and Rossetti (2026), the corporate bond factor replication crisis: cleaning bond transactions | FINRA TRACE |
| 6 | SQL at scale, remote machines, and HPC | Holden and Jacobsen (2014), measuring liquidity from intraday trades and quotes | NYSE TAQ |
| 7 | The data build tool (dbt) | Ardia, Guidotti, and Kroencke (2024), bid-ask spreads from daily prices, checked against TAQ | NYSE TAQ, CRSP |
| 8 | CI/CD with GitHub Actions | Bernanke and Kuttner (2005), monetary policy surprises from fed funds futures: replicating the CME FedWatch tool | CME fed funds futures |
| 9 | Basic MLOps: monitoring models | Welch and Goyal (2008), predicting the equity premium | All of the above |

The arc on the finance side runs from equities (weeks 0 to 2), to interest
rates and options (weeks 3 and 4), to corporate bonds and market microstructure
(weeks 5 to 7), and ends with a prediction problem that draws on everything
before it.

## The final project

The final project is a replication of a published paper, done in a small group,
and built as a complete pipeline: one command takes it from raw data to a
finished report. Browse the
[past final projects](../FinalProject/past_final_projects.md) to see what
students have built.

(course-map-postings-by-week)=
## What the postings ask for, week by week

Each week's tool was chosen because firms ask for it by name. Below, each week
of the schedule above is matched to what the postings say, quoted verbatim. A
single sentence in a posting often spans several of the course's topics, so
some quotes appear under more than one week. Each firm name links to the full
archived posting.

**Week 1: Git, GitHub, and virtual environments**

- "Demonstrated experience working on an Agile team employing software engineering best practices, such as GitOps and CI/CD, to deliver complex software projects" ([Akuna Capital](../Appendix/job_posting_akuna_capital.md))
- "Strong command of engineering best practices, including code quality, design documentation, peer code reviews, automated testing, and test coverage" ([AQR](../Appendix/job_posting_aqr.md))

**Week 2: Task runners (PyDoit) and ETL**

- "Design, create, automate, and maintain custom data pipelines" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))
- "build pipelines that ingest and transform external data" ([Jane Street](../Appendix/job_posting_jane_street.md))
- "3+ years of demonstrated experience designing and implementing ingestion pipelines" ([DRW](../Appendix/job_posting_drw.md))
- "Improve data ETL pipeline and build tools to analyze new data efficiently." ([Point72](../Appendix/job_posting_point72.md))

**Week 3: Reproducible reports**

- "Setup analytical infrastructure to facilitate exploration and visualization of datasets" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))
- "Clear written and verbal communication skills to translate complex technical work for business stakeholders and collaborate in agile, cross functional teams." ([JPMorganChase](../Appendix/job_posting_jpmorganchase.md))
- "Strong ability to communicate with other stakeholders (e.g., data vendors, QRs, etc.)" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))

**Week 4: Python packaging and documentation**

- "Strong coding skills: proficiency in Python, SQL DBs, Cloud, schedulers, containers, CI/CD, software packaging" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))
- "gain experience with our full-cycle process for development, testing, and release" ([Jump Trading](../Appendix/job_posting_jump_trading.md))
- "Produce clean, well-tested, and documented code with a clear design to support mission critical applications" ([Akuna Capital](../Appendix/job_posting_akuna_capital.md))

**Week 5: Unit tests and data validation**

- "Setup 'data checks' and alerts to determine when the data is 'bad'" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))
- "Proven expertise in developing data quality control processes to detect gaps or inaccuracies" ([DRW](../Appendix/job_posting_drw.md))
- "Perform data reconciliations, validations, and quality checks" ([Hudson River Trading](../Appendix/job_posting_hudson_river_trading.md))
- "Build automated data validation test suites that ensure that data is processed and published in accordance with well-defined Service Level Agreements (SLA's) pertaining to data quality, data availability and data correctness" ([Akuna Capital](../Appendix/job_posting_akuna_capital.md))

**Week 6: SQL at scale, remote machines, and HPC**

- "Proficient with SQL or DataFrame libraries like pandas or Polars" ([Jane Street](../Appendix/job_posting_jane_street.md))
- "Experienced in at least one SQL dialect (PostgreSQL, MSSQL, MYSQL) and able to use others as needed" ([Hudson River Trading](../Appendix/job_posting_hudson_river_trading.md))
- "Comfortable with the Linux command line" ([Hudson River Trading](../Appendix/job_posting_hudson_river_trading.md))
- "Unix scripting experience (bash, python, etc.)" ([IMC Trading](../Appendix/job_posting_imc.md))

**Week 7: The data build tool (dbt)**

- "Data Build Tool (DBT)" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))
- "Translate high-level market research concepts into scalable processes that further transform the data" ([Citadel Securities](../Appendix/job_posting_citadel_securities.md))

**Week 8: CI/CD with GitHub Actions**

- "Demonstrated experience working on an Agile team employing software engineering best practices, such as GitOps and CI/CD, to deliver complex software projects" ([Akuna Capital](../Appendix/job_posting_akuna_capital.md))
- "Ability to work with a team in a fast-paced environment, deploying new software daily" ([Jump Trading](../Appendix/job_posting_jump_trading.md))
- "Build, deploy, and monitor our data processing pipelines (Java, Python, Spark, Flink)" ([IMC Trading](../Appendix/job_posting_imc.md))

**Week 9: Basic MLOps and monitoring models**

- "they engineer data, build and deploy models, and monitor them in production" ([JPMorganChase](../Appendix/job_posting_jpmorganchase.md))
- "Experience with monitoring, observability, and alerting systems for data pipelines" ([DRW](../Appendix/job_posting_drw.md))

**Every week: know the data**

- "An understanding of the financial data vendor landscape and product offerings" ([DRW](../Appendix/job_posting_drw.md))
- "Experience with financial datasets (e.g. Refinitiv, S&P, Bloomberg) is a big plus" ([Hudson River Trading](../Appendix/job_posting_hudson_river_trading.md))
- "Strong understanding of financial point-in-time and time-series data and analysis" ([DRW](../Appendix/job_posting_drw.md))

The course does not cover everything in these postings. Kafka, Spark,
Kubernetes, and warehouse platforms like Snowflake and Databricks appear in
several and are beyond our scope. What the course does claim is the layer
beneath all of it: the habits of building pipelines that are versioned, tested,
documented, automated, and reproducible.

(course-map-archived-postings)=
## Appendix: the archived job postings

Job postings are ephemeral. Once a role is filled the page is taken down. Each
posting quoted on this page is therefore preserved on its own page of this
website, with the full text, a screenshot, a PDF snapshot, and a link to a
Wayback Machine copy. All ten were collected in August 2026.

| Firm | Role | Location | Archived copy |
|---|---|---|---|
| Jane Street | Data Engineer | London | [Full posting](../Appendix/job_posting_jane_street.md) |
| Citadel Securities | Senior Research Engineer (Data) | Miami | [Full posting](../Appendix/job_posting_citadel_securities.md) |
| IMC Trading | Data Engineer | Chicago | [Full posting](../Appendix/job_posting_imc.md) |
| DRW | Data Engineer, Cumberland/FICCO | Chicago | [Full posting](../Appendix/job_posting_drw.md) |
| Point72, Cubist Systematic Strategies | Data Engineer | New York | [Full posting](../Appendix/job_posting_point72.md) |
| Jump Trading | Campus Data Engineer (Intern) | Chicago | [Full posting](../Appendix/job_posting_jump_trading.md) |
| Hudson River Trading | Data Production Engineer | London, New York, Singapore | [Full posting](../Appendix/job_posting_hudson_river_trading.md) |
| Akuna Capital | Software Engineer, Data Engineering | Chicago | [Full posting](../Appendix/job_posting_akuna_capital.md) |
| AQR Capital Management | Portfolio Analytics Engineer, Vice President | Greenwich, CT | [Full posting](../Appendix/job_posting_aqr.md) |
| JPMorganChase | Data & AI Internship Program | | [Full posting](../Appendix/job_posting_jpmorganchase.md) |

For the longer argument these postings support, see
[What Is This Course About?](../Week1/what_is_this_course_about.md)
The index of archived postings is [here](../Appendix/job_postings.md).

## References

The papers in the table above, in the order that we cover them. Each title links
to the published version. Where a free version exists, it is linked as well.
From a campus network or the library proxy, the journal links should give you
the full text.

- **Week 0.** Markowitz, Harry. ["Portfolio Selection."](https://doi.org/10.1111/j.1540-6261.1952.tb01525.x) *The Journal of Finance* 7, no. 1 (1952): 77-91.
- **Week 1.** Sharpe, William F. ["Capital Asset Prices: A Theory of Market Equilibrium under Conditions of Risk."](https://doi.org/10.1111/j.1540-6261.1964.tb02865.x) *The Journal of Finance* 19, no. 3 (1964): 425-442.
- **Week 2.** Fama, Eugene F., and Kenneth R. French. ["Common Risk Factors in the Returns on Stocks and Bonds."](https://doi.org/10.1016/0304-405X(93)90023-5) *Journal of Financial Economics* 33, no. 1 (1993): 3-56.
- **Week 3.** Gürkaynak, Refet S., Brian Sack, and Jonathan H. Wright. ["The U.S. Treasury Yield Curve: 1961 to the Present."](https://doi.org/10.1016/j.jmoneco.2007.06.029) *Journal of Monetary Economics* 54, no. 8 (2007): 2291-2304. First circulated in 2006 as Finance and Economics Discussion Series paper 2006-28, which is [free from the Federal Reserve Board](https://www.federalreserve.gov/pubs/feds/2006/200628/200628abs.html).
- **Week 4.** Constantinides, George M., Jens Carsten Jackwerth, and Alexi Savov. ["The Puzzle of Index Option Returns."](https://doi.org/10.1093/rapstu/rat004) *The Review of Asset Pricing Studies* 3, no. 2 (2013): 229-257.
- **Week 5.** Dickerson, Alexander, Cesare Robotti, and Giulio Rossetti. ["The Corporate Bond Factor Replication Crisis."](https://arxiv.org/abs/2604.07880) Working paper, 2026. Also on [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=6088966). An earlier version circulated as "Common Pitfalls in the Evaluation of Corporate Bond Strategies." The data and code are at [Open Source Bond Asset Pricing](https://openbondassetpricing.com/).
- **Week 6.** Holden, Craig W., and Stacey Jacobsen. ["Liquidity Measurement Problems in Fast, Competitive Markets: Expensive and Cheap Solutions."](https://doi.org/10.1111/jofi.12127) *The Journal of Finance* 69, no. 4 (2014): 1747-1785. A [free copy](https://host.kelley.iu.edu/cholden/Holden%20and%20Jacobsen%20(2014).pdf) is on Craig Holden's website.
- **Week 7.** Ardia, David, Emanuele Guidotti, and Tim A. Kroencke. ["Efficient Estimation of Bid-Ask Spreads from Open, High, Low, and Close Prices."](https://doi.org/10.1016/j.jfineco.2024.103916) *Journal of Financial Economics* 161 (2024): 103916. Also on [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3892335), with code at the [project website](https://bidask.eguidotti.com/).
- **Week 8.** Bernanke, Ben S., and Kenneth N. Kuttner. ["What Explains the Stock Market's Reaction to Federal Reserve Policy?"](https://doi.org/10.1111/j.1540-6261.2005.00760.x) *The Journal of Finance* 60, no. 3 (2005): 1221-1257. A free version is [NBER Working Paper 10402](https://www.nber.org/papers/w10402).
- **Week 9.** Welch, Ivo, and Amit Goyal. ["A Comprehensive Look at the Empirical Performance of Equity Premium Prediction."](https://doi.org/10.1093/rfs/hhm014) *The Review of Financial Studies* 21, no. 4 (2008): 1455-1508.
