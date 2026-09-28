# Homework 3: Option-Implied Crash Probabilities

- **Launched:** week 4. **Due:** week 6. The date is posted on Canvas.
- **Link to Assignment:** posted at launch
- **Goes with:** week 4

## Overview

Options prices contain the market's probability distribution for the
underlying. In this assignment you recover it. Following
[Martin and Shi (2025)](https://personal.lse.ac.uk/martiniw/oib_martin_shi_latest.pdf),
*Forecasting Crashes with a Smile*, you build the risk-neutral distribution of
returns from the volatility smile by the Breeden-Litzenberger method, turn it
into an option-implied probability of a crash, and apply the paper's
corrections to get a bound that actually forecasts crashes out of sample.

The assignment runs in an arc. First, build the risk-neutral density from the
OptionMetrics volatility surface and get a crash probability. Second, discover
that the surface's observed strikes stop well short of the crash thresholds,
so that at short horizons the number rests on an extrapolation convention.
Third, rebuild the density from raw quotes on E-mini S&P 500 options from
Databento's CME Globex feed, where every listed strike is quoted, and meet the
convexity violations that the vendor's smoothed surface hid from you. Fourth,
integrate the same density to reproduce a second published measure of
option-implied risk in a few lines. Two data sources, one seam between them,
and the seam is the lesson.

The code you complete is a small, pip-installable Python package with tests,
which is why this assignment sits in the packaging week. Smile fitting and
risk-neutral density extraction is daily work on an options market-making
desk, and the assignment is built with those roles in mind.

## Learning Outcomes

- Recover a risk-neutral distribution from option prices and understand why monotone regression is needed to do it from raw quotes.
- Compute option-implied crash probabilities and the bounds of Martin and Shi (2025).
- Pull and harmonize two option data sources, OptionMetrics via WRDS and CME Globex via Databento, and validate one against the other.
- Complete and test a Python package, with `pyproject.toml`, a build backend, and a documented public API.

## Data

- OptionMetrics volatility surface (`vsurfd`) and, for the single-name cross-section, the OptionMetrics files on WRDS.
- E-mini S&P 500 options on futures from Databento `GLBX.MDP3`, which is free under the course license. Single-name equity options are on the metered OPRA feed and are not used.

Details, the scoped universe and date range, the grading harness, and the
repository link are posted at launch.
