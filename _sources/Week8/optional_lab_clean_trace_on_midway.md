# Optional Lab: Clean TRACE on Midway

This lab is optional and ungraded. It walks through running the Clean TRACE
corporate bond pipeline on UChicago's RCC Midway3 cluster as a SLURM batch job:
connecting over SSH, setting up an environment on a shared cluster, reading
data that was pulled once for the whole class, submitting the job, and
reaching a remote VS Code session through an SSH tunnel.

## Learning Outcomes

- Connect to UChicago's Midway3 HPC cluster via SSH
- Set up a working directory and a Python environment on a shared cluster
- Run a real corporate bond data pipeline (Clean TRACE) using SLURM batch jobs
- Read a shared, read-only copy of the data instead of pulling your own
- Use `scode` to launch a remote VS Code instance on a compute node
- Use SSH port forwarding to access remote services from your laptop

## Assignment

This is a lightweight exercise designed to give you hands-on experience with
the RCC Midway3 cluster. You will set up your environment, run the Clean TRACE
data pipeline as a batch job, and launch a remote VS Code session. It is
intentionally light.

---

## Part 1: Set Up Your Working Directory on RCC

### Step 1: SSH into Midway3

Open a terminal on your laptop and connect to Midway3:

```bash
ssh <your-cnetid>@midway3.rcc.uchicago.edu
```

You will be prompted for your password, followed by Duo multi-factor
authentication (MFA). After completing MFA, you will land on a **login node**.

```{admonition} Login nodes are shared
:class: warning

Login nodes are shared by all users. They are intended for lightweight tasks:
editing files, submitting jobs, transferring data. **Do not run computationally
intensive work on login nodes.** Use the job scheduler (SLURM) to request
dedicated compute resources instead.
```

### Step 2: Create your personal directory in project space

Your home directory (`/home/$USER`) has a small storage quota (~30 GB) but a
generous file count limit (300,000 files). The shared project directory has
more storage but a limited file count quota. Use each for what it's best at:

| Location | Path | Quota | Backed Up | Purpose |
|----------|------|-------|-----------|---------|
| Home | `/home/<cnetid>` | 30 GB | Yes | Config files, small scripts, Python environments |
| Project | `/project/finm32800` | Large (shared) | Yes | Shared course data and code |
| Scratch | `/scratch/midway3/<cnetid>` | Large | **No** | Temporary large files, intermediate results |

Create a personal directory in the project space:

```bash
mkdir -p /project/finm32800/${USER}
```

### Step 3: Clone the Clean TRACE repository

Clone the case study repository into your project directory:

```bash
cd /project/finm32800/${USER}
git clone https://github.com/finm-32800/case_study_clean_trace case_study_clean_trace
cd case_study_clean_trace
```

```{tip}
Store your code and data in `/project/finm32800/${USER}/` for this course.
Store Python environments in your home directory (`$HOME/envs/`) to avoid
hitting the file count limit on the shared project directory.
```

---

## Part 2: Set Up the Environment

You set up on the **login node**, which has internet access. Installing
packages is one of the things a login node is for. Running the pipeline is
not; that goes to a compute node in Part 3.

### Step 1: Install `uv` (once)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Log out and back in so that `uv` is on your `PATH`, then check with
`uv --version`.

### Step 2: Create the environment in your home directory

From inside `case_study_clean_trace`:

```bash
uv venv $HOME/envs/clean_trace --python 3.12
uv pip install --python $HOME/envs/clean_trace/bin/python -r requirements.txt
```

The environment lives in your home directory, not in `/project/`, because
it is thousands of small files and the project space limits the file
count. To use it in a later session:

```bash
source $HOME/envs/clean_trace/bin/activate
```

### Step 3: Create the `.env` file

```bash
cp .env.example .env
```

Leave it as it is. You do not connect to WRDS in this lab, and the date range
stays at the default, a 2-month sample (January--February 2024).

---

## Part 3: Run the Clean TRACE Pipeline

### The data is already pulled

Compute nodes on Midway3 have no internet access, so a batch job cannot pull
from WRDS. The pull ran once for the whole class, and its output sits in a
shared folder:

```
/project/finm32800/shared/case_study_clean_trace/_data/pulled/
```

**Treat this folder as read-only.** It is the single copy everyone reads. You
do not copy it into your own directory, because 80 copies of the same data
would fill the course's storage. Your `_data/pulled` becomes a symbolic link
to it, and everything the pipeline *builds* from it lands in your own
`_data/`.

This is the "pull once, process anywhere" pattern from
[Remote Machines and HPC](./remote_machines_and_hpc.md), with one more
step: the pull is shared by everyone who needs it.

### The sbatch script

