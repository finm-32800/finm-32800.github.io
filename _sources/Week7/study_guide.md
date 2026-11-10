# Study Guide: Week 7

Open-ended questions on the material from week 7. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Issues and Pull Requests

*Read: [GitHub Issues and Pull Requests](../Week6/GitHub_pull_requests.md).*

1. What is a pull request? What is the difference between pushing a commit
    to `main` and opening a pull request?
2. `git pull` and a pull request sound alike. What does each one do?
3. What is a GitHub issue, and what does writing `fixes #123` in a pull
    request do?
4. Why do maintainers protect `main` and require a review before merging?

## Cleaning TRACE

*Read: [Data Sources Overview](../notebooks/_01_data_sources_overview_ipynb.ipynb)
and [Cleaning TRACE Corporate Bond Data](../notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb).*

1. What does TRACE record, and why does its raw feed contain cancellations,
    corrections, and reversals?
2. What is an agency double count, and what goes wrong in a volume or
    liquidity measure if it is left in?
3. Two careful researchers clean the same TRACE feed and get different
    datasets. Why is that possible, and what makes either result
    defensible?

## Medium Data and Polars

*Read: [Medium-Sized Data Strategies](../Week8/medium_sized_data_strategies.md)
and [Polars Exercises](../Week8/polars_exercises.md).*

1. Name the three ways out when data does not fit in memory. Which one solves
    most problems?
2. What is the difference between eager and lazy evaluation in Polars?
3. What do predicate pushdown and projection pushdown each skip reading in
    the query below?

    ```python
    (pl.scan_parquet("trace/*.parquet")
       .filter(pl.col("trd_exctn_dt") >= date(2020, 1, 1))
       .select(["cusip_id", "rptd_pr"])
       .collect())
    ```

4. A dataset is stored as `trace/year=2019/...`, `trace/year=2020/...`, and so
    on. What is this layout called, and what does a filter on `year` save?
5. What does `collect(engine="streaming")` or `sink_parquet` buy you that
    `collect()` does not?

## Remote Machines

*Read: [Remote Machines and High-Performance Computing](../Week8/remote_machines_and_hpc.md).*

1. What is the difference between a login node and a compute node? Why must
    you not run a long computation on a login node?
2. On Midway3, what belongs in home, in project, and in scratch?
3. Most compute nodes have no internet access. How does the TRACE pipeline
    still pull from WRDS, and what does separating pulls from processing buy?
4. What does each of `-a`, `-v`, and `-z` do in `rsync -avz`, and why is
    `rsync` better than `scp` for a directory you sync repeatedly?
5. What does `ssh -N -f -L 8888:10.50.1.2:8888 midway3` do?
6. What do `ControlMaster`, `ControlPath`, and `ControlPersist` in an SSH
    config buy you on a cluster that asks for Duo?
7. `scode`, Remote-SSH, and `code tunnel` all put VS Code on a remote machine.
    Where does the server run in each, and why does `code tunnel` not work on
    a Midway3 compute node?

## SLURM and Grid Engine

*Read: [Remote Machines and High-Performance Computing](../Week8/remote_machines_and_hpc.md).*

1. What does a job scheduler do, in four steps?
2. What is the difference between `sinteractive` and `sbatch`?
3. What does each line of this script ask for?

    ```bash
    #SBATCH --partition=caslake
    #SBATCH --time=00:02:00
    #SBATCH --mem=1G
    #SBATCH --array=1-5
    #SBATCH --output=%x_%A_%a.out
    ```

4. When is a set of tasks a good fit for a job array, and when is it not?
5. What do `squeue`, `scancel`, and `sacct` each tell you or do?
6. Give the Grid Engine command on the WRDS Cloud that matches each of
    `sinteractive`, `sbatch`, `squeue`, and `scancel`.
