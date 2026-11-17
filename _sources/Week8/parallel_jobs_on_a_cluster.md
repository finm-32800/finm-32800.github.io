# Parallel Jobs on a Cluster

Cleaning one month of TRACE takes about two minutes. The full history is more
than 270 months. Run one after another, that is nine hours; run at the same
time on 270 cores, it is still two minutes. A cluster has the cores. This
page is about how you ask for them.

There are two common ways, and you should be able to read both:

- **GNU Parallel** runs many commands at once *inside one job*.
- **A SLURM job array** submits *many jobs* at once, one per task.

We build up to the TRACE job in six small steps. The scripts are in the
[`slurm` folder of the in-class examples](https://github.com/finm-32800/inclass_examples/tree/main/slurm).
Each one runs in under a minute and costs almost nothing, so submit them
yourself.

## When Can Work Run in Parallel?

Tasks can run at the same time when they are **independent**: no task needs
another task's output, and no two tasks write to the same file.

Stage 0 of the TRACE pipeline is independent by month. February's raw trades
are in one file, and cleaning them produces one file. March has its own raw
file and its own output. Nothing in March's cleaning reads February. Work like this is called
*embarrassingly parallel*, because there is nothing clever to do: split it up
and run the pieces.

Stage 1 is different. It follows each bond through time to compute returns
and to track its rating, so it needs every month together. It runs as one
task, after stage 0 has finished.

## The Work: a Task That Takes Five Seconds

To see the scheduling without waiting on real data, the examples use a task
that sleeps for five seconds, squares a number, and says which machine it ran
on:

```bash
# slow_square.sh
N="$1"
sleep 5
echo "$(date +%T)  $(hostname)  ${N} squared is $((N * N))"
```

We want to run it for the numbers 1 through 6.

## Step 1: One After Another

```bash
#!/bin/bash
#SBATCH --job-name=01-serial
#SBATCH --account=finm32800
#SBATCH --partition=caslake
#SBATCH --cpus-per-task=1
#SBATCH --mem=1G
#SBATCH --time=00:02:00
#SBATCH --output=%x_%j.out

for N in 1 2 3 4 5 6; do
    bash slow_square.sh ${N}
done
```

Submit it with `sbatch 01_serial.sbatch` and read the output file when it is
done:

```text
02:00:50  midway3-0156.rcc.local  1 squared is 1
02:00:55  midway3-0156.rcc.local  2 squared is 4
02:01:00  midway3-0156.rcc.local  3 squared is 9
02:01:05  midway3-0156.rcc.local  4 squared is 16
02:01:10  midway3-0156.rcc.local  5 squared is 25
02:01:15  midway3-0156.rcc.local  6 squared is 36
```

One machine, one core, six tasks five seconds apart: 30 seconds.

## Step 2: GNU Parallel

[GNU Parallel](https://www.gnu.org/software/parallel/) is a command-line
program that takes a command and a list of inputs, and runs the command once
per input, several at a time. It is not part of SLURM. It runs on a laptop
the same way it runs on a cluster.

The smallest example, which you can type at any prompt where it is installed
(on Midway3, after `module load parallel`):

```bash
parallel echo "Hello, {}" ::: Alice Bob Carol
```

```text
Hello, Alice
Hello, Bob
Hello, Carol
```

- `:::` starts the list of inputs.
- `{}` is where each input goes in the command.
- The three `echo` commands run at the same time, so the lines can come out
  in any order.

The options you will meet most often:

| Option | What it does |
|---|---|
| `-j 4` | Run at most four commands at a time |
| `--dry-run` | Print the commands that would run, and run nothing |
| `:::: tasks.txt` | Read the inputs from a file, one per line |
| `--colsep ' '` | Split each input line into `{1}`, `{2}`, `{3}`, ... |
| `--joblog log.txt` | Write one line per finished command: start, run time, exit code |
| `--resume` | Skip the commands that the job log says already ran |
| `--resume-failed` | Skip those that succeeded, and rerun those that failed |

`--dry-run` is the one to learn first. Use it every time before the real run:

```bash
parallel --dry-run -j 2 python clean_month.py {} ::: 2020-02 2020-03 2020-04
```

```text
python clean_month.py 2020-02
python clean_month.py 2020-03
python clean_month.py 2020-04
```

Without `--dry-run`, this runs those three commands, two at a time. The third
starts when either of the first two finishes.

Now the six squares, as a batch job. The job asks for six cores on one
machine, and GNU Parallel uses them:

```bash
#!/bin/bash
#SBATCH --job-name=02-gnu-parallel
#SBATCH --account=finm32800
#SBATCH --partition=caslake
#SBATCH --cpus-per-task=6
#SBATCH --mem=1G
#SBATCH --time=00:02:00
#SBATCH --output=%x_%j.out

export LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
module load parallel

parallel -j ${SLURM_CPUS_PER_TASK} bash slow_square.sh {} ::: 1 2 3 4 5 6
```

```text
02:00:50  midway3-0099.rcc.local  6 squared is 36
02:00:50  midway3-0099.rcc.local  4 squared is 16
02:00:50  midway3-0099.rcc.local  2 squared is 4
02:00:50  midway3-0099.rcc.local  3 squared is 9
02:00:50  midway3-0099.rcc.local  1 squared is 1
02:00:50  midway3-0099.rcc.local  5 squared is 25
```

One machine, the same second on every line, and no particular order: 5
seconds.

Two things in the script are worth a note. `SLURM_CPUS_PER_TASK` is set by
SLURM to the number you asked for, so the `-j` can never disagree with the
`#SBATCH` line. And the `export` line is there because a Mac sends its own
language setting over SSH, the compute nodes do not have it, and GNU Parallel
(a Perl program) prints a long warning for every task without it.

## Step 3: a Job Array

A job array asks SLURM to do the splitting. One line does it:

```bash
#!/bin/bash
#SBATCH --job-name=03-array
#SBATCH --account=finm32800
#SBATCH --partition=caslake
#SBATCH --array=1-6
#SBATCH --cpus-per-task=1
#SBATCH --mem=1G
#SBATCH --time=00:02:00
#SBATCH --output=%x_%A_%a.out

bash slow_square.sh ${SLURM_ARRAY_TASK_ID}
```

`--array=1-6` tells SLURM to run this script six times, as six separate jobs.
Each copy is identical except for one environment variable,
`SLURM_ARRAY_TASK_ID`, which is 1 in the first copy, 2 in the second, and so
on. The script uses that number to decide what its own piece of the work is.

Every other `#SBATCH` line describes **one task**, not the whole array: each
of the six gets one core, 1 GB, and two minutes.

In the output file name, `%A` is the array's job ID and `%a` is the task's
number, so each task writes its own file:

```text
== 03-array_63296473_1.out
02:00:50  midway3-0156.rcc.local  1 squared is 1
== 03-array_63296473_2.out
02:00:50  midway3-0214.rcc.local  2 squared is 4
== 03-array_63296473_3.out
02:00:50  midway3-0136.rcc.local  3 squared is 9
== 03-array_63296473_4.out
02:00:50  midway3-0097.rcc.local  4 squared is 16
== 03-array_63296473_5.out
02:00:57  midway3-0203.rcc.local  5 squared is 25
== 03-array_63296473_6.out
02:01:10  midway3-0203.rcc.local  6 squared is 36
```

Look at the machines and the times. The six tasks ran on five different
machines, and tasks 5 and 6 started a few seconds after the others. Each task
is its own job in the queue, and each started when the scheduler found it a
free core.

`sacct` shows one row per task:

```text
$ sacct -X -j 63296473 --format=JobID%16,JobName,State,Elapsed,NodeList
           JobID    JobName      State    Elapsed        NodeList
---------------- ---------- ---------- ---------- ---------------
      63296473_1   03-array  COMPLETED   00:00:06    midway3-0156
      63296473_2   03-array  COMPLETED   00:00:06    midway3-0214
      63296473_3   03-array  COMPLETED   00:00:06    midway3-0136
      63296473_4   03-array  COMPLETED   00:00:06    midway3-0097
      63296473_5   03-array  COMPLETED   00:00:16    midway3-0203
      63296473_6   03-array  COMPLETED   00:00:29    midway3-0203
```

A few more forms of `--array`:

| Line | Meaning |
|---|---|
| `--array=1-6` | Tasks 1 through 6 |
| `--array=2015-2024` | Ten tasks; the task ID is the year |
| `--array=3,5` | Only tasks 3 and 5 |
| `--array=0-99%10` | A hundred tasks, at most ten running at a time |

The `%10` matters on a shared allocation. Without it, a large array takes
every core the scheduler will give it.

## Step 4: GNU Parallel Across Machines

Step 2 kept all six tasks on one machine. A job can also ask for six *slots*
and let SLURM put them wherever there is room:

```bash
#SBATCH --ntasks=6
#SBATCH --cpus-per-task=1

parallel -j ${SLURM_NTASKS} \
    srun --exclusive -N1 -n1 bash slow_square.sh {} ::: 1 2 3 4 5 6
```

GNU Parallel still decides which task runs next. `srun` is SLURM's command
for starting a program on the resources a job already holds; here each `srun`
takes one free slot of the job's allocation and runs one task there. In our
run, five tasks landed on `midway3-0012` and one on `midway3-0029`.

This is still **one job**: one line in `squeue`, one job ID, one output file.

## Steps 5 and 6: When One Task Fails

In real work one month has a bad row and its task crashes. The examples use
`flaky_square.sh`, which fails for the number 3 until a file named `fixed`
exists.

**With a job array,** `sacct` tells you which task failed:

```text
           JobID      State ExitCode
---------------- ---------- --------
      63296475_1  COMPLETED      0:0
      63296475_2  COMPLETED      0:0
      63296475_3     FAILED      1:0
      63296475_4  COMPLETED      0:0
      63296475_5  COMPLETED      0:0
      63296475_6  COMPLETED      0:0
```

SLURM does not rerun task 3, and it keeps no list of what is left to do. You
fix the problem and submit the same script again, for that task only:

```bash
touch fixed                                       # "fix the bug"
sbatch --array=3 05_array_with_a_failure.sbatch
```

An option given on the command line overrides the `#SBATCH` line of the same
name. So this submission is an array with one task, number 3, and the script
does what it always does for task 3.

**With GNU Parallel,** the scheduler knows only that the one job failed. The
detail is in the job log, which has one line per command. Four of its nine
columns are shown here; `Exitval` is the command's exit code, and anything
but 0 is a failure:

```text
Seq  JobRuntime  Exitval  Command
1    5.000       0        bash flaky_square.sh 1
2    5.000       0        bash flaky_square.sh 2
4    5.000       0        bash flaky_square.sh 4
5    5.000       0        bash flaky_square.sh 5
6    5.000       0        bash flaky_square.sh 6
3    5.000       1        bash flaky_square.sh 3
```

Because the script passes `--joblog joblog.txt --resume-failed`, you fix the
problem and submit the *same script, unchanged*. GNU Parallel reads the log,
skips the five that succeeded, and reruns the one that failed.

### Idempotent Tasks

There is a third way, and it works with either tool. Write each task so that
it checks for its own output first and exits at once when the output is
already there. A task like that is called **idempotent**: running it twice
leaves the same result as running it once.

Then you never work out which tasks failed. You submit everything again. The
finished tasks each take a second, and only the missing ones do any work.
The TRACE tasks below are written this way.

## The Two Side by Side

| | Job array | GNU Parallel in one job |
|---|---|---|
| What the scheduler sees | N jobs, each queued and billed on its own | One job; you divide its allocation yourself |
| When the work starts | Each task as soon as a slot is free for it | When the whole allocation is free |
| Where a failure shows up | `sacct`, one row per task | The job log |
| Rerunning a failure | `sbatch --array=3 script.sbatch` | Submit again with `--resume-failed` |
| Output | One file per task | One file for the job |
| Works without a scheduler | No | Yes, on any Linux or Mac |
| Good for | Tens to hundreds of tasks, minutes to hours each | Hundreds or thousands of short tasks |

The last row is the one that decides most cases. Every job costs the
scheduler some work to queue, start, and record. For a thousand tasks of
three seconds each, that overhead is larger than the work, and cluster
administrators ask you not to submit them as an array. One job that runs GNU
Parallel is the right tool there. For six tasks of two minutes, or two
hundred tasks of an hour, the array is simpler and tells you more.

Neither is a pipeline. If task 4 needs the output of task 3, the tasks are
not independent, and the tool is `doit`, or a second job submitted with
`sbatch --dependency=afterok:<job id>`, which starts only after the first has
finished without an error.

## The TRACE Job, Both Ways

The real job cleans February, March, and April of 2020 for two TRACE feeds,
Enhanced and Rule 144A: six tasks. The scripts are in the
[`slurm` folder of the TRACE case study](https://github.com/finm-32800/case_study_clean_trace/tree/main/slurm).

Both versions read the same list, `slurm/tasks.txt`, one task per line:

```text
enhanced 2020 2
enhanced 2020 3
enhanced 2020 4
144a 2020 2
144a 2020 3
144a 2020 4
```

and both call the same script, `slurm/clean-one-month.sh <feed> <year> <month>`,
which cleans one month and skips it if the output already exists.

**As a job array** (`slurm/clean-trace-array.sbatch`), task N takes line N of
the list:

```bash
#SBATCH --array=1-6
#SBATCH --cpus-per-task=2
#SBATCH --mem=8G
#SBATCH --time=00:30:00
#SBATCH --output=%x_%A_%a.out

TASK=$(sed -n "${SLURM_ARRAY_TASK_ID}p" slurm/tasks.txt)
bash slurm/clean-one-month.sh ${TASK}
```

`sed -n "3p" file` prints line 3 of a file. This is the usual way to hand an
array anything more complicated than a number: put the inputs in a file, one
per line, and let the task ID pick the line.

```text
$ sacct -X -j 63296433 --format=User,JobID%16,JobName,State,Elapsed,NodeList
     User            JobID    JobName      State    Elapsed        NodeList
--------- ---------------- ---------- ---------- ---------- ---------------
jbejarano       63296433_1 trace-arr+  COMPLETED   00:01:41    midway3-0090
jbejarano       63296433_2 trace-arr+  COMPLETED   00:01:57    midway3-0105
jbejarano       63296433_3 trace-arr+  COMPLETED   00:01:55    midway3-0012
jbejarano       63296433_4 trace-arr+  COMPLETED   00:00:35    midway3-0099
jbejarano       63296433_5 trace-arr+  COMPLETED   00:00:36    midway3-0185
jbejarano       63296433_6 trace-arr+  COMPLETED   00:00:35    midway3-0185
```

Six tasks on five machines, done in under two minutes. One after another,
the same work takes more than seven.

**With GNU Parallel** (`slurm/clean-trace-parallel.sbatch`), one job asks for
six slots and hands the six lines out:

```bash
#SBATCH --ntasks=6
#SBATCH --cpus-per-task=2
#SBATCH --mem-per-cpu=4G

module load parallel

parallel -j "${SLURM_NTASKS}" --colsep ' ' \
    --joblog trace-parallel_joblog.txt --resume \
    srun --exclusive -N1 -n1 bash slurm/clean-one-month.sh {1} {2} {3} \
    :::: slurm/tasks.txt
```

`--colsep ' '` splits each line of the list at the spaces, so `{1}` is the
feed, `{2}` the year, and `{3}` the month.

To clean ten years, neither script changes. The list gets longer, and for
the array `--array=1-6` becomes `--array=1-240%20`.

[HW 5](../HW5.md) asks you to submit the array version yourself.

## Further Reading

- [GNU Parallel tutorial](https://www.gnu.org/software/parallel/parallel_tutorial.html)
- [SLURM job array documentation](https://slurm.schedmd.com/job_array.html)
- [RCC user guide: running jobs on Midway3](https://docs.rcc.uchicago.edu/slurm/main/)
