# List of Potential Final Projects

The project that you are assigned will come from one of the categories below. Each project asks you to do the same thing: take a finance paper, rebuild its dataset from the original sources, reproduce the tables and figures that carry its main result, and then extend the sample to the present.
If you can't access the papers below, you can find a link to them here: https://drive.google.com/drive/folders/1uaFsV1nn9MeO_XIRcolBnKcbkHKCEHu8?usp=drive_link

Every project here can be built from data the course already has: WRDS (CRSP, Compustat, daily TAQ, OptionMetrics, Mergent FISD, CRSP Treasury, Lipper TASS), Databento's CME Globex feed, Bloomberg in the trading lab, and free public sources such as FRED, SEC EDGAR, and Ken French's library. Where a paper's original vendor is something we do not have, the entry says what to substitute and what coverage you give up.

Not sure what a finished replication looks like? The [Project Previews](project_previews.md) page walks through three projects that students in this course actually built — one each for the published site, the written report, and the extension — and [Past Final Projects](past_final_projects.md) links the finished repositories from the last four cohorts.

Table and figure numbers below refer to the published version of each paper unless the entry says otherwise. Check them against whichever copy you work from; several of these papers circulated for years as working papers with different numbering.

## Order Flow and Market Microstructure

### 1. [Tracking Retail Investor Activity](https://doi.org/10.1111/jofi.13033)

Most U.S. retail equity orders never reach an exchange. They are sold to wholesalers—Citadel Securities, Virtu, and a few others—who internalize them and report the prints to a FINRA trade reporting facility with a small amount of sub-penny price improvement. This paper turns that piece of plumbing into a measurement device: an off-exchange print whose price sits just below a round penny is a retail *buy*, just above it is a retail *sell*. Out of public data comes a signed retail order-flow series for every U.S. stock, and it is now the standard retail-flow proxy in academic work and on trading desks alike.

The pipeline is a TAQ exercise end to end: read daily TAQ trades, keep the off-exchange prints, classify each one by the sub-penny remainder of its price, aggregate signed volume to the stock-day, and then ask whether the resulting order imbalance predicts returns over the following weeks.

- **Tasks**:
  - **Build the retail trade panel**: classify retail buys and sells in daily TAQ over the paper's sample, and report retail volume share by size decile and through time, with a summary table and a time-series figure (this is the data-cleaning deliverable)
  - **Replicate the descriptive results on retail order imbalance**: its persistence, its cross-sectional distribution, and how it varies with firm size and share price
  - **Replicate the main predictability result**: weekly portfolio sorts on retail order imbalance and the matching Fama-MacBeth regressions, including the horizon over which the effect decays
  - **Replicate the earnings-announcement evidence**: retail order imbalance in the days around announcements and its relation to the announcement return
  - **Update**: extend the panel through the most recent TAQ year and ask whether the predictability survived the move to zero commissions in 2019 and the retail boom of 2020-2021
- **Data sources** (all on WRDS): daily TAQ (`taqmsec`), CRSP daily and monthly stock files, IBES or Compustat for announcement dates, Fama-French factors
- **Notes**:
  - Daily TAQ is big. Build one year on a subset of stocks first and only then scale up; this is a natural WRDS Cloud or Midway job (week 6).
  - The classification rule is simple enough to state in a sentence and easy to get subtly wrong. Write unit tests against hand-constructed prints before running anything at scale.
  - Later work disputes how much retail flow the rule actually captures. Comparing your volume shares against the paper's, and against the later critiques, is a good use of the "own exhibit" requirement.
- **Citation**: Boehmer, Ekkehart, Charles M. Jones, Xiaoyan Zhang, and Xinran Zhang. "Tracking Retail Investor Activity." The Journal of Finance 76, no. 5 (2021): 2249-2305. https://doi.org/10.1111/jofi.13033 (working paper: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2822105)

### 2. [Informed Trading Intensity](https://doi.org/10.1111/jofi.13320)

Every standard measure of informed trading—PIN, VPIN, Kyle's lambda—starts from a model of how informed traders behave and then applies it to data. This paper inverts the order. It takes a set of trades that are known to be informed, namely the purchases Schedule 13D filers are later forced to disclose, and trains a machine learning model to recognize them from ordinary microstructure inputs. The fitted measure, informed trading intensity (ITI), "increases before earnings, mergers and acquisitions, and news announcements," and it works because "it captures nonlinearities and interactions between informed trading, volume, and volatility" that the closed-form measures assume away.

The pipeline: build daily microstructure features from TAQ (order imbalance, volume, volatility, spreads, and their intraday shape), label days using 13D filings from EDGAR, train and cross-validate the classifier, then evaluate the fitted measure on events it never saw and in cross-sectional return regressions. If you liked the VPIN half of HW 4, this is the project that picks up where it stops.

- **Tasks**:
  - **Build the feature panel**: daily TAQ-derived microstructure features plus the 13D event labels, with a summary table and a figure of the features in the days around labeled events (this is the data-cleaning deliverable)
  - **Replicate the estimation of ITI**: the training design, the cross-validation, and the reported model performance
  - **Replicate the event evidence**: ITI in the days before earnings announcements, M&A announcements, and news
  - **Replicate the asset-pricing results**: the return-reversal evidence and the cross-sectional pricing tests
  - **Benchmark against VPIN and PIN** on the same sample—a direct extension of HW 4, and the natural place for your own exhibit
  - **Validate against the authors' series**: the fitted stock-day ITI measure for 1993-2024 is posted at https://bogousslavsky.github.io/data/, which gives you a true benchmark rather than a published table
  - **Update**: extend the measure past the posted series
- **Data sources**: daily TAQ and CRSP (WRDS); SEC EDGAR Schedule 13D filings (free); IBES or Compustat announcement dates; the paper's news source is RavenPack, which we do not have—substitute EDGAR 8-K filing timestamps
- **Notes**:
  - The posted ITI series makes this one of the best-validated replications on the list: you can score your own measure against the authors' stock-day by stock-day rather than against four significant digits in a table.
  - Be careful with the labels. Schedule 13D discloses the purchase window after the fact, and how you map filings to trading days determines everything downstream.
- **Citation**: Bogousslavsky, Vincent, Vyacheslav Fos, and Dmitriy Muravyev. "Informed Trading Intensity." The Journal of Finance 79, no. 2 (2024): 903-948. https://doi.org/10.1111/jofi.13320

### 3. [Sparse Signals in the Cross-Section of Returns](https://doi.org/10.1111/jofi.12733)

