# Course Map: Is This the Right Course for You?

## Three computing courses

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

For the longer version, including the job postings that motivated the course's
design, see [What Is This Course About?](../Week1/what_is_this_course_about.md)

## One paper and one tool each week

Every week pairs a tool for building data pipelines with a well-known finance
paper and, usually, a new data set. Replicating the paper gives you repetitions
with the tool. It also gives you a working knowledge of the papers and the data
that quantitative researchers are expected to know.

The plan for this quarter is below. The second half of the schedule may still
change.

| Week | Pipeline tool | Paper | Data |
|---|---|---|---|
| 0 | `requirements.txt`; clone and run | Markowitz (1952), portfolio selection | CRSP extract |
| 1 | Git, GitHub, virtual environments | The CAPM: build the market portfolio and the S&P 500 | CRSP |
| 2 | Task runners (PyDoit) | Fama and French (1993), the three-factor model | CRSP, Compustat |
| 3 | Reproducible reports: notebooks, GitHub Pages, LaTeX | Gürkaynak, Sack, and Wright (2006), the Treasury yield curve | CRSP Treasuries |
| 4 | Python packaging and documentation | Constantinides, Jackwerth, and Savov (2013), index option returns | OptionMetrics, CME Globex |
| 5 | Unit tests and data validation | Dickerson, Robotti, and Rossetti (2026), the corporate bond factor replication crisis: cleaning bond transactions | FINRA TRACE |
| 6 | SQL at scale, remote machines, and HPC | Measuring liquidity from intraday trades and quotes | NYSE TAQ |
| 7 | The data build tool (dbt) | Bid-ask spreads from daily prices, checked against TAQ | NYSE TAQ, CRSP |
| 8 | CI/CD with GitHub Actions | Replicating the CME FedWatch tool | CME fed funds futures |
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
