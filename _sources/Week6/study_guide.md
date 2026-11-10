# Study Guide: Week 6

Open-ended questions on the material from week 6. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Tests and Data Validation

*Read: [Unit Tests](../Week5/unit_tests.md),
[Data Validation](../Week5/data_validation.md), and Agenda Item 2 of the
[week 6 chapter](../overview_w6.md).*

1. A unit test and a plausibility check can both stop a pipeline. What does
   each one check?
2. A daily job pulls a series, checks it, and publishes a page. Why should
   the checks run before the page is published, and what should a failed
   check leave on the site?

19. A pipeline's unit tests and plausibility checks all pass, and the number
    it publishes is wrong. How can that happen, and what kind of check would
    have caught it?
20. You reconcile your series against a second source that was computed
    from the same inputs. What can that comparison catch, and what can it
    not?
21. Give one data validation failure that should stop a pipeline from
    publishing and one that should only be flagged. What makes the
    difference?


## The FTSFR Benchmark

*Read: [The Financial Time Series Forecasting Repository](../Week3/ftsfr.md)
and [Bejarano et al. (2026)](https://www.financialresearch.gov/working-papers/2026/08/25/time-series-forecasting-methods-financial-markets/).*

3. Two studies compare the same forecasting methods on what both call
   corporate bond returns, and they rank the methods differently. Other than
   the methods, what can explain the difference?
4. Much of the data in a financial forecasting benchmark comes from licensed
   databases such as CRSP and OptionMetrics. Why does such a benchmark
   publish the code that builds each dataset rather than the datasets?
5. A benchmark builds corporate bond portfolios twice from the same source
   data, changing only how the sorting signal is cleaned, and the two sets of
   portfolio returns are 98 to 99 percent correlated. The same forecasting
   models are run on both. What happens to the ranking of the models?
6. If a ranking of forecasting methods can change when only the cleaning
   changes, why must a published comparison record the exact version of the
   data it used?


## Hashes, Snapshots, and Pins

*Read: [ChartBook: Know Where Every Number Came From](./chartbook_catalog.md) and Agenda Items 4
and 5 of the [week 6 chapter](../overview_w6.md).*

7. What is a content hash of a file, and what happens to it when one byte of
   the file changes?
8. Git names each commit by a hash computed from the hashes of its files and
   the hash of its parent commit. Why does editing a file in an old commit
   change the hash of every commit after it?
9. A lockfile records, for each dependency, one hash computed from the
   hashes of every asset pinned from it. Why is that one hash enough to tell
   whether anything pinned from the dependency has changed?
10. What is the difference between reading another project's working folder
   and reading a snapshot of it?
11. `chartbook pin` records which snapshot of a dependency a project reads,
   and never moves a pin that is already there; `chartbook update` moves it.
   Why keep the two commands separate?
12. A pull request's diff of `chartbook.lock` includes these lines, under
   the record for `ftsfr/fed_yield_curve`. What does the change say
   happened, and which command made it?

    ```diff
    -          "snapshot": "2026-11-02T05:22:19Z",
    -          "pinned_hash": "sha256:aff4…"
    +          "snapshot": "2026-11-09T05:20:41Z",
    +          "pinned_hash": "sha256:51e0…"
    ...
    -      "content_hash": "sha256:b63d…"
    +      "content_hash": "sha256:bc10…"
    ```

13. You are editing an upstream pipeline and the report that reads it, on
   the same machine. The report reads the upstream's snapshot in your store,
   not the upstream's working folder. What could go wrong if it read the
   folder, and what does the store give you instead?


## Lineage and Provenance

*Read: [ChartBook: Know Where Every Number Came From](./chartbook_catalog.md).*

14. What is the difference between the lineage of a chart and its
   provenance?


## Task Runners, Orchestrators, and Catalogs

*Read: Agenda Item 4 of the [week 6 chapter](../overview_w6.md).*

15. `doit`, Airflow, and ChartBook each answer a different question about a
   pipeline. What question does each one answer?
16. A task that appends the day's rows to a file every time it runs is not
   idempotent. What goes wrong when a scheduler retries it after a failure?


## Parquet

*Read: [Strategies for Medium-Sized Data](../Week8/medium_sized_data_strategies.md).*

17. Parquet stores a table column by column. Why does reading three columns
   of a hundred-column Parquet file cost little more than the three columns
   themselves?
18. A Parquet file keeps the minimum and maximum of each column for every row
   group. How does a reader use those statistics to skip data when a query
   filters on that column?