This paper hands the LASSO the entire cross-section of lagged one-minute returns and asks it to forecast each stock's return one minute ahead, with no economic prior about which stocks should matter. It works: "the LASSO increases both out-of-sample fit and forecast-implied Sharpe ratios by identifying predictors that are unexpected, short-lived, and sparse." And the predictors it picks are not noise—they tend to be stocks with fresh news about fundamentals. It is the cleanest academic statement of what a short-horizon statistical arbitrage desk actually does.

The pipeline: build one-minute return bars for the cross-section from TAQ, then run a rolling LASSO for every stock in every minute, which is a genuinely large estimation problem and the reason this project needs a cluster.

- **Tasks**:
  - **Build the minute-bar panel**: one-minute returns for the sample stocks from TAQ with the paper's filters, plus a summary table and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate Tables I and II (out-of-sample fit)**: the LASSO's out-of-sample fit and its improvement over the AR(3), market, and other benchmarks
  - **Replicate Tables IV and V (trading strategies)**: performance of the forecast-implied strategies net of trading costs, and their turnover
  - **Replicate Table VI and Figures 4-6 (what gets selected)**: the characteristics of the selected predictors and how often they are used
  - **Replicate Table VII (news)**: the change in the LASSO's selection rate around news about a company
  - **Update**: extend to a recent year and ask whether the effect has decayed as the strategy became crowded
- **Data sources**: daily TAQ (WRDS) for the minute bars; CRSP for the cross-section and for delisting; the paper's news source is Dow Jones news flashes, which we do not have—substitute EDGAR 8-K filing timestamps
- **Notes**:
  - This is the most compute-hungry project on the list: a LASSO per stock per minute over several years. It is a job-array problem, and Midway or the WRDS Cloud (week 6) is the right home for it. Scale the universe down before you scale it up.
  - The working paper version posted on Alex Chinco's site has the same tables and is free.
- **Citation**: Chinco, Alex, Adam D. Clark-Joseph, and Mao Ye. "Sparse Signals in the Cross-Section of Returns." The Journal of Finance 74, no. 1 (2019): 449-492. https://doi.org/10.1111/jofi.12733
- **Paper PDF**: [ChincoClarkJosephYe2019SparseSignals.pdf](project_papers/ChincoClarkJosephYe2019SparseSignals.pdf) (working paper version)

### 4. [Efficient Estimation of Bid-Ask Spreads from Open, High, Low, and Close Prices](https://doi.org/10.1016/j.jfineco.2024.103916)

You can measure the effective spread directly if you have the trades and quotes. Most of the time you do not—you have daily open, high, low, and close, and that is all. The estimators that recover spreads from those four numbers (Roll, Corwin-Schultz, Abdi-Ranaldo) are biased downward when trading is infrequent, which is exactly the case where you most want them. This paper derives estimators that account for discretely observed prices and combines them optimally "to minimize estimation variance," producing the EDGE estimator now shipping in the `bidask` package.

What makes this a good course project is the validation structure. The estimator has a published reference implementation, and the quantity it estimates has an observable ground truth in TAQ. You are not comparing your output to a printed table, you are comparing it to the thing itself.

- **Tasks**:
  - **Build the price and benchmark panel**: CRSP daily open/high/low/close for the sample, plus TAQ effective spreads for the same stock-months as ground truth, with a summary table and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate the Monte Carlo**: bias and RMSE of EDGE against Roll, Corwin-Schultz, and Abdi-Ranaldo as trading frequency falls
  - **Replicate the empirical comparison**: each estimator's correlation with TAQ effective spreads, by size decile and through time
  - **Implement the estimator from the paper's equations**, then test your implementation against the `bidask` package output—the package is the oracle, your code is what is being tested
  - **Extend to futures**: apply the estimators to E-mini and Treasury futures from Databento, where the true spread is observable from the top of book at essentially no data cost
- **Data sources**: CRSP daily stock file and daily TAQ (WRDS); Databento GLBX (`mbp-1`) for the futures extension; reference implementation at https://github.com/eguidotti/bidask
- **Notes**:
  - Do not call the package and stop. The deliverable is your own implementation plus the test suite that proves it matches.
  - The futures extension is the most interesting part and is not in the paper. Futures give you a clean, cheap ground truth in an asset class where the daily-data estimators are used constantly and validated rarely.
- **Citation**: Ardia, David, Emanuele Guidotti, and Tim A. Kroencke. "Efficient estimation of bid-ask spreads from open, high, low, and close prices." Journal of Financial Economics 161 (2024): 103916. https://doi.org/10.1016/j.jfineco.2024.103916 (working paper: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3892335)

### 5. [A Tug of War: Overnight versus Intraday Expected Returns](https://doi.org/10.1016/j.jfineco.2019.03.011)

Split every daily return into its overnight (close-to-open) and intraday (open-to-close) halves and a strange thing appears: each half persists strongly on its own, while the two offset each other across periods, and the offsetting reversal runs for years. The punchline is the one every quant remembers—"profits of 14 trading strategies are earned either entirely overnight or entirely intraday." Momentum is an overnight strategy. Several others are entirely intraday. Anyone who trades a factor book at the open or the close has to know which of these they are in.

The pipeline is mostly CRSP: open prices, close prices, and careful handling of the corporate actions that make close-to-open returns misleading. The volume evidence pulls in TAQ.

- **Tasks**:
  - **Build the overnight/intraday decomposition**: firm-level overnight and intraday return series from CRSP, with the paper's screens, plus a summary table and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate the persistence and reversal tests**, including Figure 2, the t-statistics of the overnight/intraday persistence test across horizons
  - **Replicate the strategy table**: the overnight/intraday decomposition of returns for the 14 strategies—the central result
  - **Replicate Figures 1 and 3 (trading volume)**: dollar volume by 30-minute interval across the day, and large versus small trades
  - **Update**: extend through the most recent year, including the period after 2019 when retail participation and closing-auction volume both jumped
- **Data sources**: CRSP daily stock file (open, close, returns) on WRDS; Ken French's library for the factors and for the strategy definitions; daily TAQ for the intraday volume figures
- **Notes**:
  - CRSP daily open prices begin in 1992, which sets the start of the decomposition. The paper is explicit about this and so should you be.
  - Overnight returns are unusually sensitive to distribution and split adjustments. The unit tests for this project write themselves.
- **Citation**: Lou, Dong, Christopher Polk, and Spyros Skouras. "A tug of war: Overnight versus intraday expected returns." Journal of Financial Economics 134, no. 1 (2019): 192-213. https://doi.org/10.1016/j.jfineco.2019.03.011
- **Paper PDF**: [LouPolkSkouras2019TugOfWar.pdf](project_papers/LouPolkSkouras2019TugOfWar.pdf) (accepted version)

## High-Frequency Futures: Volatility and Tail Risk

