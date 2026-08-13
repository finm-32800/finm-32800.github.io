# Week 9: Medium-Sized Data and Polars

```{toctree}
:maxdepth: 1

Week8/medium_sized_data_strategies.md
Week8/polars_exercises.md
Week8/remote_machines_and_hpc.md
Week8/exercise_jupyter_on_midway.md
notebooks/_01_data_sources_overview_ipynb.ipynb
notebooks/_02_trace_cleaning_walkthrough_ipynb.ipynb
```

## Agenda

- Final project proposal presentations: Ashish + Omar, Piyush, Ahmad + Jeffrey
- Medium-sized data strategies and Polars
- Introduction to TRACE: data sources overview and the Clean TRACE cleaning walkthrough
- Launch the reworked [HW 4](HW4.md): deploy a live, self-updating FedWatch monitor (due Sunday, August 23)
- Final project logistics: rubric walkthrough, oral defense expectations, and signing up for a final presentation time (all groups must present by August 21)

## Learning Outcomes

- Understand strategies for working with medium-sized datasets (1GB-100GB)
- Compare Pandas and Polars for data processing at scale
- Understand lazy evaluation, predicate pushdown, streaming, and Hive partitioning
- Introduction to TRACE corporate bond data
- Understand why data pipelines must decouple internet-dependent pulls from processing

## Make-Up Material

Due to RCC access issues, the discussion of remote machines and HPC---including
[Remote Machines and HPC](Week8/remote_machines_and_hpc.md) and the
[Exercise: Jupyter on Midway](Week8/exercise_jupyter_on_midway.md)---is
postponed. We will cover this material in a make-up session at a later date.
Relatedly, [Homework 5](HW5.md), which runs the Clean TRACE pipeline on RCC, is
now **optional**.

When we cover this material, you will:

- Connect to remote machines via SSH and transfer files with rsync
- Understand HPC cluster architecture (login nodes, compute nodes, storage)
- Submit and manage jobs with SLURM (sinteractive, sbatch)
- Set up SSH port forwarding to access Jupyter notebooks on remote compute nodes
