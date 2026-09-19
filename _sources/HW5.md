# Homework 5 (Optional)

```{admonition} This assignment is now optional
:class: important

**Update (August 13, 2026):** Due to ongoing issues with access to the RCC
Midway3 cluster, Homework 5 is now **optional** and will not be collected or
graded. The remote machines and HPC material will be covered in a make-up
session at a later date. If you would like the hands-on HPC experience, you
are encouraged to work through this assignment once cluster access is restored.
```

## Learning Outcomes

- Connect to UChicago's Midway3 HPC cluster via SSH
- Set up a working directory and conda environment on a shared cluster
- Run a real corporate bond data pipeline (Clean TRACE) using SLURM batch jobs
- Use `rsync` to copy pre-pulled data from the instructor's project directory
- Use `scode` to launch a remote VS Code instance on a compute node
- Use SSH port forwarding to access remote services from your laptop

## Assignment

The assignment is located at the following GitHub Classroom link: TBD

**Due Date:** None---this assignment is optional.

This is a lightweight assignment designed to give you hands-on experience with
the RCC Midway3 cluster. You will set up your environment, run the Clean TRACE
data pipeline as a batch job, and launch a remote VS Code session. This is
intentionally light so that you can focus on your final projects.

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
| Home | `/home/<cnetid>` | 30 GB | Yes | Config files, small scripts, conda environments |
| Project | `/project/finm32900` | Large (shared) | Yes | Shared course data and code |
| Scratch | `/scratch/midway3/<cnetid>` | Large | **No** | Temporary large files, intermediate results |

Create a personal directory in the project space:

```bash
mkdir -p /project/finm32900/${USER}
```

### Step 3: Clone the Clean TRACE repository

Clone the case study repository into your project directory:

```bash
cd /project/finm32900/${USER}
git clone https://github.com/finm-32800/case_study_clean_trace case_study_clean_trace
cd case_study_clean_trace
```

```{tip}
Store your code and data in `/project/finm32900/${USER}/` for this course.
Store conda environments in your home directory (`$HOME/envs/`) to avoid
hitting the file count limit on the shared project directory.
```

---

## Part 2: Set Up the Conda Environment

### Step 1: Load the Anaconda module

Midway uses a **module system** to manage software. Load the Anaconda module:

```bash
module load python/anaconda-2024.10
```

You can see all available modules with:

```bash
module avail
```

```{admonition} Important
:class: warning

Do **not** run `conda init` on RCC. It modifies your shell profile in ways
that conflict with the module system. Always use `source activate` instead
of `conda activate`.
```

### Step 2: Switch conda to the classic solver

The default `libmamba` solver is incompatible with Midway3's system libraries.
Switch to the classic solver (you only need to do this once):

```bash
conda config --set solver classic
```

```{note}
You will see an error message like `Error while loading conda entry point:
conda-libmamba-solver ...` every time you run a conda command. **This is
harmless.** Conda still tries to load the libmamba plugin at startup, but
since the solver is set to `classic`, it doesn't need it. The commands will
complete successfully despite the error message.
```

### Step 3: Create a conda environment in your home directory

Create an isolated conda environment in your home directory (not in
`/project/`, to avoid hitting the shared file count limit):

```bash
conda create --prefix=$HOME/envs/clean_trace python=3.11 -y
```

Activate the environment and install dependencies:

```bash
source activate $HOME/envs/clean_trace
pip install -r requirements.txt
```

To reactivate this environment in future sessions:

```bash
module load python/anaconda-2024.10
source activate $HOME/envs/clean_trace
```

### Step 4: Configure the environment file

```bash
cp .env.example .env
```

Edit `.env` and set your WRDS username. Use the default small sample date
range (January--February 2024) to keep the run quick:

```
WRDS_USERNAME="your_wrds_username"
```

```{tip}
By default, the pipeline processes only a 2-month sample (January--February
2024). This is intentional---it lets you run end-to-end quickly without
processing 20+ years of data. Do **not** uncomment `START_DATE` or `END_DATE`
unless you want a much longer run.
```

### Step 5: Configure WRDS `.pgpass`

If you haven't already set up passwordless WRDS authentication, run the
following on the **login node** (which has internet access):

```bash
python -c "import wrds; db = wrds.Connection(); db.close()"
```

This will prompt for your WRDS username and password and create the `~/.pgpass`
file.

---

