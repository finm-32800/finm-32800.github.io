# Remote Machines and High-Performance Computing

## Why Remote Machines?

In quantitative finance, you will frequently need to work on machines other than
your laptop. Common reasons include:

- **Large datasets**: The TRACE corporate bond dataset exceeds 100 GB. Your laptop
  may not have enough disk space, let alone RAM, to process it.
- **Long-running computations**: Cleaning 20+ years of TRACE data takes 7--10 hours.
  You don't want to tie up your laptop for that long (or worry about it going to
  sleep mid-computation).
- **Shared project storage**: A research team needs a central location where
  everyone can access the same data and code.
- **Specialized hardware**: Some computations benefit from high-memory nodes
  (512 GB+ RAM) or GPUs that aren't available on personal machines.

The solution is to run your code on a **remote machine**---a server or compute
cluster that you connect to over the network.


## SSH: Secure Shell

### What is SSH?

**SSH (Secure Shell)** is the standard protocol for securely connecting to remote
machines over the network. It provides:

- **Encrypted communication**: All data between your laptop and the remote
  machine is encrypted.
- **Authentication**: You prove your identity with a password, SSH key, or
  multi-factor authentication (MFA).
- **Remote terminal**: Once connected, you get a command-line shell on the
  remote machine, just as if you were sitting in front of it.

### Connecting to Midway3

To connect to UChicago's Midway3 cluster:

```bash
ssh <your-cnetid>@midway3.rcc.uchicago.edu
```

You will be prompted for your password, followed by Duo multi-factor
authentication (MFA). After completing MFA, you land on one of the cluster's
**login nodes**.

```{admonition} Login nodes are shared
:class: warning

Login nodes are shared by all users. They are intended for lightweight tasks:
editing files, submitting jobs, transferring data. **Do not run computationally
intensive work on login nodes.** Use the job scheduler (SLURM) to request
dedicated compute resources instead.
```

### SSH Config File

If you connect frequently, you can simplify the command by adding an entry to
your `~/.ssh/config` file:

```text
Host midway3
    HostName midway3.rcc.uchicago.edu
    User <your-cnetid>
```

After this, you can connect with just:

```bash
ssh midway3
```

### One Duo Prompt, Many Connections

Midway3 asks for your password and Duo on every new connection, and so does
every `scp`, `rsync`, and tunnel. SSH can reuse one authenticated connection
for all of them. Add three lines to the same `Host` block:

```text
Host midway3
    HostName midway3.rcc.uchicago.edu
    User <your-cnetid>
    ControlMaster auto
    ControlPath ~/.ssh/cm-%r@%h:%p
    ControlPersist 8h
```

The first `ssh midway3` of the day asks for Duo. For the next eight hours,
every `ssh midway3`, `rsync ... midway3:...`, and `scp` rides on that
connection without asking again. `ssh -O exit midway3` closes it.


## UChicago's Midway3 Cluster

### What is an HPC Cluster?

A high-performance computing (HPC) cluster is a collection of interconnected
computers (called **nodes**) that work together to provide large-scale
computational resources. Unlike your laptop, which has a single processor and
limited memory, a cluster can have thousands of processors and terabytes of RAM
distributed across hundreds of nodes.

```{figure} ./assets/hpc_cluster.jpg
:name: hpc-cluster
:width: 60%

An HPC cluster: rows of interconnected compute nodes in a data center.
*Image: Argonne National Laboratory / Wikimedia Commons*
```

### Midway3 Architecture

UChicago's [Research Computing Center (RCC)](https://rcc.uchicago.edu/) operates
the Midway3 cluster, which has **220+ nodes** with over **10,000 CPU cores**.
The cluster consists of:

- **Login nodes**: Where you land after SSH. Used for file editing, job
  submission, and data transfer. These have internet access.
- **Compute nodes**: Where your jobs actually run. These are allocated by the
  SLURM job scheduler. Most compute partitions have **no internet access**.
- **Storage systems**: Shared filesystems accessible from all nodes.

### Storage Hierarchy

