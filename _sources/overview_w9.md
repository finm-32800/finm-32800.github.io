# Week 9: Medium-Sized Data and Remote Machines

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

- Medium-sized data strategies and Polars
- Remote machines and HPC
- Go over Homework 5: running the Clean TRACE pipeline on RCC

## Learning Outcomes

- Understand strategies for working with medium-sized datasets (1GB-100GB)
- Compare Pandas and Polars for data processing at scale
- Understand lazy evaluation, predicate pushdown, streaming, and Hive partitioning
- Introduction to TRACE corporate bond data
- Connect to remote machines via SSH and transfer files with rsync
- Understand HPC cluster architecture (login nodes, compute nodes, storage)
- Submit and manage jobs with SLURM (sinteractive, sbatch)
- Understand why data pipelines must decouple internet-dependent pulls from processing
- Set up SSH port forwarding to access Jupyter notebooks on remote compute nodes
