# A Tour of the Data

One goal of this course is to get you working with as many of the important
financial data sets as possible, early enough that you can choose among them
for your final project. This page is a short preview of each.

## Where this week's data came from

The returns in the [portfolio selection notebook](../notebooks/_01_markowitz.ipynb)
come from CRSP, pulled through WRDS by the script `src/pull_crsp.py` in the
[HW 0 repository](https://github.com/finm-32800/hw0). When you ran `doit`, that
script downloaded a cached copy of the extract, because your WRDS account is
probably not approved yet. Set `NO_CACHE=True` and the same script pulls the
data from WRDS directly. Read it either way. It shows the three things that
every data pull in this course does:

1. It connects to the data provider with credentials that are kept *outside*
   the code.
2. It states exactly which tables, columns, dates, and securities it wants, in
   SQL.
3. It saves the result to disk, so that the rest of the pipeline reads a file
   and never depends on the network.

Notice one detail in the script. It looks up each stock by its ticker, but only
to find the stock's **PERMNO**, CRSP's permanent identifier. Tickers change and
are reused. A pipeline that identifies securities by ticker will eventually
merge two different companies.

## WRDS: CRSP, Compustat, OptionMetrics, TRACE, and TAQ

[Wharton Research Data Services (WRDS)](https://wrds-www.wharton.upenn.edu/) is
the platform that most academic finance research runs on. One account gives you
SQL access to many data sets. See [Introduction to WRDS](../Week2/WRDS_intro_and_web_queries.md)
for a fuller introduction. The ones we use:

- **CRSP** (Center for Research in Security Prices), founded here at the
  University of Chicago in 1960. Prices, returns, and shares outstanding for US
  stocks back to 1926, free of survivorship bias. Also US Treasury prices. *Weeks 0
  through 3.*
- **Compustat.** Accounting data from company financial statements. Merging it
  with CRSP is a rite of passage. *Week 2.*
- **OptionMetrics.** End-of-day prices, implied volatilities, and greeks for US
  equity and index options. *Week 4.*
- **FINRA TRACE.** Transaction-level data for US corporate bonds. It is
  notoriously messy, which makes it our best case study in data validation.
  *Week 5.*
- **NYSE TAQ** (Trade and Quote). Every trade and quote in US equities, stamped
  to the millisecond. It is far too large to download, so we send the
  computation to the data. *Weeks 6 and 7.*

## Databento: CME futures, options on futures, and the order book

The program has a subscription to [Databento](https://databento.com/) for
historical data from CME Globex: futures and options on futures, including the
E-mini S&P 500, Treasury futures, SOFR, and fed funds futures. Unlike the
end-of-day data above, it goes all the way down to the full order book, message
by message. *Weeks 4 and 8, and in the microstructure weeks.*

## Bloomberg

The Bloomberg Terminal has the broadest coverage of any source, and it is what
many of you will use at work. It is also the hardest to build a pipeline
around, because the data can only be pulled at a physical terminal. We will
use it for spot checks against our own calculations. See
[The Bloomberg Terminal](../Week7/bloomberg_terminal.md).

## FRED and other public data

[FRED](https://fred.stlouisfed.org/), from the Federal Reserve Bank of St.
Louis, serves hundreds of thousands of macroeconomic time series through a free
API. Because it needs no license, it is the easiest source to use in an
automated pipeline that runs in the cloud, as the
[FedWatch monitor](https://finm-32800.github.io/case_study_fedwatch/) does.