## Part 3: Run the Clean TRACE Pipeline

### Understanding the workflow

Worker nodes on Midway3 do **not** have internet access, so data pulls (WRDS,
Fama-French, etc.) cannot run inside a batch job. Instead, the instructor has
already pulled the data once on the head node. The sbatch script will `rsync`
this pre-pulled data into your project directory and then run the processing
pipeline.

The instructor's pre-pulled data is located at:

```
/project/finm32900/case_study_clean_trace/_data/pulled/
```

### The sbatch script

The repository includes a `run-pipeline.sbatch` script that automates
everything. Here is the script with annotations:

```bash
#!/bin/bash
#SBATCH --job-name=clean-trace-pipeline
#SBATCH --output=%j_clean-trace-pipeline.out
#SBATCH --error=%j_clean-trace-pipeline.err
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=16
#SBATCH --mem-per-cpu=8000
#SBATCH --time=8:00:00
#SBATCH --account=finm32900

set -euo pipefail

# --- Configuration ---
PROJECT_BASE="/project/finm32900"
WORK_DIR="${PROJECT_BASE}/${USER}/case_study_clean_trace"
INSTRUCTOR_DATA="${PROJECT_BASE}/case_study_clean_trace/_data/pulled"
STUDENT_DATA="${WORK_DIR}/_data/pulled"

# --- Change to working directory ---
cd "${WORK_DIR}"
echo "[$(date)] Working directory: ${WORK_DIR}"

# --- Activate conda environment ---
module load python/anaconda-2024.10
source activate "$HOME/envs/clean_trace"
echo "[$(date)] Activated conda environment"

# --- Install/update dependencies ---
pip install --quiet -r requirements.txt
echo "[$(date)] Dependencies up to date"

# --- Rsync pulled data from instructor's project directory ---
echo "[$(date)] Syncing pulled data from instructor directory..."
mkdir -p "${STUDENT_DATA}"
rsync -a --info=progress2 "${INSTRUCTOR_DATA}/" "${STUDENT_DATA}/"
echo "[$(date)] Data sync complete"

# --- Tell doit to skip all pull tasks (data already synced) ---
echo "[$(date)] Marking pull tasks as ignored..."
doit ignore pull:fama_french pull:fisd pull:liu_wu pull:osbap \
    pull:trace_144a pull:trace_enhanced pull:trace_standard
echo "[$(date)] Pull tasks ignored"

# --- Run the full pipeline ---
echo "[$(date)] Starting pipeline..."
doit
echo "[$(date)] Pipeline complete"
```

Key details about this script:

| Directive | Value | Meaning |
|-----------|-------|---------|
| `--job-name` | `clean-trace-pipeline` | Human-readable name for your job |
| `--output` / `--error` | `%j_clean-trace-pipeline.out/.err` | Stdout/stderr files (`%j` = job ID) |
| `--nodes` | `1` | Use a single compute node |
| `--cpus-per-task` | `16` | Request 16 CPU cores |
| `--mem-per-cpu` | `8000` | 8 GB per CPU (128 GB total) |
| `--time` | `8:00:00` | Maximum wall time of 8 hours |
| `--account` | `finm32900` | Course billing account |

The script does the following:

1. **Rsync** the instructor's pre-pulled data into your `_data/pulled/` directory
2. **Mark pull tasks as ignored** so that `doit` does not attempt to download data (compute nodes have no internet)
3. **Run the full pipeline** with `doit`, which builds the FISD universe, filters TRACE, runs Stage 0 (Dick-Nielsen cleaning), and runs Stage 1 (bond analytics)

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

# Follow the job's stdout in real time
tail -f <jobid>_clean-trace-pipeline.out

# Follow stderr
tail -f <jobid>_clean-trace-pipeline.err

# Cancel a job if needed
scancel <jobid>
```

The pipeline processes 20+ years of TRACE corporate bond data through multiple
stages. Depending on queue wait times and system load, the full run can take
several hours.

---

## Part 4: Launch VS Code on Midway with `scode`

RCC provides a tool called `scode` that launches a VS Code web server on a
compute node, which you can then connect to from your laptop's browser via
SSH port forwarding.

### Step 1: Load the required modules

On the Midway3 **login node**, load `scode` and the Anaconda module:

```bash
module load scode python/anaconda-2024.10
```

### Step 2: Launch the VS Code server

```bash
scode serve-web -- --account finm32900 --time 05:00:00 --mem 16G
```

This submits a SLURM job that starts a VS Code web server on a compute node.
You should see output similar to:

```
Removing existing symlink: /home/jbejarano/.vscode/cli/serve-web/072586267e68ece9a47aa43f8c108e0dcbf44622.
Linked /home/jbejarano/.scode/versions/stable/1.109.5/vscode-server-linux-x64-web to /home/jbejarano/.vscode/cli/serve-web/072586267e68ece9a47aa43f8c108e0dcbf44622
Submitting SBATCH to serve VSCode environment.

