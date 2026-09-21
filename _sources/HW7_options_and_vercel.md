# Homework 5

## Learning Outcomes

 - Learn to pull options data from WRDS
 - Learn to deploy a Sphinx site to Vercel
 - Learn to protect a Vercel site using Clerk

## Assignment

**Link to Assignment:** TBD

This homework focuses on understanding and implementing SPX hedging with options.
For this homework, you simply need to do the following:

1. Complete the SQL query in `src/pull_options_data.py` to pull the SPX options data from WRDS.
Completeing this query should solve the tests in `src/test_spx_hedging_functions.py`.

2. Take the resulting Sphinx site generated in `docs/` and deploy it to Vercel, and protect it from unauthenticated and unauthorized access using Clerk. You can do this using Example 7 from my simple_auth repo here: https://github.com/jmbejara/simple_auth/tree/main/ex7_sphinx_auth

3. Add one of your classmates as an authorized user to your Vercel project.

For more information on how to use the OptionMetrics data, see the following resources:

- https://wrds-www.wharton.upenn.edu/pages/support/manuals-and-overviews/optionmetrics/wrds-overview-optionmetrics/
- https://wrds-www.wharton.upenn.edu/data-dictionary/optionm_all/opprcd2023/
- https://wrds-www.wharton.upenn.edu/data-dictionary/optionm_all/secprd1996/
- https://wrds-www.wharton.upenn.edu/pages/get-data/optionmetrics/ivy-db-us/options/option-prices/
- https://wrds-www.wharton.upenn.edu/data-dictionary/frb_all/rates_daily/