### 6. [Exploiting the Errors: A Simple Approach for Improved Volatility Forecasting](https://doi.org/10.1016/j.jeconom.2015.10.007)

Realized volatility is measured, not observed, and how well it is measured varies enormously from day to day. The standard HAR model ignores that and treats every day's realized variance as equally informative. This paper lets the model's own coefficients depend on the estimated measurement error: "the models exhibit stronger persistence, and in turn generate more responsive forecasts, when the measurement error is relatively low." The resulting HARQ model beats HAR out of sample, and the idea—weight your regressors by how precisely you measured them—generalizes far past volatility.

This is the project where you build realized measures from raw ticks. Databento's CME Globex feed is flat-rate for this course, so the data is free, and the work is the work: bars, realized variance, realized quarticity, bipower variation, and the forecast evaluation on top.

- **Tasks**:
  - **Build the realized measure panel**: daily RV, RQ, bipower variation, and noise-robust RV for the E-mini S&P 500 and a set of liquid CME products, with Table 2's summary statistics and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate Table 1 (simulation)**: the simulation evidence that motivates the HARQ specification
  - **Replicate Table 3 (in-sample estimates)** and **Tables 4 and 5 (out-of-sample forecast losses)**, including the losses stratified by the level of measurement error—the crux result
  - **Replicate Tables 7 and 8 (weekly and monthly horizons)**
  - **Replicate Tables 9 and 10 (noise-robust RV)**: HARQ against HAR models built on noise-robust realized variances
  - **Update**: run the whole comparison on the modern sample, where tick frequency is orders of magnitude higher than in the paper's data
- **Data sources**: Databento `GLBX.MDP3` (free under the course subscription), `trades` or `ohlcv-1m` for ES, NQ, ZN, CL, and GC; the paper's stock-level results use TAQ, which is on WRDS if you want that leg too
- **Notes**:
  - Two Databento facts shape this project. History for `GLBX.MDP3` starts 2010-06-06, and on many days before 2017-05-21 the OHLCV schemas return fewer bars than actually traded. Build your bars from `trades` and validate them against `ohlcv-1m` on a post-2017 day before you trust anything earlier. That validation is exactly the HW 4 pattern applied to a new question.
  - Choosing the sampling frequency is a real decision with a literature behind it, not a default. Say what you chose and why.
- **Citation**: Bollerslev, Tim, Andrew J. Patton, and Rogier Quaedvlieg. "Exploiting the errors: A simple approach for improved volatility forecasting." Journal of Econometrics 192, no. 1 (2016): 1-18. Free copy: https://public.econ.duke.edu/~ap172/BPQ_Exploiting_Errors_JoE_2016.pdf

### 7. [Tails, Fears, and Risk Premia](https://doi.org/10.1111/j.1540-6261.2011.01695.x)

How much of the equity premium is payment for ordinary volatility, and how much is payment for the rare, violent move? This paper separates them by estimating jump tails twice: once under the physical measure, from high-frequency index returns, and once under the risk-neutral measure, from deep out-of-the-money index puts. The gap between the two is a direct, model-light reading of investor fear, and the paper shows that "the compensation for rare events accounts for a large fraction of the equity and variance risk premia in the S&P 500 market index."

It is the one project on this list that uses both halves of the course's derivatives material at once: the high-frequency futures data from HW 4 and the option surface from HW 3.

- **Tasks**:
  - **Build both panels**: high-frequency S&P 500 returns and the out-of-the-money index option panel, with summary statistics and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate Table 1 (mean jump intensities)** and **Figure 1 (implied measures)**
  - **Replicate Tables 5 and 7 (risk-neutral tail estimation)**: the extreme-value fit to the option-implied tails
  - **Replicate Figure 2 (equity and variance risk premia due to large jumps)**—the paper's headline decomposition
  - **Replicate Figure 6 (time-of-day factor)**: the intraday pattern in the jump intensity
  - **Update**: extend through the present, which now includes February 2018, March 2020, and August 2024 as out-of-sample tail events
- **Data sources**: Databento `GLBX.MDP3` for E-mini S&P 500 high-frequency returns from June 2010 (free); daily TAQ on WRDS for SPY high-frequency returns before 2010; OptionMetrics IvyDB US on WRDS for SPX options (`secid = 108105`) and the zero-coupon curve; CRSP for index returns
- **Notes**:
  - The paper's high-frequency sample predates Databento's history. Use TAQ's SPY trades for the early period and E-mini futures for the modern one, and document the seam honestly—the two instruments do not have the same trading hours, which matters for overnight jumps.
  - The extreme-value estimation is the hard part. Budget time for it and write the tests against simulated data where you know the tail index.
- **Citation**: Bollerslev, Tim, and Viktor Todorov. "Tails, Fears, and Risk Premia." The Journal of Finance 66, no. 6 (2011): 2165-2211. https://doi.org/10.1111/j.1540-6261.2011.01695.x
- **Paper PDF**: [BollerslevTodorov2011TailsFearsRiskPremia.pdf](project_papers/BollerslevTodorov2011TailsFearsRiskPremia.pdf) (working paper version)

## Monetary Policy and Return Predictability

### 8. [The Pre-FOMC Announcement Drift](https://doi.org/10.1111/jofi.12196)

Large average excess returns on U.S. equities are earned in the twenty-four hours *before* scheduled FOMC announcements—before anyone can know what the committee decided. The effect is big enough to account for a substantial share of the equity premium since 1994, it is not explained by the policy surprise itself, and it does not appear before other macroeconomic releases. It is one of the most cited anomalies in monetary-policy finance and one of the most heavily traded.

The daily half of this replication is cheap: a calendar and CRSP. The intraday half is where the work is, because the paper's headline window is a 2pm-to-2pm return that spans the overnight session, which means you need an instrument that trades overnight. E-mini S&P 500 futures are that instrument, and they are free under the course's Databento subscription.

- **Tasks**:
  - **Assemble the FOMC calendar and the return panel**: scheduled and unscheduled meeting dates, daily S&P 500 excess returns, and the 2pm-to-2pm intraday windows, reproducing **Table 1 (summary statistics)** (this is the data-cleaning deliverable)
  - **Replicate Table 2 (main daily regression)**: the pre-FOMC dummy in daily S&P 500 returns, with the paper's standard errors, plus **Table 4 (alternative samples)**
  - **Replicate Figure 1 (cumulative returns around the announcement)** and **Table 5 (returns before, at, and after the FOMC news)**
  - **Replicate Table 8 (fixed income instruments)** and **Table 6 (international indices)**, and **Table 7 (other economic news)**—the placebo that makes the result interesting
  - **Replicate Table 12 (out-of-sample analysis)**
  - **Update**: extend through 2026. The drift is widely claimed to have weakened after publication; settle it on your own data and say so plainly either way