| Location | Path | Quota | Backed Up | Purpose |
|----------|------|-------|-----------|---------|
| Home | `/home/<cnetid>` | 30 GB | Yes | Config files, small scripts |
| Project | `/project/finm32800` | Large (shared) | Yes | Shared course data and code |
| Scratch | `/scratch/midway3/<cnetid>` | Large | **No** | Temporary large files, intermediate results |

```{tip}
Store your code and data in `/project/` or `/scratch/`, not `/home/`.
Store conda/virtual environments in your home directory (`$HOME/envs/`)---they
create many small files that can exhaust the shared project directory's file
count limit.
```

### Module System

Midway uses a **module system** to manage software versions. Instead of
installing software yourself, you load pre-installed modules:

```bash
# See available Python versions
module avail python

# Load a specific Python version
module load python/3.11.9

# See currently loaded modules
module list

# Unload a module
module unload python/3.11.9
```


## SLURM: The Job Scheduler

### Who Asks for It?

The [job postings](../Week0/course_map.md) this course tracks show where
SLURM matters. It is rare across all technical postings: 43 of about 9,200.
But it shows up at 13 of the 63 firms, and those firms are concentrated
where students of this program go. It appears at 3 of 8 proprietary
trading firms (Jump, Tower, DRW), 3 of 10 market makers (IMC, Citadel
Securities, Virtu), and 4 of 16 systematic funds (Voleon, Squarepoint,
WorldQuant, Qube). It appears at none of the 12 asset managers. At a firm
that runs its own cluster, the scheduler is assumed knowledge. The command
line under it is assumed almost everywhere: Bash or shell scripting
appears at 45 of the 63 firms.

### Why a Job Scheduler?

A cluster is shared by hundreds of users. Without coordination, everyone would
try to run their jobs on the same nodes, leading to resource contention and
poor performance. **SLURM** (Simple Linux Utility for Resource Management) is
the job scheduler that manages this:

1. You **request** resources (CPUs, memory, time).
2. SLURM **queues** your request and waits for resources to become available.
3. When resources are free, SLURM **allocates** a compute node and runs your job.
4. When your job finishes (or exceeds its time limit), SLURM **releases** the
   resources for other users.

### Partitions

Compute nodes are organized into **partitions** (groups of nodes with similar
hardware). Key partitions on Midway3:

| Partition | Nodes | Cores/Node | RAM/Node | Max Time | Internet? |
|-----------|-------|------------|----------|----------|-----------|
| `caslake` (default) | 184 | 48 | 192 GB | 36 hours | No |
| `bigmem` | 2 | 48 | 768--1536 GB | 36 hours | No |
| `gpu` | 11 | varies | 192--384 GB | 36 hours | No |
| `build` | shared | varies | varies | **6 hours** | **Yes** |

```{admonition} The `build` partition
:class: note

The `build` partition is special: it is the only partition with **outbound
internet access**. Use it when you need to download data, install packages
from PyPI, or pull from Git repositories. Its 6-hour wall time limit makes it
unsuitable for long-running computations, but it is ideal for setup tasks and
data pulls.
```

### Interactive Sessions with `sinteractive`

For exploratory work, use `sinteractive` to get a shell on a compute node:

```bash
# Basic interactive session (2 hours, default partition)
sinteractive --account=finm32800

# Request more resources
sinteractive --account=finm32800 --partition=caslake \
    --time=04:00:00 --cpus-per-task=4 --mem=32G

# Quick debugging (free, 15 minutes, up to 4 cores)
sinteractive --account=finm32800 --qos=debug --time=00:15:00
```

After running `sinteractive`, you wait in the queue until a node is available.
Once allocated, your terminal prompt changes to show the compute node name
(e.g., `midway3-0042`), and you can run commands directly on that node.

### Batch Jobs with `sbatch`

For long-running or unattended work, write an **sbatch script** and submit it.
Here is a real example from our TRACE data pipeline:

```bash
#!/bin/bash
#SBATCH --job-name=trace-data-pipeline-stage0
#SBATCH --output=%j_trace-data-pipeline-stage0.out
#SBATCH --error=%j_trace-data-pipeline-stage0.err
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=5
#SBATCH --mem-per-cpu=8000
#SBATCH --partition=build
#SBATCH --time=6:00:00
#SBATCH --account=finm32800

# Change to the project directory
cd /home/${USER}/trace-data-pipeline
echo "Changed to trace-data-pipeline directory"

# Activate Python virtual environment
source $HOME/envs/clean_trace/bin/activate
echo "Activated virtual environment"

# Run the pipeline
echo "Executing stage 0 pipeline"
./run_pipeline_stage0.sh
```

