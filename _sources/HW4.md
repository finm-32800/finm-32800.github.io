# Homework 4: Order Book Validation and Flow Toxicity

- **Launched:** week 5. **Due:** week 7. The date is posted on Canvas.
- **Link to Assignment:** posted at launch
- **Goes with:** weeks 5 and 6

## Overview

A limit order book is the state of a market at an instant, and a market maker
lives inside it. In this assignment you rebuild the E-mini S&P 500 futures
book from the raw message feed, validate your reconstruction against the
vendor's own book, and then measure the toxicity of order flow following
[Easley, López de Prado, and O'Hara (2012)](https://doi.org/10.1093/rfs/hhs053),
*Flow Toxicity and Liquidity in a High-Frequency World*, whose VPIN measure was
built on exactly this contract.

The assignment starts locally on one trading day and then scales. Pull the
market-by-order (`mbo`) and top-ten-levels (`mbp-10`) schemas for ES from
Databento's CME Globex feed. Replay the messages to rebuild the book, and
diff your book against the vendor's `mbp-10` snapshot: every discrepancy is a
bug you can locate, which makes this the cleanest data-validation exercise in
the course. Classify trades by the bulk volume method and by the tick rule and
check them against each other. Then compute VPIN in volume time. The full run
covers a quarter of trading days, and the days are independent, so it runs as
an array of batch jobs on a cluster: either UChicago's Midway3 with SLURM or
the WRDS Cloud with Grid Engine. Which platform hosts the graded run is
announced at launch; the other is used in class.

The extension aimed at options desks: pull top-of-book quotes for the E-mini
options and ask whether options market makers widen their quoted spreads when
the underlying's order flow turns toxic.

## Learning Outcomes

- Reconstruct a limit order book from market-by-order messages and validate it against a reference.
- Write data-validation tests with a known ground truth, and trade-classification checks without one.
- Compute VPIN and reproduce the toxicity-before-stress pattern of Easley, López de Prado, and O'Hara (2012).
- Work with data that does not fit on a laptop: Polars, Parquet, lazy evaluation, and a job scheduler on a remote machine.
- Connect to a cluster over SSH, move data with `rsync`, submit and monitor a job array, and bring results back.

## Data

- Databento `GLBX.MDP3`: `mbo`, `mbp-10`, and `mbp-1` for E-mini S&P 500 futures and E-mini options, free under the course license.
- The in-class contrast is NYSE TAQ on the WRDS Cloud, following Holden and Jacobsen (2014); see [Pulling Market Data From Databento](notebooks/_01_databento_ipynb.ipynb) for the vendor's schemas.

Details, the date range, the platform for the graded run, and the repository
link are posted at launch. An optional practice run on Midway3, with a
different pipeline, is in [Optional Lab: Clean TRACE on Midway](Week8/optional_lab_clean_trace_on_midway.md).