- **Data sources**: CRSP and Ken French's library for daily returns; the Federal Reserve Board's website for the FOMC calendar (free); Databento `GLBX.MDP3` E-mini S&P 500 for the intraday windows from June 2010; daily TAQ (SPY) for intraday windows from 2003; fed funds futures for the Kuttner policy surprise, which you already built in HW 2
- **Notes**:
  - The paper's intraday data come from Thomson Reuters TickHistory and Tickdata.com, neither of which we have. The E-mini plus TAQ substitution above covers 2003 forward; the 1994-2002 intraday evidence is out of reach and should be reported as such.
  - HW 2 gives you the fed funds futures machinery for free. Reuse it rather than rebuilding it.
- **Citation**: Lucca, David O., and Emanuel Moench. "The Pre-FOMC Announcement Drift." The Journal of Finance 70, no. 1 (2015): 329-371. https://doi.org/10.1111/jofi.12196
- **Paper PDF**: [LuccaMoench2015PreFOMCAnnouncementDrift.pdf](project_papers/LuccaMoench2015PreFOMCAnnouncementDrift.pdf) (New York Fed Staff Report 512)

### 9. [Stock Returns over the FOMC Cycle](https://doi.org/10.1111/jofi.12818)

The claim is almost absurdly sharp: "since 1994, the equity premium is earned entirely in weeks 0, 2, 4, and 6 in Federal Open Market Committee (FOMC) cycle time, that is, even weeks starting from the last FOMC meeting." Odd weeks earn nothing. The authors trace it to informal communication from the Fed—the even-week pattern lines up with Board of Governors meetings and with the timing of leaks to the financial press.

The data here is nearly free, which shifts the difficulty entirely onto construction. Everything depends on the calendar: what counts as day zero, how unscheduled meetings are handled, how weeks are counted across a year with eight meetings and fifty-two weeks. This is a testing project as much as a replication project.

- **Tasks**:
  - **Build FOMC cycle time**: the meeting calendar, the day-zero convention, and the mapping from calendar days to cycle weeks, reproducing **Figure 2 (the histogram of meeting timing within the year)** (this is the data-cleaning deliverable)
  - **Replicate Figure 1 (stock returns over the FOMC cycle, 1994-2016)**—the picture everybody has seen
  - **Replicate Table 2 (international stock returns over the FOMC cycle)**
  - **Replicate Table 6 (the even-week effect and Board of Governors meetings)**—the mechanism
  - **Replicate Table 9 (predicting target changes with even-week returns that follow market declines)** and the Figure 4 mean-reversion evidence
  - **Update**: extend through 2026, covering the zero-lower-bound years, the 2022-2023 hiking cycle, and the post-2011 press-conference regime
- **Data sources**: CRSP and Ken French's library for daily returns (free); the Federal Reserve Board's website for the FOMC and Board meeting calendars; international index returns—the paper uses Datastream, substitute Bloomberg in the trading lab or free index data; fed funds futures from HW 2
- **Notes**:
  - Build the calendar as its own tested module with its own exhibits. If the calendar is wrong, everything downstream is wrong and nothing will tell you.
  - The authors post their data and the FOMC cycle-time variable, which gives you a reconciliation target for the hardest part of the build.
- **Citation**: Cieslak, Anna, Adair Morse, and Annette Vissing-Jorgensen. "Stock Returns over the FOMC Cycle." The Journal of Finance 74, no. 5 (2019): 2201-2248. https://doi.org/10.1111/jofi.12818
- **Paper PDF**: [CieslakMorseVissingJorgensen2019FOMCCycle.pdf](project_papers/CieslakMorseVissingJorgensen2019FOMCCycle.pdf) (working paper version)

## Machine Learning and the Cross-Section

### 10. [Empirical Asset Pricing via Machine Learning](https://doi.org/10.1093/rfs/hhaa009)

The paper that moved machine learning from the periphery of asset pricing to its center. It runs a controlled comparison—OLS, penalized linear models, dimension reduction, random forests, boosted trees, and neural networks—on a single problem, measuring risk premia for the U.S. cross-section, using 94 stock characteristics, eight macro predictors, and industry dummies. Trees and neural networks win, most of the gain comes from allowing nonlinear interactions, and the same handful of characteristics (price trends, liquidity, volatility) drives every method.

This is the largest engineering job on the list and the closest to what a systematic equity team actually maintains. It is also the project where the course's cluster material stops being optional.

- **Tasks**:
  - **Build the characteristic panel**: the 94 characteristics from CRSP and Compustat with point-in-time discipline, the macro predictors, and the industry dummies, with a coverage table and a figure showing how coverage changes over the decades (this is the data-cleaning deliverable, and it is most of the project)
  - **Replicate Table 1 (monthly out-of-sample stock-level prediction performance)**: out-of-sample R² by method, overall and split by large and small stocks
  - **Replicate Table 3 (Diebold-Mariano comparisons)** and **Table 5 (portfolio-level out-of-sample R²)**
  - **Replicate Figures 4 and 5 (variable importance)** and **Table 4 (macroeconomic variable importance)**
  - **Replicate Tables 7 and 8 and Figure 9 (machine learning portfolios)**: performance, drawdown, turnover, and cumulative returns
  - **Update**: extend the sample forward and report how the out-of-sample R² has held up
- **Data sources**: CRSP and Compustat on WRDS; Welch-Goyal macro predictors from Amit Goyal's website (free); Dacheng Xiu's website posts the characteristic list and supporting material
- **Notes**:
  - The compute is the constraint, not the statistics. Use Midway with job arrays (week 6), shrink the neural-network ensembles, and coarsen the hyperparameter grid—and say in the report exactly what you shrank and what it cost you.
  - Point-in-time construction of the accounting characteristics is where replications of this paper usually go wrong. Test the lag conventions explicitly.
- **Citation**: Gu, Shihao, Bryan Kelly, and Dacheng Xiu. "Empirical Asset Pricing via Machine Learning." The Review of Financial Studies 33, no. 5 (2020): 2223-2273. https://doi.org/10.1093/rfs/hhaa009
- **Paper PDF**: [GuKellyXiu2020EmpiricalAssetPricingML.pdf](project_papers/GuKellyXiu2020EmpiricalAssetPricingML.pdf) (working paper version)

### 11. [Shrinking the Cross-Section](https://doi.org/10.1016/j.jfineco.2019.06.008)

Given a hundred candidate return predictors, what is the stochastic discount factor? Run an unpenalized regression and you fit noise; drop everything but a handful of factors and you throw away real information. This paper shows that the right answer is economically motivated shrinkage: a prior that says the SDF should not load heavily on low-variance principal components, which turns out to be equivalent to a particular ridge-plus-lasso estimator. The resulting SDF is sparse in PC space rather than in characteristic space, and it holds up out of sample where sparse characteristic models do not.