Key `#SBATCH` directives:

| Directive | Meaning |
|-----------|---------|
| `--job-name` | Human-readable name for your job |
| `--output` / `--error` | Files for stdout and stderr (`%j` = job ID) |
| `--nodes` | Number of compute nodes |
| `--cpus-per-task` | CPU cores per task |
| `--mem-per-cpu` | Memory per CPU core (in MB) |
| `--partition` | Which partition to use |
| `--time` | Maximum wall time (HH:MM:SS) |
| `--account` | Billing account |

Submit and manage jobs:

```bash
# Submit a batch job
sbatch my_script.sbatch

# Check your jobs in the queue
squeue --user=$USER

# Cancel a job
scancel <job_id>
```

### Job Arrays

Much of the work on a cluster is the same script run on many independent
pieces: one task per year of TRACE, per ticker, per parameter setting. A
**job array** submits all of them at once. SLURM gives each copy its own
`SLURM_ARRAY_TASK_ID`, and the script uses it to choose its piece:

```bash
#!/bin/bash
#SBATCH --job-name=hello-array
#SBATCH --account=finm32800
#SBATCH --partition=caslake
#SBATCH --time=00:02:00
#SBATCH --mem=1G
#SBATCH --array=1-5
#SBATCH --output=%x_%A_%a.out

echo "Task ${SLURM_ARRAY_TASK_ID} of job ${SLURM_ARRAY_JOB_ID} on $(hostname)"
```

`%A` is the array's job ID and `%a` the task's index, so each task writes its
own output file. `squeue --user=$USER` lists the tasks as `<jobid>_1`,
`<jobid>_2`, and so on, and `scancel <jobid>` cancels all of them. The real
version looks like this:

```bash
#SBATCH --array=2015-2024
python src/process_year.py --year ${SLURM_ARRAY_TASK_ID}
```

The tasks must not depend on one another. If year 2020 needs the output of
year 2019, it is not an array; it is a pipeline, and `doit` or an
orchestrator orders it.

After a job finishes, `sacct -j <jobid>` reports how long it ran and how much
memory it used. Read it before the next submission: asking for far more
memory or time than you use makes you wait longer in the queue.

```{admonition} Discussion
:class: note

Notice that the sbatch script above uses `--partition=build`. Why? Because the
TRACE pipeline's stage 0 includes data pulls from WRDS, which requires internet
access. What would you change if the job only needed to process data already
on disk?
```


## The Internet Access Problem

This is one of the most important architectural considerations when working on
an HPC cluster: **most compute nodes have no internet access.**

On Midway3, only the `build` partition and login nodes can reach the outside
internet. This means your code **cannot** download data, install packages, or
access APIs while running on a standard compute partition like `caslake`.

### Decoupling Data Pulls from Processing

The TRACE case study addresses this with a clean architectural separation:

```text
┌─────────────────────────────────────────────────┐
│  Phase 1: Data Pulls (requires internet)        │
│  ─────────────────────────────────────────────── │
│  • pull_trace_enhanced.py  → WRDS               │
│  • pull_fisd.py            → WRDS               │
│  • pull_liu_wu_yields.py   → Google Sheets       │
│  • pull_open_source_bond.py → openbondassetpricing│
│                                                  │
│  Run on: login node or build partition           │
│  Output: _data/pulled/**/*.parquet               │
└──────────────────────┬──────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────┐
│  Phase 2: Processing (no internet needed)       │
│  ─────────────────────────────────────────────── │
│  • build_fisd_universe.py                        │
│  • filter_trace_fisd.py                          │
│  • stage0/clean_trace_local.py                   │
│  • stage1/create_daily_stage1.py                 │
│                                                  │
│  Run on: any partition (caslake, bigmem, etc.)   │
│  Input: _data/pulled/**/*.parquet (local disk)   │
│  Output: _data/stage0/, _data/stage1/            │
└─────────────────────────────────────────────────┘
```

