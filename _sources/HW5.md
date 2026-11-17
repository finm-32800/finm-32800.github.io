# Homework 5

**Due: Friday, December 11, at 11:59 pm.** 4 points.

This assignment is short on purpose. The exam is on December 1 and your final
project is due the week after, and that is where your time should go. You can
finish it in class in week 8.

You do two things on Midway3 and take a screenshot of each:

- **Part A (2 points).** Submit a batch job with `sbatch`: a job array that
  cleans three months of corporate bond trades, February through April 2020,
  as six tasks at the same time.
- **Part B (2 points).** Open VS Code on a compute node with `scode`, in the
  browser on your laptop.

You get the points for doing each one and showing it. The job does not have
to finish without an error.

The steps are in the `README.md` of your copy of the repository,
`hw-finm-32800/hw5-<your GitHub username>`. To submit, commit the two
screenshots to the repository's `screenshots` folder and set two flags and
two file paths in `src/hw5_submission.py`. The autograder checks that the
flags are set and that the image files are there. I look at each screenshot
by hand.

## What to Read

- [Parallel Jobs on a Cluster](./Week8/parallel_jobs_on_a_cluster.md): what a
  job array is, and how the script you submit hands each task its month.
- [Remote Machines and HPC](./Week8/remote_machines_and_hpc.md): logging in,
  the scheduler, and *VS Code on a Compute Node*, which explains the `ssh -L`
  command that `scode` asks you to run.
- [Corporate Bonds in March 2020](./notebooks/_03_corporate_bonds_march_2020_ipynb.ipynb):
  what the data you are cleaning is used for.

## What You Should Be Able To Do Afterward

- Log in to a cluster, submit a batch job, and find out what happened to it
  with `squeue` and `sacct`.
- Read a job array script and say what each task runs.
- Open an editor on a compute node through an SSH tunnel.