It is one of the most directly usable pieces of research on this list. The estimator is what a multi-factor equity desk reaches for when it has more signals than history.

- **Tasks**:
  - **Build the anomaly portfolio set**: the 50 anomaly portfolios from CRSP and Compustat, plus the interaction-based characteristic-managed portfolios, with a summary table and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate Table 1 (largest SDF factors, 50 anomaly portfolios)** and **Table 3 (largest SDF factors, models with interactions)**
  - **Replicate the cross-validated shrinkage path**: out-of-sample R² and Sharpe ratio across the two-dimensional penalty grid, which is the paper's central figure
  - **Replicate Table 4 (out-of-sample alpha in the withheld sample)**
  - **Update**: re-estimate on data through the present and report how the withheld-sample result looks now that the withheld sample is history
- **Data sources**: CRSP and Compustat on WRDS; Serhiy Kozak posts the MATLAB code and the supporting portfolio data at https://github.com/serhiykozak/SCS
- **Notes**:
  - The reference code is MATLAB. Porting it to a tested Python package is a legitimate and valuable part of this project—but the report has to show the ported code reproduces the original numbers, not merely that it runs.
  - The same portfolio construction feeds project 12 and project 17. If two groups take those, coordinate on the build.
- **Citation**: Kozak, Serhiy, Stefan Nagel, and Shrihari Santosh. "Shrinking the cross-section." Journal of Financial Economics 135, no. 2 (2020): 271-292. https://doi.org/10.1016/j.jfineco.2019.06.008
- **Paper PDF**: [KozakNagelSantosh2020ShrinkingTheCrossSection.pdf](project_papers/KozakNagelSantosh2020ShrinkingTheCrossSection.pdf) (working paper version)

### 12. [Factor Timing](https://doi.org/10.1093/rfs/hhaa017)

Timing the market is famously hard. Timing *factors*, this paper argues, is not: "market-neutral equity factors are strongly and robustly predictable, and exploiting this predictability leads to substantial improvement in portfolio performance relative to static factor investing." The predictor is the one every value investor already knows—each factor's own book-to-market ratio—applied to the dominant principal components of a large anomaly cross-section. The implication for the SDF is uncomfortable: its conditional variance moves far more than standard models allow.

- **Tasks**:
  - **Build the anomaly portfolios and their book-to-market ratios**: the long-short anomaly set, its principal components, and the factor-level valuation ratios, reproducing **Table 1 (percentage of variance explained by anomaly PCs)** (this is the data-cleaning deliverable)
  - **Replicate Table 2 and Figure 1 (predicting dominant equity components with BE/ME ratios)**—the core predictability result
  - **Replicate Table 3 (predicting individual anomaly returns)** and **Table 5 (out-of-sample R² of various forecasting methods)**
  - **Replicate Table 6 (performance of various portfolio strategies)**: the economic value of factor timing
  - **Replicate Table 7 and Figure 2 (variance of the SDF)** and **Table 8 (SDF variance and macroeconomic variables)**
  - **Update**: extend through the present, which now includes the 2020-2021 value drawdown and reversal
- **Data sources**: CRSP and Compustat on WRDS; the anomaly-portfolio code and data that back both this paper and project 11, at https://github.com/serhiykozak/SCS
- **Notes**:
  - Shares its portfolio construction with project 11. Building that layer as a tested, installable package that both projects could use is the kind of engineering the rubric rewards.
- **Citation**: Haddad, Valentin, Serhiy Kozak, and Shrihari Santosh. "Factor Timing." The Review of Financial Studies 33, no. 5 (2020): 1980-2018. https://doi.org/10.1093/rfs/hhaa017
- **Paper PDF**: [HaddadKozakSantosh2020FactorTiming.pdf](project_papers/HaddadKozakSantosh2020FactorTiming.pdf)

### 13. [Is There a Replication Crisis in Finance?](https://doi.org/10.1111/jofi.13249)

Hundreds of published return predictors, most of them tested on the same U.S. data, many of them now suspected of being nothing. This paper answers the charge with a Bayesian hierarchical model of factor replication estimated on 153 factors, and finds that most of them do replicate, that they cluster into thirteen economic themes, and that the evidence is *strengthened* rather than weakened by the sheer number of factors, because a hierarchical prior learns from the population of factors rather than testing each in isolation. The out-of-sample test is a new dataset covering 93 countries.

For this course the paper has a special status: the authors publish their full code, and the global factor data is downloadable directly from WRDS. That makes this the one project where a complete reference pipeline exists—so the deliverable is not discovery, it is a tested Python rebuild that reconciles against it, which is precisely the skill the course is about.

- **Tasks**:
  - **Pull and profile the global factor dataset from WRDS**, then **rebuild a subset of the factors from raw CRSP and Compustat** and reconcile your series against the published ones, with a reconciliation table and figure (this is the data-cleaning deliverable and the heart of the project)
  - **Replicate Figure 1 (replication rates versus the literature)**
  - **Replicate Figure 6 (replication rates in global data)** and **Figure 12 (alphas by size group and region)**
  - **Replicate Figures 13 and 14 (tangency portfolio weights and the evolution of the tangency Sharpe ratio)**
  - **Replicate Figure 2 (out-of-sample performance of marginally significant factors)** and **Figure 3 (the false-discovery-rate simulation)**
  - **Update**: extend the factor set through the most recent data and report which clusters have held up
- **Data sources**: the Global Factor Data on WRDS; CRSP and Compustat (North America and Global) for the rebuild; reference code at https://github.com/bkelly-lab/ReplicationCrisis
- **Notes**:
  - Because the reference implementation exists, the bar for this project is higher than for the others. The report must be explicit about what your pipeline does differently and why every difference is either intentional or a bug you found.
  - The reference code is R and SAS. The port is the work.
- **Citation**: Jensen, Theis Ingerslev, Bryan Kelly, and Lasse Heje Pedersen. "Is There a Replication Crisis in Finance?" The Journal of Finance 78, no. 5 (2023): 2465-2518. https://doi.org/10.1111/jofi.13249 (NBER working paper 28432: https://www.nber.org/papers/w28432)

## Rates, Funding, and Liquidity

### 14. [Risk-Free Interest Rates](https://doi.org/10.1016/j.jfineco.2021.06.012)