Copy the class's batch script into your clone:

```bash
cd /project/finm32800/${USER}/case_study_clean_trace
cp /project/finm32800/shared/run-pipeline.sbatch .
```

It reads:

```bash
#!/bin/bash
#SBATCH --job-name=clean-trace
#SBATCH --account=finm32800
#SBATCH --partition=caslake
#SBATCH --nodes=1
#SBATCH --cpus-per-task=8
#SBATCH --mem=16G
#SBATCH --time=01:00:00
#SBATCH --output=%x_%j.out

set -euo pipefail

# Compute nodes have en_US.UTF-8 but not C.UTF-8, which a Mac often sends.
export LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8

SHARED_PULLED=/project/finm32800/shared/case_study_clean_trace/_data/pulled

cd /project/finm32800/${USER}/case_study_clean_trace
source $HOME/envs/clean_trace/bin/activate

mkdir -p _data
[ -e _data/pulled ] || ln -s "${SHARED_PULLED}" _data/pulled

doit ignore pull:fama_french pull:fisd pull:liu_wu pull:osbap \
    pull:trace_144a pull:trace_enhanced pull:trace_standard

doit
echo "Pipeline complete $(date)"
```

| Directive | Value | Meaning |
|-----------|-------|---------|
| `--job-name` | `clean-trace` | Name shown in the queue; `%x` in the output file name |
| `--account` | `finm32800` | The course's allocation, which is billed for the job |
| `--partition` | `caslake` | The general-purpose compute nodes |
| `--cpus-per-task` | `8` | Eight CPU cores |
| `--mem` | `16G` | Memory for the whole job (the sample run peaks near 7 GB) |
| `--time` | `01:00:00` | Wall-clock limit; the job is killed if it runs longer |
| `--output` | `%x_%j.out` | Output file: job name and job ID |

The script does three things:

1. **Links** `_data/pulled` to the shared folder, if the link is not already
   there.
2. **Marks the pull tasks as ignored,** so `doit` never tries to reach WRDS
   from a compute node.
3. **Runs the rest of the pipeline** with `doit`: the FISD universe, the
   TRACE filters, Stage 0 (the Dick-Nielsen cleaning), and Stage 1 (bond
   analytics).

### Submit the batch job

```bash
sbatch run-pipeline.sbatch
```

You should see output like:

```
Submitted batch job 12345678
```

### Monitor your job

Use these commands to check on your job:

```bash
# Check job status
squeue -u ${USER}

# View all partitions and node availability
sinfo

# Check your account balance (compute allocation)
accounts balance

# Follow the job's output in real time
tail -f clean-trace_<jobid>.out

# After it finishes: how long it ran and how much memory it used
sacct -j <jobid> --format=JobID,Elapsed,MaxRSS,State

# Cancel a job if needed
scancel <jobid>
```

On the 2-month sample the run takes about 10 to 15 minutes once the job
starts. The full history, July 2002 to the present, takes several hours on
the same script with a longer `--time`.

---

## Part 4: Launch VS Code on Midway with `scode`

RCC's `scode` starts VS Code on a compute node as a SLURM job and tells you
the SSH tunnel to open from your laptop. It is the tunnel you built by hand
in [Exercise: Jupyter on Midway](./exercise_jupyter_on_midway.md), scripted.

**Follow RCC's instructions, which are the source of truth:**
[SCode: VS Code on Midway](https://docs.rcc.uchicago.edu/software/apps-and-envs/scode/main/).
RCC updates the tool, and its page is kept current with it. Two things to
supply from this course:

- **The account:** `--account finm32800`, after the lone `--` in the
  `scode serve-web` command.
- **If the browser shows "Connection refused":** see the
  {ref}`fix in the exercise <option-2-scode>`.

```{admonition} Always clean up
:class: warning

When you are done, cancel the `scode` job with `scancel <jobid>`. An idle
session still bills the course's allocation until its time runs out.
```

---

## Part 5: Deliverables

Submit the following screenshots to demonstrate that you completed the assignment:

1. **Pipeline completion**: A screenshot showing that the pipeline ran
   successfully in your own directory at `/project/finm32800/$USER/case_study_clean_trace/`.
   This could be the final lines of your job's `.out` file showing
   `"Pipeline complete"`, or a listing of the output files in `_data/stage1/`.

2. **VS Code via scode**: A screenshot of VS Code running in your browser
   via `scode`, showing that you are connected to a Midway3 compute node.

```{tip}
You can use `tail` to show the end of your job output file:

    tail -20 clean-trace_<jobid>.out

Or list the pipeline outputs:

    ls -lh /project/finm32800/${USER}/case_study_clean_trace/_data/stage1/
```
