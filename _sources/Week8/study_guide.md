# Study Guide: Week 8

Open-ended questions on the material from week 8. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Pins That a Rebuild Can Meet

*Read: [Pins That a Rebuild Can Meet](./pins_that_a_rebuild_can_meet.md).*

1. In HW 4 the report's lockfile recorded a hash of the bytes of each
    upstream file. A classmate checked out the same upstream commit a week
    later, rebuilt it, and got a table whose hash did not match. No code had
    changed. What had?
2. Why can no rebuild reproduce the bytes of a Plotly HTML chart or of an
    executed notebook, even from the same code and the same data?
3. A pin by bytes works when a team shares one store, and fails when each
    person rebuilds the upstream. Why?
4. Suppose a table is pinned by a hash of its rows up to the pin's last date,
    and not by the bytes of its file. For each change below, say whether the
    pin is still met, and why.

    - The source has published two new days of data.
    - The same rows were written in another order, by another version of the
      Parquet writer.
    - The source revised a value from last year.

## Corporate Bonds in March 2020

*Read: [Corporate Bonds in March 2020](../notebooks/_03_corporate_bonds_march_2020_ipynb.ipynb).*

5. What is a credit spread? When a bond's credit spread widens and Treasury
    yields do not move, what has happened to the bond's price?
6. What happened to investment-grade and high-yield credit spreads between
    late February and March 20, 2020?
7. What did the Federal Reserve announce on March 23, 2020? Its first
    purchases came weeks later. What does the timing of the fall in spreads
    say about how the announcement worked?
8. The figure of daily spreads and trade counts is drawn from cleaned TRACE.
    Name one way the same figure drawn from the raw feed would be wrong.
9. In most crises investors buy Treasuries, and Treasury yields fall. In the
    dash for cash of March 2020 they sold Treasuries too. Why does that point
    to a need for cash rather than fear of default, and how does it fit with
    the safest corporate bonds' spreads rising the most in proportion?

## Port Forwarding and VS Code

*Read: [Remote Machines and High-Performance Computing](./remote_machines_and_hpc.md).*

10. VS Code is running on a compute node of Midway3 and listening on a port.
    Why can the browser on your laptop not open it directly?
11. In the command below, what does each of `8000`, `midway3-0123`, and
    `41234` name? Which machine looks up the name `midway3-0123`?

    ```bash
    ssh -L 8000:midway3-0123:41234 cnetid@midway3.rcc.uchicago.edu
    ```

12. With that command running, you open `http://localhost:8000` in a browser.
    Trace the request from the browser to VS Code, machine by machine.
13. `scode` starts VS Code inside a SLURM job. What happens to your editor
    when the job's time limit runs out, and what happens to the files you
    saved?

## Parallel Jobs: GNU Parallel and Job Arrays

*Read: [Parallel Jobs on a Cluster](./parallel_jobs_on_a_cluster.md).*

14. What makes a set of tasks independent enough to run in parallel? Stage 0
    of the TRACE pipeline can be split by month. Why?
15. What does the command below run, and how many of them at a time?

    ```bash
    parallel -j 2 python clean_month.py {} ::: 2020-02 2020-03 2020-04
    ```

16. What does GNU Parallel write to the file named by `--joblog`, and what
    does `--resume` do with it on the next run?
17. How many jobs does the script below create, and what does each one run?

    ```bash
    #SBATCH --array=0-2
    MONTHS=(2020-02 2020-03 2020-04)
    python clean_month.py ${MONTHS[$SLURM_ARRAY_TASK_ID]}
    ```

18. After submitting that script as job 12345, `sacct -j 12345` shows
    `12345_1` as `FAILED` and the other two as `COMPLETED`. What command
    reruns only the task that failed?
19. A task that exits at once when its output file already exists is called
    idempotent. How does that make rerunning a whole array cheap?
20. What does the `%10` do in `#SBATCH --array=0-99%10`?
21. The scheduler sees a job array as many jobs and a GNU Parallel run as one
    job. What does that difference mean for when the work starts and for how
    you find out which task failed?
22. When is one job that runs GNU Parallel a better choice than a job array?