What is the actual risk-free rate? Treasury yields are contaminated by the convenience yield investors pay for safe, liquid collateral; OIS and repo rates carry their own frictions. This paper reads the risk-free rate out of option prices instead. A box spread on an index—long a call spread, short the matching put spread—has a fixed, known payoff regardless of where the index lands, so its price is a pure discount factor. The authors "estimate risk-free interest rates unaffected by convenience yields on safe assets by inferring them from risky asset prices without relying on any specific model of risk," and find Treasuries carry a convenience yield of roughly 40 basis points, quadrupling in the financial crisis.

The pipeline is an options-data project with a rates payoff: build box spreads from the SPX and DJX option surfaces, extract the implied discount curve out to three years, and compare it against every other risk-free benchmark.

- **Tasks**:
  - **Build the option-implied rate panel**: box spreads from SPX and DJX options across strikes and maturities, reproducing **Tables 1 and 3 (summary statistics of SPX and DJX option-implied rates)** (this is the data-cleaning deliverable)
  - **Replicate Table 2 (comparison with other implied and observed rates)** and **Table 4 (the DJX-SPX difference)**
  - **Replicate Table 7 (the influence of bid-ask spreads on estimated rates)**—the robustness check that decides whether the result is real
  - **Replicate Table 9 (effects of fed funds surprises on yields around FOMC announcements)**, reusing the policy-surprise machinery from HW 2
  - **Replicate Table 11 (government bond arbitrage)**
  - **Update**: extend through 2026, covering the 2020 dash-for-cash, the 2022-2023 hiking cycle, and the 2023 debt-ceiling episode—all periods where the convenience yield should move
- **Data sources**: OptionMetrics IvyDB US on WRDS (SPX `secid = 108105`, DJX, and the zero-coupon curve); FRED for OIS, Treasury bills, and GC repo; fed funds futures from HW 2
- **Notes**:
  - Both index option classes are European-style, so no early-exercise adjustment is needed. Filtering the surface is still the whole game—which strikes, which maturities, which bid-ask widths—and every filter needs a test.
  - This builds directly on the option material in HW 3 but asks a rates question, which makes it a good fit for a group that wants derivatives without doing another volatility project.
- **Citation**: van Binsbergen, Jules H., William F. Diamond, and Marco Grotteria. "Risk-free interest rates." Journal of Financial Economics 143, no. 1 (2022): 1-29. https://doi.org/10.1016/j.jfineco.2021.06.012
- **Paper PDF**: [vanBinsbergenDiamondGrotteria2022RiskFreeInterestRates.pdf](project_papers/vanBinsbergenDiamondGrotteria2022RiskFreeInterestRates.pdf) (NBER working paper 26138)

### 15. [Bond Risk Premiums with Machine Learning](https://doi.org/10.1093/rfs/hhaa062)

Treasury excess returns are predictable in-sample and stubbornly hard to predict out of sample. This paper brings the full machine learning toolkit—penalized regressions, trees, and neural networks—to bear on the problem, with a large macroeconomic panel alongside the yield curve itself, and finds economically meaningful out-of-sample gains concentrated in the periods where a bond investor would most want them.

This project has a feature no other entry on this list has: a **corrigendum**, published in the same issue, that revised the paper's empirical results. So the replication has a built-in question with a right answer—does your pipeline reproduce the original numbers, the corrected ones, or neither? That is the most honest test of a replication there is, and the write-up should confront it directly.

- **Tasks**:
  - **Build the bond return and predictor panel**: Treasury excess returns by maturity, the yield-curve factors, and the macro panel, with a summary table and a coverage figure (this is the data-cleaning deliverable)
  - **Replicate the main out-of-sample R² results** by maturity and method, against the Cochrane-Piazzesi and yields-only benchmarks
  - **Replicate the economic-value results**: the utility gains and portfolio performance for a mean-variance bond investor
  - **Replicate the variable-importance evidence**: which macro groups the models actually use
  - **Reconcile against the corrigendum**: state which set of published numbers your pipeline matches, and diagnose the difference
  - **Update**: extend through 2026, which now includes the 2022 bond drawdown—by a wide margin the hardest out-of-sample period in the modern data
- **Data sources**: CRSP Treasury (Fama-Bliss discount bond returns) on WRDS, and the Gürkaynak-Sack-Wright curve from the Federal Reserve (free)—you built the GSW machinery in HW 2; FRED-MD for the macro panel (free); Ludvigson and Ng's factors, posted on Sydney Ludvigson's website
- **Notes**:
  - Read the corrigendum before you write any code, not after.
  - Reuse HW 2's yield-curve work rather than rebuilding it. The novelty here is the forecasting layer, and the report should be judged on that.
- **Citation**: Bianchi, Daniele, Matthias Büchner, and Andrea Tamoni. "Bond Risk Premiums with Machine Learning." The Review of Financial Studies 34, no. 2 (2021): 1046-1089. https://doi.org/10.1093/rfs/hhaa062. Corrigendum: The Review of Financial Studies 34, no. 2 (2021): 1090-1103. https://doi.org/10.1093/rfs/hhaa098

### 16. [Noise as Information for Illiquidity](https://doi.org/10.1111/jofi.12083)

When arbitrage capital is plentiful, Treasury prices line up along a smooth yield curve because somebody is paid to make them. When capital withdraws, individual bonds drift away from the curve and stay there. This paper turns that observation into a market-wide illiquidity measure: fit a smooth curve to Treasury prices every day, and take the root-mean-squared pricing error as "noise." The series is quiet for years and then explodes in 1998, 2008, and every episode since, and it prices hedge fund and currency-carry returns in the cross-section.

The curve-fitting step is HW 2's machinery pointed at a different question, which makes this an efficient project with a high bar: the replication starts from something you already have, so the asset-pricing half is where the group has to earn its grade.

- **Tasks**:
  - **Build the daily Treasury panel and fit the curve**: CUSIP-level Treasury quotes with the paper's filters, a Svensson fit each day, and the resulting noise series, reproducing **Figure 1 (example yield curves against market-observed yields)** (this is the data-cleaning deliverable)
  - **Replicate Figure 2 (the daily noise measure, in basis points)** and **Figure 3 (the 2008-2009 episode in detail)**
  - **Replicate the summary statistics and the comparison with other illiquidity measures** (VIX, the on-the-run premium, TED spread, and the Pastor-Stambaugh measure)
  - **Replicate the hedge fund results**, including **Figure 4 (exit rates of noise-beta-sorted funds)**, and the currency-carry cross-section
  - **Update**: extend through 2026, which adds March 2020 and the March 2023 bank stress—both periods when the measure should light up, and a genuine out-of-sample test of whether it still does
- **Data sources**: the CRSP US Treasury Database on WRDS for daily CUSIP-level quotes; Lipper TASS on WRDS for hedge fund returns; FRED for VIX, TED, and rates; Kenneth French and Lubos Pastor's data for the benchmark factors
- **Notes**:
  - Because HW 2 gives you the curve fitting, the replication core here is cheaper than on most projects. The expectation is correspondingly higher on the asset-pricing side and on the update.
  - Bond selection filters (bills in or out, on-the-run treatment, maturity screens) move the level of the noise measure materially. Make them explicit, test them, and show the sensitivity.
