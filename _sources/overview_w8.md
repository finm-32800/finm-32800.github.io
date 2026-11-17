# Week 8: VS Code on a Cluster and Parallel Jobs

```{toctree}
:maxdepth: 1
Week8/pins_that_a_rebuild_can_meet.md
notebooks/_03_corporate_bonds_march_2020_ipynb.ipynb
Week8/parallel_jobs_on_a_cluster.md
Week8/study_guide.md
```

Last week we logged in to Midway3 and met the scheduler. This week we put the
cluster to work on a question. In March 2020 the corporate bond market came
close to breaking, and on March 23 the Federal Reserve said it would buy
corporate bonds. To see what happened day by day we need three months of
TRACE, cleaned. That gives us two things to learn: how to work on a compute
node with a real editor, which turns out to be a question about how a browser
on your laptop reaches a machine that is not on the internet; and how to
clean many months at the same time, with GNU Parallel and with a SLURM job
array.

We start with HW 4, and with what its lockfile taught us about ChartBook.

## Announcements

- **[HW 4](./HW4.md), the class report, is due next Tuesday,** November 24,
  at 9:30 am. Only pull requests merged by then count. Your pull request
  should be open today, so that your two reviewers have the week.
- **[HW 5](./HW5.md) is out today and is short on purpose:** submit one batch
  job on Midway3, open VS Code on a compute node, and take a screenshot of
  each. It is due **Friday, December 11, at 11:59 pm,** the end of exam
  week, but you can finish it in class today.
- **No class next week.** November 24 falls in Thanksgiving break. Week 9 meets
  on December 1.
- **The exam is December 1,** at the start of class: 30 multiple-choice
  questions in 60 minutes, covering weeks 0 through 8, today included. A
  practice exam of 20 questions is handed out today; see
  [Exam Preparation](./exam_prep.md).
- **Final project work should be underway.** Your project is due 24 hours
  before your final project presentation in week 10. I run and grade the code
  before we meet, so it must run from a clean clone by following your README
  alone.
- **Final project presentation sign-ups.** Book your group's 30-minute slot for
  week 10 at [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile). The last day
  is Friday, December 11.

## Objectives

- Say why a rebuild of an upstream pipeline can fail to match a pin made
  from a hash of a file's bytes, and how a pin by rows up to a date differs.
- Describe what happened to corporate bond credit spreads and trading in
  March 2020, and what the Federal Reserve's announcement of March 23 did.
- Explain SSH port forwarding: why a browser on your laptop cannot reach a
  compute node, and what each part of `ssh -L 8000:node:port login` names.
- Run VS Code on a Midway3 compute node with `scode`.
- Run independent tasks at the same time with GNU Parallel and with a SLURM
  job array, read `sacct` and a GNU Parallel job log, and rerun only the task
  that failed.
- Choose between a job array and GNU Parallel for a given set of tasks.

## Agenda

1. **Announcements, HW 5, and the exam.** What the exam looks like: the
   practice exam, the bubble sheet, and how a question is scored.
2. **What broke in HW 4, and the fix**
   ([Pins That a Rebuild Can Meet](./Week8/pins_that_a_rebuild_can_meet.md)).
   A pin is a hash of bytes. A rebuild a week later has newer data, and a
   chart never has the same bytes twice. ChartBook is being changed so that
   a lockfile holds only what code reads, and a table is pinned by its rows
   up to a date.
3. **Corporate bonds in March 2020**
   ([Corporate Bonds in March 2020](./notebooks/_03_corporate_bonds_march_2020_ipynb.ipynb)).
   Credit spreads, the dash for cash, and the announcement of March 23. The
   figure we want needs three months of TRACE, cleaned, on Midway3.
4. **VS Code on a compute node**
   ([Remote Machines and HPC](./Week8/remote_machines_and_hpc.md)). To work
   on the cluster you want your editor, running where the data is. The
   browser on your laptop cannot reach a compute node, and SSH port
   forwarding is how it gets there. Then `scode`, live.
5. **Parallel jobs**
   ([Parallel Jobs on a Cluster](./Week8/parallel_jobs_on_a_cluster.md)).
   Six small steps: one after another, GNU Parallel, a job array, GNU
   Parallel across machines, and what to do when one task fails. Then the
   TRACE job both ways, and the figure it makes.
6. **[HW 5](./HW5.md), in the room.** Submit the array, open VS Code, take
   the two screenshots.

## Looking ahead to Week 9

The **exam** is at the start of class on December 1. After it, a
demonstration to close the course; the topic will be announced. There is no
class on November 24.
