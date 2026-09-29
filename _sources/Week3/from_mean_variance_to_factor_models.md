# From Mean-Variance to Factor Models

In Lecture 0 you solved one investor's portfolio problem. This page asks what
happens when *every* investor solves it, and how that question leads first to
the CAPM and then to multifactor models like Fama-French. The theory itself is
outside the scope of this course. It is here so that you know why the factors
you build in HW 1 exist. There are no derivations, and the only formulas are
ones you have already seen.

## Two funds are enough

The [appendix to the HW 0 notebook](../notebooks/_02_markowitz_derivation.ipynb)
proved the **two-fund theorem**: every portfolio on the mean-variance frontier
is a mix of the same two portfolios. Add a risk-free asset and the two funds
become especially simple. An investor who mixes the risk-free asset with a risky
portfolio $w$ moves along a straight line whose slope is the Sharpe ratio of
$w$, so the best risky portfolio is the one that solves

$$
\max_{w} \; \frac{w^\top \mu - r_f}{\sqrt{w^\top \Sigma w}}
\quad \text{subject to} \quad w^\top \mathbf{1} = 1 ,
$$

which is the **tangency portfolio** from HW 0. Nothing in this problem depends
on how risk averse the investor is. A cautious investor and an aggressive one
hold the *same* risky portfolio and differ only in how much they put in it
versus the risk-free asset. This is two-fund separation with a risk-free
asset, due to Tobin (1958).

## If everyone holds it, it is the market: the CAPM

Now suppose all investors agree on $\mu$ and $\Sigma$. Then they all hold the
same tangency portfolio, in different amounts. Add up everyone's holdings and
you get every share outstanding, which is the value-weighted **market
portfolio**. So in equilibrium the tangency portfolio *is* the market.

That one fact is the Capital Asset Pricing Model of Sharpe (1964), Lintner
(1965), and Mossin (1966). Saying the market is the tangency portfolio is the
same as saying that each asset's expected excess return is proportional to its
beta with the market:

$$
E[R_i] - r_f = \beta_i \, \big(E[R_m] - r_f\big).
$$

The intuition is the first-order condition of the tangency problem. At the
optimum, no asset can be tilted toward without lowering the Sharpe ratio, so an
asset's reward must be in line with how much it adds to the market's risk,
which is its beta.

This is what makes the CAPM testable. Regress a portfolio's excess return on
the market's excess return. If the CAPM holds, the intercept (Jensen's alpha)
is zero. A nonzero alpha means the market is *not* the tangency portfolio: some
mix of the market and that portfolio has a higher Sharpe ratio than the market
alone (Gibbons, Ross, and Shanken 1989). "The CAPM fails" and "the market is
not mean-variance efficient" are the same statement.

## When one period is not enough: the ICAPM

Markowitz's problem has one period. Real investors live through many, and the
investment opportunities they face change over time: interest rates move,
expected returns rise and fall, volatility comes and goes. Merton (1973) showed
that a long-horizon investor cares about more than next period's wealth. They
also care about how good their opportunities will be afterward, so they value
assets that pay off when opportunities get worse, as a hedge.

In this **Intertemporal CAPM (ICAPM)**, two funds are no longer enough.
Investors hold the tangency portfolio *plus* a hedging portfolio for each
source of change in the opportunity set (each "state variable"). Expected
returns then depend on several betas: one on the market and one on each
state-variable portfolio. One period gave one factor. Many periods give many
factors.

Breeden (1979) showed that all of Merton's betas can be collapsed into a single
beta with aggregate consumption. That is the Consumption CAPM. It is elegant but
hard to test, because consumption is measured poorly and infrequently.

## From the ICAPM to Fama-French

The ICAPM says that extra factors should exist, but not what they are. Fama and
French (1993) went the other way and started from the data. Sorting stocks on
size and book-to-market leaves large CAPM alphas, so they built two portfolios
to capture them: **SMB** (small minus big) and **HML** (high minus low
book-to-market). Fama and French (1996) and Fama (1996) then read the
three-factor model as an ICAPM, with SMB and HML standing in for unnamed state
variables that investors want to hedge. Fama calls the portfolios investors hold
in that world "multifactor efficient."

Two caveats. First, this is an interpretation, not a derivation. Nobody has
shown which state variables SMB and HML track, and others argue the premiums
come from mispricing instead of risk (Lakonishok, Shleifer, and Vishny 1994).
Second, the Arbitrage Pricing Theory of Ross (1976) justifies multifactor models
by a different route, with no investor optimization at all.

In mean-variance terms the story comes back to where it started. The CAPM says
the market alone is the tangency portfolio. A multifactor model says the
tangency portfolio is a mix of the factors, so Mkt, SMB, and HML together should
reach a higher Sharpe ratio than the market alone, and no other portfolio should
have alpha once all three are in the regression.

## Where the data comes in

The [CAPM and Fama-French notebook](../notebooks/_06_CAPM_and_Fama_French_ipynb.ipynb)
tests both claims on the portfolios your HW 1 pipeline builds. It computes the
tangency portfolio of the factors with the HW 0 formula, finds the alphas the
CAPM leaves behind, and checks how many of them SMB and HML absorb.

## References

- Breeden, Douglas T. "An Intertemporal Asset Pricing Model with Stochastic
  Consumption and Investment Opportunities." *Journal of Financial Economics*
  7, no. 3 (1979): 265-296.
- Fama, Eugene F. "Multifactor Portfolio Efficiency and Multifactor Asset
  Pricing." *Journal of Financial and Quantitative Analysis* 31, no. 4 (1996):
  441-465.
- Fama, Eugene F., and Kenneth R. French. "Common Risk Factors in the Returns on
  Stocks and Bonds." *Journal of Financial Economics* 33, no. 1 (1993): 3-56.
- Fama, Eugene F., and Kenneth R. French. "Multifactor Explanations of Asset
  Pricing Anomalies." *Journal of Finance* 51, no. 1 (1996): 55-84.
- Gibbons, Michael R., Stephen A. Ross, and Jay Shanken. "A Test of the
  Efficiency of a Given Portfolio." *Econometrica* 57, no. 5 (1989): 1121-1152.
- Lakonishok, Josef, Andrei Shleifer, and Robert W. Vishny. "Contrarian
  Investment, Extrapolation, and Risk." *Journal of Finance* 49, no. 5 (1994):
  1541-1578.
- Lintner, John. "The Valuation of Risk Assets and the Selection of Risky
  Investments in Stock Portfolios and Capital Budgets." *Review of Economics and
  Statistics* 47, no. 1 (1965): 13-37.
- Merton, Robert C. "An Intertemporal Capital Asset Pricing Model."
  *Econometrica* 41, no. 5 (1973): 867-887.
- Mossin, Jan. "Equilibrium in a Capital Asset Market." *Econometrica* 34, no. 4
  (1966): 768-783.
- Ross, Stephen A. "The Arbitrage Theory of Capital Asset Pricing." *Journal of
  Economic Theory* 13, no. 3 (1976): 341-360.
- Sharpe, William F. "Capital Asset Prices: A Theory of Market Equilibrium under
  Conditions of Risk." *Journal of Finance* 19, no. 3 (1964): 425-442.
- Tobin, James. "Liquidity Preference as Behavior Towards Risk." *Review of
  Economic Studies* 25, no. 2 (1958): 65-86.