The pull scripts download everything once and write to Hive-partitioned Parquet
files in `_data/pulled/`. The processing scripts then read exclusively from
local disk---no network calls, no WRDS connection, no API requests.

This separation has several benefits:

- **Flexibility**: Processing jobs can run on any partition, not just `build`.
- **Reliability**: No risk of a network timeout crashing your 6-hour job at
  hour 5.
- **Reproducibility**: The pulled data is a snapshot. Rerunning processing on
  the same snapshot produces identical results.
- **Incremental updates**: Pull scripts check which partitions already exist
  on disk and only download new months.

```{tip}
This "pull once, process anywhere" pattern is a good practice for any data
pipeline that runs on machines with restricted internet access. It also makes
your pipeline easier to debug, since you can inspect the raw pulled data
independently of the processing logic.
```


## File Transfer: `rsync` and `scp`

You will frequently need to move files between your laptop and the cluster.

### `scp`: Simple Copies

`scp` (secure copy) works like `cp`, but over SSH:

```bash
# Copy a file TO Midway
scp my_script.py <cnetid>@midway3.rcc.uchicago.edu:/home/<cnetid>/

# Copy a file FROM Midway
scp <cnetid>@midway3.rcc.uchicago.edu:/scratch/midway3/<cnetid>/results.parquet ./

# Copy an entire directory
scp -r my_project/ <cnetid>@midway3.rcc.uchicago.edu:/project/finm32800/<cnetid>/
```

### `rsync`: Smart Synchronization

`rsync` is more powerful than `scp`. It only transfers files that have changed,
making it much faster for repeated syncs:

```bash
# Sync a local project directory to Midway
rsync -avzhe ssh ./my_project/ \
    <cnetid>@midway3.rcc.uchicago.edu:/project/finm32800/<cnetid>/my_project/

# Sync results from Midway back to your laptop
rsync -avzhe ssh \
    <cnetid>@midway3.rcc.uchicago.edu:/scratch/midway3/<cnetid>/results/ \
    ./results/
```

Key `rsync` flags:

| Flag | Meaning |
|------|---------|
| `-a` | Archive mode (preserves permissions, timestamps, symlinks) |
| `-v` | Verbose (show files being transferred) |
| `-z` | Compress data during transfer |
| `-h` | Human-readable file sizes |
| `-e ssh` | Use SSH as the transport |

:::{admonition} rsync vs. scp
:class: tip

**Use `scp`** for quick one-off file transfers.
**Use `rsync`** when you're synchronizing directories repeatedly (e.g., pushing
code updates to the cluster, pulling results back). `rsync` will only transfer
the files that have changed, saving significant time on large directories.
:::

### Globus: Large-Scale Transfers