Submitted batch job 45863567

sbatch: Verify job submission ...
sbatch: Using a shared partition ...
sbatch: Partition: caslake
sbatch: QOS-Flag: caslake
sbatch: Account: finm32900
sbatch: Verification: ***PASSED***

SBATCH job /home/jbejarano/.scode/sbatches/scode-web_20260223_105124.sbatch submitted successfully.
Output will be directed to /home/jbejarano/.scode/logs/scode-web_45863567.out.
Errors will be directed to /home/jbejarano/.scode/logs/scode-web_45863567.err.

VSCode server is starting with Slurm Job ID 45863567.
Use `scode jobs status 45863567` to get server status and connection info.
Use `scancel 45863567` to cancel the server job.

VSCode job 45863567 is running on 1 nodes: midway3-0067
Primary node: 10.50.250.67
Environment: /home/jbejarano/.scode/envs/stable/default

To connect to the VSCode Web GUI you need to create an SSH tunnel from your
local machine to the primary node above. This can be done with the following
command to be run on your local machine (e.g., PowerShell in Windows):

    ssh -L 8000:10.50.250.67:58992 jbejarano@midway3.rcc.uchicago.edu

Once the tunnel is created, you may access the VSCode Web GUI by entering
the following address in your browser:

    http://localhost:8000/?tkn=SOME_LONG_TOKEN

Server outputs are being written to /home/jbejarano/.scode/logs/scode-web_45863567.out.
Server errors are being written to /home/jbejarano/.scode/logs/scode-web_45863567.err.
Use `squeue -j 45863567` to check the status of the job.
Use `scancel 45863567` to cancel the job.
```

### Step 3: Set up SSH port forwarding

On your **local machine** (your laptop), run the SSH tunnel command shown in
the `scode` output. Add the `-N` flag so that SSH only does port forwarding
and does not open a remote shell:

```bash
ssh -N -L 8000:<compute_node_ip>:<remote_port> <your-cnetid>@midway3.rcc.uchicago.edu
```

For example, using the output above:

```bash
ssh -N -L 8000:10.50.250.67:58992 jbejarano@midway3.rcc.uchicago.edu
```

You can optionally add the `-f` flag to run the tunnel in the background.

### Step 4: Connect via your browser

Open a browser and navigate to the URL shown in the `scode` output:

```
http://localhost:8000/?tkn=SOME_LONG_TOKEN
```

You should see the VS Code editor running in your browser, connected to the
Midway3 compute node. You can edit files, open terminals, and work as if you
were using VS Code locally --- but all computation happens on the cluster.

### Step 5: Managing the scode session

```bash
# Check the status of your scode server
scode jobs status <jobid>

# Check the job in the SLURM queue
squeue -j <jobid>

# Cancel the session when you're done
scancel <jobid>
```

```{admonition} Always clean up
:class: warning

When you are done working, cancel the `scode` job with `scancel`. Idle jobs
still consume your account's compute allocation. Don't leave sessions running
overnight.
```

---

## Part 5: Deliverables

Submit the following screenshots to demonstrate that you completed the assignment:

1. **Pipeline completion**: A screenshot showing that the pipeline ran
   successfully in your own directory at `/project/finm32900/$USER/case_study_clean_trace/`.
   This could be the final lines of your job's `.out` file showing
   `"Pipeline complete"`, or a listing of the output files in `_data/stage1/`.

2. **VS Code via scode**: A screenshot of VS Code running in your browser
   via `scode`, showing that you are connected to a Midway3 compute node.

```{tip}
You can use `tail` to show the end of your job output file:

    tail -20 <jobid>_clean-trace-pipeline.out

Or list the pipeline outputs:

    ls -lh /project/finm32900/${USER}/case_study_clean_trace/_data/stage1/
```
