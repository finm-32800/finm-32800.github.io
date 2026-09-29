# Exam Preparation

This page is the running outline of what the midterm covers. It is the single
source of truth for scope: if a page, notebook, or paper is listed here, it is
fair game.

## Format

The midterm is in-person, closed-book, closed-notes, multiple choice, answered
on a bubble sheet. Each question has four options, (A) through (D), and **one or
more options may be correct**. Most questions have more than one correct
option, and in some every option is correct. You are not told how many to
select. A question earns its point only if every bubble is right: all correct
options filled in and all incorrect options left blank. There is no partial
credit.

Questions test three things in roughly equal measure:

- **The tools.** What each tool in the pipeline is for, how it is invoked, and
  what its configuration means. Expect short code blocks (a `dodo.py`, a SQL
  query, a workflow YAML file, a cron line, an `sbatch` script) followed by
  "what happens" or "what is wrong here".
- **The data.** What each dataset is, who provides it, how we access it, its key
  identifiers and fields, and the quirks the notes call out.
- **The papers.** What each replicated paper did, how the replication constructs
  it, and what it finds.

## The Midterm

The midterm is held in class in **week 8**, on Tuesday, November 17, and covers
the material through week 7: all lecture notes, in-class discussions, the notebooks linked from the
weekly chapters, and Homework 1 through 4. The date and room are posted on
Canvas. There is no final exam.

## Practice Questions

A set of practice questions in the same format, with the answer key, is handed
out ahead of the midterm. It is not graded. Work it under exam conditions and
grade yourself; the answer key explains why each option is right or wrong.

## Online Notes

We do not cover every page of the online notes in class. **All material in the
course notes is fair game**, including pages not explicitly discussed in class.
You are responsible for reading the full notes on your own. Each week's chapter
opens with a table of contents listing the pages for that week; those pages and
the notebooks they link to are the scope.

## Homework Assignments

Each homework page and the notebooks it links to ("HW guides") may appear on
either exam. Questions about the homework ask what the pipeline does and why,
not for the numerical results.

## Papers

The following papers and methods are covered in the notes and may appear on the
midterm. More are added here as the quarter progresses.

- Markowitz, H. (1952). Portfolio Selection. *Journal of Finance*, 7(1), 77--91.
- Sharpe, W. F. (1964). Capital asset prices: A theory of market equilibrium
  under conditions of risk. *Journal of Finance*, 19(3), 425--442. The CAPM, as
  the framing for the CRSP market portfolio and the S&P 500 reconstruction.
- Fama, E. F., & French, K. R. (1993). Common risk factors in the returns on
  stocks and bonds. *Journal of Financial Economics*, 33(1), 3--56. Including
  the investment (asset growth) sort from Fama and French (2015), *A five-factor
  asset pricing model*, *Journal of Financial Economics*, 116(1), 1--22.
- Gürkaynak, R. S., Sack, B., & Wright, J. H. (2007). The U.S. Treasury yield
  curve: 1961 to the present. *Journal of Monetary Economics*, 54(8),
  2291--2304. (Federal Reserve Board working paper 2006-28.)
- The CME FedWatch methodology: implying FOMC meeting-outcome probabilities from
  30-Day Federal Funds futures.
- Dick-Nielsen, J. (2009, 2014). Liquidity biases in TRACE, and How to clean
  Enhanced TRACE data. As implemented in the Clean TRACE walkthrough.
- Martin, I., & Shi, R. (2025). Forecasting crashes with a smile. Working paper.
  Recovering the risk-neutral density from the volatility smile by
  Breeden-Litzenberger, and the crash-probability bounds.
- Easley, D., López de Prado, M. M., & O'Hara, M. (2012). Flow toxicity and
  liquidity in a high-frequency world. *Review of Financial Studies*, 25(5),
  1457--1493. Order book reconstruction, bulk volume classification, and VPIN
  in volume time.
- Holden, C. W., & Jacobsen, S. (2014). Liquidity measurement problems in fast,
  competitive markets: Expensive and cheap solutions. *Journal of Finance*,
  69(4), 1747--1785. Computing the NBBO and standard liquidity measures from
  TAQ, and why the choice between Daily and Monthly TAQ changes the answer.

*Additional papers are posted here as the course progresses.*