For very large transfers (tens of gigabytes or more), UChicago's RCC recommends
[Globus](https://www.globus.org/), a managed file transfer service optimized
for research data. It handles retries, parallelism, and integrity checking
automatically. See the
[RCC documentation on data transfer](https://docs.rcc.uchicago.edu/ssh/main/)
for details.


## SSH Port Forwarding

### The Problem

You've started a Jupyter notebook server on a Midway compute node. But that
compute node is deep inside the cluster network---your laptop can't reach it
directly. How do you open the notebook in your browser?

### The Solution: SSH Tunneling

**SSH port forwarding** (also called SSH tunneling) creates a secure tunnel
from a port on your laptop to a port on the remote compute node, routed
through the Midway login node.

```{figure} ./assets/ssh_local_port_forwarding.png
:name: ssh-port-forwarding
:width: 80%

Local port forwarding: your laptop connects to localhost:PORT, and SSH
forwards the traffic through the login node to the compute node where Jupyter
is running.
```

*Image: [iximiuz.com](https://iximiuz.com/en/posts/ssh-tunnels/)*

The command looks like this:

```bash
ssh -N -f -L <local_port>:<compute_node_ip>:<remote_port> \
    <cnetid>@midway3.rcc.uchicago.edu
```

| Flag | Meaning |
|------|---------|
| `-N` | Don't execute a remote command (just forward ports) |
| `-f` | Run in the background |
| `-L` | Local port forwarding: `local_port:remote_host:remote_port` |

After running this, opening `http://127.0.0.1:<local_port>` in your browser
will connect to the Jupyter server on the compute node, tunneled securely
through Midway's login node.

```{note}
We walk through this entire process step-by-step in the
[in-class exercise](./exercise_jupyter_on_midway.md).
```


## VS Code on a Remote Machine

You can edit files on the cluster with `nano` or `vim`, but most people want
their editor. There are three ways to get VS Code onto a remote machine,
and where the server runs is what separates them.

- **`scode` on Midway3.** RCC's `scode` submits a SLURM job that starts VS
  Code on a **compute node** and prints the `ssh -L` command that tunnels it
  to your browser. This is the supported way on Midway3. Follow
  [RCC's instructions](https://docs.rcc.uchicago.edu/software/apps-and-envs/scode/main/),
  which are the source of truth, with `--account finm32800`. The session is
  an ordinary SLURM job: it gets the CPUs and memory you ask for, and it ends
  when its time runs out or when you `scancel` it.
- **Remote-SSH.** The VS Code extension of that name opens a folder over
  SSH, and it starts its server on whatever machine you SSH into. On a
  machine of your own that is the right tool. On Midway3 you land on a
  **login node**, which is for editing and submitting jobs, not for a
  long-running server or computation, so prefer `scode` there.
- **A VS Code tunnel.** `code tunnel` on a remote machine connects *out* to
  Microsoft's relay and registers the machine under your GitHub account. You
  then open it from [vscode.dev](https://vscode.dev) or the Remote - Tunnels
  extension, from anywhere, with no SSH and no open port. Because it needs
  outbound internet for as long as it runs, it does not work on Midway3's
  compute nodes, which have none. It is the tool for a machine you control:
  a workstation at the office, a cloud VM, or a Mac mini in a closet.

  ```bash
  code tunnel --name my-workstation   # first run: log in with GitHub
  ```


## The WRDS Cloud: Same Idea, Different Scheduler

WRDS runs its own cluster, the
[WRDS Cloud](https://wrds-www.wharton.upenn.edu/pages/support/the-wrds-cloud/),
next to the data. A query that pulls a year of TAQ to your laptop can
instead run where TAQ lives and send back only the result. You reach it with
your WRDS account and Duo:

```bash
ssh <wrds-username>@wrds-cloud.wharton.upenn.edu
```

Its scheduler is **Grid Engine**, not SLURM. The concepts are the same, and
the commands map one to one:

| Task | SLURM (Midway3) | Grid Engine (WRDS Cloud) |
|---|---|---|
| Interactive shell on a compute node | `sinteractive` | `qrsh` |
| Submit a batch script | `sbatch job.sh` | `qsub job.sh` |
| Directives inside the script | `#SBATCH ...` | `#$ ...` |
| List your jobs | `squeue --user=$USER` | `qstat` |
| Cancel a job | `scancel <id>` | `qdel <id>` |
| Job array | `--array=1-10` | `-t 1-10` |
| This task's index | `$SLURM_ARRAY_TASK_ID` | `$SGE_TASK_ID` |

The WRDS Cloud is tighter than Midway3. A user gets about ten concurrent
batch jobs and three interactive sessions, a one-week limit per job, a 10 GB
home directory, and scratch space purged after seven days. Pull what you
need, reduce it there, and `rsync` the result home.

Once you can translate between two schedulers, a third one, such as the one
at your employer, takes an afternoon to learn.


## Further Reading

- [RCC User Guide: SSH](https://docs.rcc.uchicago.edu/ssh/main/) --- Connecting to Midway, file transfers
- [RCC User Guide: SLURM](https://docs.rcc.uchicago.edu/slurm/main/) --- Job submission, partitions, resource requests
- [RCC User Guide: sinteractive](https://docs.rcc.uchicago.edu/slurm/sinteractive/) --- Interactive sessions
- [RCC User Guide: Python & Jupyter](https://docs.rcc.uchicago.edu/software/apps-and-envs/python/) --- Setting up Python and Jupyter on Midway
- [RCC User Guide: Partitions](https://docs.rcc.uchicago.edu/partitions/) --- Available partitions and their specifications
- [TRACE Case Study Repository](https://github.com/finm-32800/case_study_clean_trace) --- The TRACE data pipeline used in this course
