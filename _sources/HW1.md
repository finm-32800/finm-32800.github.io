# Homework 1

```{toctree}
:maxdepth: 1
notebooks/_02_CRSP_market_index_ipynb.ipynb
notebooks/_03_SP500_constituents_and_index_ipynb.ipynb
```

- **Due Date:** Friday, July 10, 2026 at 11:59 pm CT.
- **Link to Assignment:** TBD


## Assignment

### Part 0 (not graded)

Please watch the following videos to better familiarize yourself with CRSP and Compustat in WRDS.

 - Watch the following videos about WRDS and CRSP
   - [WRDS Web Queries](https://player.vimeo.com/video/1019787855?h=83ee06d532) While we will be automating the query process using the WRDS Python package [`wrds`](https://pypi.org/project/wrds/), using the web query system is a good way for initial exploration of the data.
   - [CRSP Coverage](https://vimeo.com/417302309)
   - [CRSP - Useful Variale](https://wrds-www.wharton.upenn.edu/pages/grid-items/crsp-useful-variables/). This video goes over some points we made in class as well and is helpful for cleaning the CRSP data (e.g., negative prices).
   - [CRSP Stock Database Coverage](https://wrds-www.wharton.upenn.edu/pages/grid-items/crsp-stock-database-structure/) Useful for merging stock files and event files in CRSP via SQL. This is useful, for example, to incorporate delisting returns.


### Part 1 

Please continue to master the many features of Github by completing the following tutorials from the [GitHub Skills](https://skills.github.com/) page. Please make sure to use **public repositories** for this in your own GitHub user account. You will provide a link in your homework submission.

 - [Review pull requests](https://github.com/skills/review-pull-requests)
 - [Resolve merge conflicts](https://github.com/skills/resolve-merge-conflicts)


### Part 2

This will include a task of replicating the CRSP market index example from the lecture as well as a task of replicating the S&P 500 index from its constituent companies. 

To complete this assignment, please read the two HW guides here:

- [HW Guide Part A: CRSP Market Returns Indices](notebooks/_02_CRSP_market_index_ipynb.ipynb)
- [HW Guide Part B: Reconstructing the S&P 500 Index](notebooks/_03_SP500_constituents_and_index_ipynb.ipynb)

Recall that the way to go about this assignment is to run `pytest` and then determine how to write the code to pass the tests. As a tip, make sure to run the `doit` command before running `pytest`. This will ensure that all the data is pulled before running the tests.

Note, in order to complete this assignment, you will need to have the `wrds` package installed and you will need to have created a `.pgpass` file so that you can automatically authenticate to WRDS to pull the required data. Please refer to the textbook page [Example: Connecting to the WRDS Platform With Python](notebooks/_01_wrds_python_package_ipynb.ipynb) for more information, specifically the section "Creating a .pgpass file".

