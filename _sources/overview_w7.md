# Week 7: Medium-sized Data and Working with Remotes

```{toctree}
:maxdepth: 1
Week1/collaborative_report_and_style_guides.md
Week6/GitHub_pull_requests.md
notebooks/_01_data_sources_overview_ipynb.ipynb
notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb
Week8/medium_sized_data_strategies.md
Week8/polars_exercises.md
Week8/remote_machines_and_hpc.md
Week8/exercise_jupyter_on_midway.md
Week8/optional_lab_clean_trace_on_midway.md
Week7/study_guide.md
```

We start by finishing week 6: the pins demo, HW 4 from claim to merge, and the
report's style guides. Then one dataset carries the rest of the day.

TRACE records every U.S. corporate bond trade. The raw feed is full of
cancellations, corrections, and reversals, and the full history is tens of
gigabytes. Cleaning it takes hours. That makes it a medium-data problem first:
Parquet, Polars, and reading only the columns and rows you need. Then it is a
remote-machine problem: the work runs on Midway3, the university's cluster,
which you reach over SSH and edit with VS Code in a browser. Last, it is a
scheduling problem. On a shared cluster you do not start a long job yourself.
You ask the scheduler, SLURM, for a machine, and it runs the job when one is
free.

## Announcements

- **[HW 3](./HW3.md) is due today at 9:30 am,** the start of class.
- **[HW 4](./HW4.md), the class report, has more time.** It is now due
  **Tuesday, November 24, at 9:30 am,** and your pull request should be
  open by **Tuesday, November 17,** the week 8 session, so your two
  reviewers have a week. If you have not accepted your invite to the report
  repository, do it today: the invites sent on November 9 expire around
  November 16. If yours never arrived, tell me in class.
- **You should have access to UChicago RCC** To access the RCC clusters, you need your CNetID
  password and Duo. Your account is on the course allocation; if
  `ssh <cnetid>@midway3.rcc.uchicago.edu` fails, we fix it after class.
- **The exam is Tuesday, December 1,** covering weeks 0 through 8, today
  included. See [Exam Preparation](./exam_prep.md).
- **Project consultations end this week,** on Friday, November 13. If your
  group has not had one, book it today: [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile).

## Objectives

- Take an exhibit for HW 4 from claim to merge: branch, pin, pull request,
  two reviews, and the report's style guides.
- Explain the major judgment calls in cleaning TRACE and why careful
  researchers produce different "clean" datasets.
- Choose a strategy for data between 1 GB and 100 GB: use less of it, stream
  it, or move to a bigger machine. Explain lazy evaluation, predicate and
  projection pushdown, and Hive partitioning in Polars.
- Connect to Midway3 and the WRDS Cloud over SSH, keep an SSH config, and
  move files with `rsync`.
- Describe a cluster: login nodes, compute nodes, home, project, and scratch
  storage, and why you never compute on a login node.
- Run VS Code against a remote machine: `scode` on Midway3, and a VS Code
  tunnel to a machine you control.
- Submit, monitor, and cancel SLURM jobs, interactive and batch, including a
  job array, and say what the same commands are called under Grid Engine on
  the WRDS Cloud.

## Agenda

1. **Finishing week 6: the pins demo**
   ([walkthrough](./Week6/pins_walkthrough.md)): hashes by hand, a live git
   dependency, `pin` versus `update`, and rebuilding an upstream on a later
   day to get a different hash.
2. **HW 4 from claim to merge** ([HW 4](./HW4.md),
   [Issues and Pull Requests](./Week6/GitHub_pull_requests.md)): claim an
   issue, branch, pin, open the pull request with `fixes #N`, two reviews,
   and why `main` is protected.
3. **The report's style guides**
   ([The Collaborative Report and Style Guides](./Week1/collaborative_report_and_style_guides.md)):
   the code, the write-up, and the chart, and the published reports they
   borrow from.
4. **The case study: cleaning TRACE**
   ([Data Sources Overview](notebooks/_01_data_sources_overview_ipynb.ipynb),
   [Cleaning TRACE](notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb)).
   Cancellations, corrections, and reversals; agency double counts; prices
   that cannot be right. Each filter is a judgment call.
   [Dickerson, Robotti, and Rossetti (2026)](https://arxiv.org/abs/2604.07880)
   show that corporate bond factor results move with those calls. When
   "clean" is a convention, the defense is a convention that is documented,
   tested, and rerunnable.
5. **Medium data, on TRACE**
   ([Medium-Sized Data Strategies](./Week8/medium_sized_data_strategies.md),
   [Polars Exercises](./Week8/polars_exercises.md)): the three escapes;
   Parquet as a columnar format; Polars lazy scans and query plans; the
   Hive-partitioned layout the TRACE pipeline writes.
6. **Remote machines**
   ([Remote Machines and HPC](./Week8/remote_machines_and_hpc.md)): SSH and
   Duo, an SSH config, login versus compute nodes, home versus project versus
   scratch, `rsync` to bring results back, and the WRDS Cloud.
7. **VS Code on someone else's machine:** `scode` on Midway3, which runs VS
   Code on a compute node and reaches it through an SSH tunnel
   ([exercise](./Week8/exercise_jupyter_on_midway.md)); then a VS Code
   tunnel to a machine you control.
8. **SLURM** ([Remote Machines and HPC](./Week8/remote_machines_and_hpc.md)):
   who asks for it in job postings; partitions; `sinteractive`, `sbatch`,
   `squeue`, `scancel`; a job array everyone submits; then the TRACE pipeline
   as a batch job
   ([optional lab](./Week8/optional_lab_clean_trace_on_midway.md)).

**Next week:** packaging, CI, and code quality, and the paper of Bernanke and
Kuttner (2005).
