# Pins That a Rebuild Can Meet

HW 4 asked each of you to rebuild six upstream pipelines and have your builds
match the pins in the class report's `chartbook.lock`. For many of you they
did not match, and the build stopped. This page says why, what to do about it
for HW 4, and how ChartBook is being changed because of it.

ChartBook is software under development, and this class is one of the first
groups to use its lockfile this way: many people, no shared store, each
rebuilding the same upstreams on a different day. The problems below were
found by you. Nothing described under "The fix" is released yet.

## What a Pin Records Today

For every file the report reads from an upstream, the lockfile holds a
**hash of the file's bytes**. `chartbook pin --check` compares that hash with
the file in your store. One byte different, and the pin is not met.

That is the right record when the bytes travel. A team that shares one store
reads the very file that was pinned, so the hash always matches, and a
mismatch means something is wrong.

In HW 4 the bytes do not travel. There is no shared store, because several
upstreams hold data that only a licensed user may download. Each of you
builds every upstream yourself, and the only way to get the pinned data is to
make it again.

## What Went Wrong, Twice

**1. New data.** The pins were made on November 4. A student who built the
same commit of an upstream a week later got tables with more rows: the
Federal Reserve and Ken French's data library had both published since. More
rows means other bytes, so the check failed and the build stopped with
`AssetNotPinnedError`. No code had changed. The source had.

**2. Charts and notebooks.** Another student's tables matched, and the report
built, but the check still failed on 17 of the 34 pins: the 10 charts and 7
notebooks. No rebuild can match those. Draw the same Plotly chart twice, a
second apart, and the two HTML files differ, because Plotly writes a random
ID into the page. Run the same notebook twice and the files differ, because a
notebook records the time each cell ran.

In both cases the pin asked for something a rebuild cannot give. A pin that
only the original bytes can meet is of no use the day after it is made,
unless those bytes are shared.

## What To Do for HW 4

If your rebuilt upstream does not match its pin because the source has
published newer data, move the pin in your pull request with
`chartbook update <name>`, and say so in the pull request's description. The
diff of `chartbook.lock` shows your reviewers exactly what moved. This is in
[HW 4](../HW4.md), under *Rebuild the Upstreams*.

## The Fix, in Two Parts

### A lockfile holds what code reads

The lockfile is about data that code computes with: dataframes and models. A
chart or a notebook is drawn from the data, and every build draws it again.
So charts, notebooks, and notes leave the lockfile.

| Kind | In the lockfile? | Checked by |
|---|---|---|
| Dataframes | Yes | The same file; if not, the same rows up to the pin's last date |
| Models | Yes | The same file |
| Charts, notebooks, notes | No | Not checked; each build draws them again |

The class report's lockfile goes from 34 records to 17. This part ends the
second failure.

### A dataframe is pinned by its rows

For a table with a date column, the lockfile also records the **last date**
in the table and a **hash of the rows**, not only of the file.

On November 4 the author pins this table:

| ds | y |
|---|---|
| 2026-10-28 | 4.10 |
| 2026-10-29 | 4.12 |
| 2026-10-30 | 4.11 |

The lockfile records the last date, October 30, and a hash of those three
rows.

On November 12 you build the same commit and get five rows: the same three,
and two newer days. To check your build, ChartBook leaves out your rows after
October 30, hashes the three that remain, and compares. They match. Your
table holds the pinned data and two newer days, and the pin is met.

What changes between two builds, and what the check then says:

| What changed | Is the pin met? |
|---|---|
| New rows after the pin's last date | Yes. Those rows are left out before hashing |
| The same rows in other bytes: another row order, another version of the Parquet writer | Yes. The hash is of the rows, not of the file |
| An old value was revised by the source | No. This is reported as a real difference |

The last row is on purpose. If Ken French revises last year's returns, your
numbers will differ from the author's, and you should be told. Nothing in
this design brings the old values back. Only a store that shares the bytes
does that, or a source that can be asked for an old vintage.

## Where It Stands

As of November 17:

- `chartbook pin --check`, which checks the pins and writes nothing, is
  merged.
- The lockfile by kind, the rows hash, and recording a table's rows by period
  are written and under review as three pull requests.
- The check that uses the rows hash, and the reads that leave out rows after
  the pin's last date, are not built yet.

No release date is set. When it is released, the class report will be pinned
again in the new form, and an old lockfile will keep working in the meantime.

The general lesson is older than this tool. "Reproducible" has to say what is
being reproduced. The same bytes is the strictest answer, and it holds only
while nothing outside your code moves. The same rows up to a date is a
weaker promise that survives a data provider publishing on schedule, and it
still catches the case you care about: the past being changed.