- **Citation**: Hu, Grace Xing, Jun Pan, and Jiang Wang. "Noise as Information for Illiquidity." The Journal of Finance 68, no. 6 (2013): 2341-2382. https://doi.org/10.1111/jofi.12083 (free copy: https://en.saif.sjtu.edu.cn/junpan/HuPanWang.pdf)

## Equity Term Structures

### 17. [Equity Term Structures without Dividend Strips Data](https://ssrn.com/abstract=3533486)

Traded dividend strips directly reveal the term structure of equity risk premia, but the data only start around 2004 and cover few assets. This paper estimates "a rich affine model of equity prices, dividends, returns, and their dynamics" from a large cross-section of anomaly portfolios and shows that the model "prices dividend strips of the market and equity portfolios without using strips data in the estimation." The result is a synthetic equity term structure extending back to the 1970s.

The data work is a classic cross-sectional asset pricing pipeline: build 102 value-weighted portfolios (terciles on 51 characteristics) from CRSP and Compustat following Kozak, Nagel, and Santosh (2020), compute portfolio dividend yields, and extract principal components. The author posts the estimation code and supporting data at https://github.com/serhiykozak/EquityTS, so you can validate each step of your pipeline.

- **Tasks**:
  - **Replicate Table I (variance explained by anomaly PCs)**: the factor structure of the 51 long-short anomaly portfolios and the 102 underlying legs
  - **Replicate Figures 1 and 2 (factor yields and factor returns)**: time series of the market and the three PC factors that make up the state vector (this plus Table I is the data-cleaning deliverable)
  - **Replicate Figure 7 (model-implied forward equity yields for the market)**: estimate the model and plot forward equity yields across maturities, 1973–2020
  - **Replicate Figure 8 (term structures conditional on NBER recessions)**: average term structures in recessions versus expansions—the cyclicality result at the heart of the equity term structure literature
  - **Validate** your model-implied yields against the author's posted estimation code and data
- **Data sources**: CRSP and Compustat (WRDS); one-year risk-free rate and Gürkaynak–Sack–Wright Treasury yields (Federal Reserve, free); benchmark data and code from https://github.com/serhiykozak/EquityTS
- **Notes**:
  - Skip Figures 3–6 and Table IV: they compare the model to traded dividend strip and futures data (van Binsbergen–Koijen, Bansal et al.), which is proprietary and not on WRDS. The validation step against the author's posted code plays that role instead.
- **Citation**: Giglio, Stefano, Bryan Kelly, and Serhiy Kozak. "Equity Term Structures without Dividend Strips Data." Journal of Finance 79, no. 6 (2024): 4143–4196. Available at SSRN: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3533486
- **Paper PDF**: [GiglioKellyKozak2024EquityTermStructures.pdf](project_papers/GiglioKellyKozak2024EquityTermStructures.pdf)

### 18. [Equity Duration and Predictability](https://www.sciencedirect.com/science/article/pii/S0304405X25001229)

Why do expected returns—rather than dividend growth—dominate stock price movements in postwar data, when the opposite held for the previous three centuries? This paper's answer is rising equity duration: as firms cut payout ratios after 1945, the market became a longer-duration asset, and "expected returns vary more for payouts further into the future." The authors document this in three datasets: S&P 500 dividend strips (a short-duration version of the market), the aggregate market from 1629 to 2022, and payout-sorted stock portfolios. "Between 1629 and 1945, expected returns explain around 35% of the variation in the dividend-to-price ratio. In the post-1945 period, this increases to 90%."

The method is the same throughout: predictive regressions of returns and dividend growth on the dividend-to-price ratio, with the R²-style decomposition ER + EDG = 1. The authors post their data and code (Mendeley Data, linked from the article), the dividend strip series is on Benjamin Golez's webpage, and the 1629–2015 historical series comes from Golez and Koudijs (2018).

- **Tasks**:
  - **Replicate Tables 1, 4, and 6 (summary statistics)** and **Figure 1 (total payout, 1871–2022)**: the three datasets' summary statistics and the payout-ratio decline that drives the paper (this is the data-cleaning deliverable)
  - **Replicate Table 2 (strips versus market)**: predictive regressions for dividend strips and the market, 1996–2022, showing expected returns matter more for the longer-duration claim
  - **Replicate Table 5 (subperiod predictability, 1629–2022)**: the ER/EDG decomposition across the four historical subperiods—the paper's central result
  - **Replicate Table 7 and Figure 3 (payout-sorted portfolios)**: predictability results for low/medium/high payout portfolios built from CRSP and Compustat, and the summary plot of ER against payout ratios
  - **Skip** Tables 8 and 9 (the calibrated present-value model)
- **Data sources**: paper's data and code package (Mendeley Data); dividend strip series from Benjamin Golez's webpage; Golez–Koudijs (2018) historical series (1629–2015, included in the package); CRSP and Compustat (WRDS) for the cross-section; Davis–Fama–French book equity from Kenneth French's website; S&P 500 total returns from CRSP
- **Notes**:
  - The paper uses Datastream for the S&P 500 price and total return indexes; CRSP's S&P 500 series is an equivalent substitute.
- **Citation**: Golez, Benjamin, and Peter Koudijs. "Equity Duration and Predictability." Journal of Financial Economics 171 (2025): 104114. https://www.sciencedirect.com/science/article/pii/S0304405X25001229
- **Paper PDF**: [GolezKoudijs2025EquityDurationPredictability.pdf](project_papers/GolezKoudijs2025EquityDurationPredictability.pdf) (companion data paper: [GolezKoudijs2018FourCenturiesReturnPredictability.pdf](project_papers/GolezKoudijs2018FourCenturiesReturnPredictability.pdf))

## Macro-Finance and Fiscal Policy

### 19. [Debt and Deficits: Fiscal Analysis with Stationary Ratios](https://personal.lse.ac.uk/martiniw/Debt%20and%20Deficits%20250806.pdf)

This paper asks how governments ultimately deal with a weak fiscal position: do taxes rise, does spending fall, or do the returns to holders of government debt adjust? The debt-GDP ratio is nonstationary in long historical data, so the authors instead construct a stationary "fiscal position" measure—a loglinear combination of tax revenue, government spending, and the market value of government debt. They find that "fiscal adjustment, particularly through changes in spending, is the empirically relevant channel" for resolving weak fiscal positions.

This project involves assembling long historical time series (US 1841–2022, UK 1727–2022) by splicing together data from several public sources—a valuable exercise in data cleaning and documentation. All data is free and publicly available; no WRDS subscriptions are needed. The internet appendix (section IA.1) describes the data sources in detail.

- **Tasks**:
  - **Replicate the summary statistics table** (internet appendix, section IA.5): means, standard deviations, and autocorrelations of debt returns, tax growth, spending growth, and the key ratios, for US and UK
  - **Replicate Figure 1** (debt-GDP ratio in US and UK): the visual demonstration that debt-GDP is nonstationary
  - **Replicate Figure 2** (tax-debt and spending-debt ratios in US and UK): the two ratios are individually nonstationary but cointegrated
  - **Replicate Figure 3** (the fiscal position and surplus-debt ratio in US and UK): construct the paper's loglinear fiscal position measure and show it is stationary
  - **Replicate Table 1** (local projections, US and UK): regressions of cumulative future debt returns, tax growth, and spending growth on the fiscal position—the paper's central result
- **Data sources** (all free and public):
  - US: OMB total receipts, outlays, and interest payments (FRED, since 1901); market value of marketable federal debt from the Federal Reserve Bank of Dallas (from 1942) spliced with [Hall and Sargent (2021)](https://sites.google.com/brandeis.edu/george-j-hall/research) data for earlier years; GDP and GDP deflator from BEA and [MeasuringWorth](https://www.measuringworth.com/)
  - UK: [OBR historical public finances database](https://obr.uk/data/) (tax revenue and spending); market value of central government debt from the Bank of England's [A Millennium of Macroeconomic Data](https://www.bankofengland.co.uk/statistics/research-datasets) (1727–2016) spliced with BIS data (2017–2022); UK GDP and deflator from MeasuringWorth
- **Citation**: Campbell, John Y., Can Gao, and Ian W. R. Martin. "Debt and Deficits: Fiscal Analysis with Stationary Ratios." Working paper, August 2025. https://personal.lse.ac.uk/martiniw/Debt%20and%20Deficits%20250806.pdf
- **Paper PDF**: [CampbellGaoMartin2025DebtAndDeficits.pdf](project_papers/CampbellGaoMartin2025DebtAndDeficits.pdf)

## Daily Cash Flow News

### 20. [Cash Flow News and Stock Price Dynamics](https://doi.org/10.1111/jofi.12901)

On any given day, anywhere from zero to more than 100 firms announce dividends, making daily cash flow news extremely lumpy. This paper builds a daily "bottom-up" measure of aggregate dividend growth from firm-level dividend *announcements*—matching each firm's declared dividend to its own announcement in the same fiscal quarter one year earlier—and decomposes it into "a persistent component, jumps, and temporary shocks." The persistent component turns out to be "a highly significant predictor of future growth in dividends and consumption," leading measures of the state of the economy by a substantial margin.

The pipeline: construct the daily dividend growth series from CRSP declaration dates (dollar-weighted, firm-matched, year-over-year), extract a smooth persistent growth component from the very noisy daily series with a state-space model, then run predictive regressions of dividend growth, GDP growth, and consumption growth on the extracted component. The data construction is the heart of the project; the filtering step can be done with a simplified model (see notes).

- **Tasks**:
  - **Replicate Figures 1 and 2 (the daily dividend growth series)**: the 2014Q2 announcement panels (number of announcers, total dollar dividends, daily growth rate) and the bottom-up versus top-down comparison over 1973–2016 (this is the data-cleaning deliverable)
  - **Replicate Figure 3 (the persistent dividend growth component)**: first estimate the no-jump model (equations (4) and (11))—a linear-Gaussian state-space model—by Kalman filter and maximum likelihood to reproduce the spiky top panel; then extract the smooth bottom-panel series using an outlier-robust variant (see notes). The benchmark is qualitative: a smooth series ranging from roughly 0 to 0.15 that dips negative only during 2008 to 2009 and rebounds in 2009 to 2010
  - **Replicate Table II, Panel A (dividend growth predictability)**: quarterly and annual predictive regressions of CRSP dividend growth on the lagged persistent component, the dividend-price ratio, and lagged dividend growth, with Newey–West standard errors
  - **Replicate Table IV, Panel A (GDP and consumption growth)**: quarterly predictive regressions of macro growth on the persistent dividend growth component
  - **Stretch goal (optional)**: implement the paper's full Gibbs sampler (Internet Appendix Section I) with stochastic volatility and jumps, and replicate the 1973–2016 columns of Table I
- **Data sources**: CRSP distribution events (`crsp.dsedist`; declaration dates `dclrdt`, ordinary cash dividends are distribution codes below 2000), daily stock file (`crsp.dsf`, for shares outstanding and prices at announcement), and daily index file (`crsp.dsi`, `vwretd`/`vwretx`, for the top-down measure and the dividend-price ratio), all on WRDS; FRED (real GDP); BEA NIPA Table 2.3.5 (nondurables plus services consumption)
- **Notes**:
  - CRSP no longer provides declaration dates before 1962 (the paper's footnote 10—its pre-1964 observations come from an older CRSP vintage), so replicate the 1973–2016 sample, which is the paper's main focus. Checkpoints for your Figure 1: on April 24, 2014, roughly $7.1bn of dividends were announced, and the lone June 22, 2014 announcer is Costco ($155.6m versus $135.3m a year before, gross growth 1.15).
  - The dollar dividend is the declared dividend per share times shares outstanding on the announcement day. Follow the paper's filters: share codes 10/11, NYSE/AMEX/NASDAQ, valid price and shares outstanding, one dividend per firm per day (aggregate duplicates).
  - You are not required to implement the paper's full Bayesian model. For the bottom panel of Figure 3, any documented outlier-robust filter is acceptable—for example, Student-t measurement errors in the state-space model, or winsorizing days with few announcing firms before applying the Kalman smoother. For Tables II and IV, the standard for success is matching the sign and statistical significance of the coefficients on the persistent component, not the exact magnitudes, since your extracted series will differ from the paper's.
  - Skip Tables VI through IX (the present value model and same-day return dynamics): their results depend on daily innovations from the full jump model, and Table VIII additionally requires re-estimating the model weekly over 30 years.
- **Citation**: Pettenuzzo, Davide, Riccardo Sabbatucci, and Allan Timmermann. "Cash Flow News and Stock Price Dynamics." The Journal of Finance 75, no. 4 (2020): 2221–2270. https://doi.org/10.1111/jofi.12901
- **Paper PDF**: [Pettenuzzo_et_al_2020_Cash_Flow_News_and_Stock_Price_Dynamics.pdf](project_papers/Pettenuzzo_et_al_2020_Cash_Flow_News_and_Stock_Price_Dynamics.pdf)
