# Walkthrough: From a Working Folder to a Pin

This page is the week 6 demo, written down so that you can redo it after
class. It uses the class report, the repository you work in for
[HW 4](../HW4.md), and one of its upstreams, `ftsfr/fed_yield_curve`. Every
command runs from a clone of the report, and every block of output is cut
down from a real run. Steps A to D were run on November 10, against the
pins the report has had since November 3. Step E's output is from
November 2, against the pin of that day, because it shows a day when the
Fed had published new data since the pin.

The words are the ones
[ChartBook: Know Where Every Number Came From](./chartbook_catalog.md) defines:
pipeline, store, snapshot, lockfile, pin, reference, lineage. Read that page
first; this one shows each word on the screen.

**What you need.** About an hour, and:

- A clone of the report with its environment installed (`uv sync` in the
  clone). Activate it once, `source .venv/bin/activate`, so that the
  commands below are typed as written; or put `uv run` in front of each.
- `chartbook --version` printing `0.5.1` or newer.
- The snapshots `chartbook.lock` pins, in your store, each built by you
  (step E shows how, for one of them). `chartbook pin --frozen` passes when
  this is set up.
- `jq`, for reading the lockfile (`brew install jq`, or your package
  manager).

## A. The summer edition: a working folder

What you are about to see: the Summer 2026 edition of the report, on the
`summer` branch. It was built with ChartBook 0.1, which had no snapshots and
no pins.

```console
git switch summer
ls chartbook.lock
grep -n -A3 "data.load" src/chart_yield_curve.py
sed -n 140,152p README.md
```

```text
ls: chartbook.lock: No such file or directory
34:    long = data.load(
35-        pipeline="ftsfr/fed_yield_curve",
36-        dataframe="ftsfr_treas_yield_curve_zero_coupon",
37-        format="pandas",
   chartbook catalog add ~/GitRepositories/ftsfr_repos/* -y
```

There is no lockfile. The exhibit reads by name, which is good, but the name
resolved through a catalog of working folders on one machine: the README
tells you to clone every upstream, run `doit` in each, and register the
folders. Whatever those folders held on the day of the build is what the
August PDF shows.

The point: which yield curve did the August report read? Git has the code.
Nothing has the data. The folder it read from has been rebuilt since, so
August's bytes are gone, and no one can answer. Do not build `summer` with
ChartBook 0.5 (it needs 0.1.1); switch back.

```console
git switch main
```

## B. The new main: a pin that names its bytes

What you are about to see: the same exhibit, now reading through a pin.
First, where ChartBook will look.

```console
chartbook store info
```

```text
Store default: /Users/you/.chartbook/store (writable)
  ftsfr/fed_yield_curve: 1 snapshot(s)
  ... (6 pipelines)
  Newest: 2026-11-04T06:04:56.031930Z
```

This is the default store, made when your first snapshot was. (Before
`chartbook catalog init`, the same command prints the folder alone.) The report
declares its upstreams in `chartbook.toml`:

```console
sed -n '/^\[dependencies\]/,$p' chartbook.toml
```

```text
[dependencies]
"ftsfr/fed_yield_curve" = { repository = "https://github.com/ftsfr/fed_yield_curve" }
"ftsfr/ken_french_data_library" = { repository = "https://github.com/ftsfr/ken_french_data_library" }
"ftsfr/he_kelly_manela" = { repository = "https://github.com/ftsfr/he_kelly_manela" }
"ftsfr/corp_bond_returns" = { repository = "https://github.com/ftsfr/corp_bond_returns" }
"ftsfr/options" = { repository = "https://github.com/ftsfr/options" }
"ftsfr/nyu_call_report" = { repository = "https://github.com/ftsfr/nyu_call_report" }
```

Each line names a pipeline by its ID, found by that ID in a store, with no
folder of it on this machine. `repository` says where that pipeline's code
lives, so that a reader of this file knows what to clone and build; a pin
then also keeps to snapshots made in that repository, should two ever claim
the same ID. `"ftsfr/fed_yield_curve" = {}` pins the same snapshot, but
write the address. (`chartbook add https://github.com/ftsfr/fed_yield_curve`
is how such a line gets written: it adds the line, address included, and
pins the newest snapshot in your stores.) What the report got for it is in
`chartbook.lock`. Two records matter for one dependency: the dependency's
entry, and the record of each asset pinned from it.

```console
jq '.records[0].dependencies[] | select(.id=="ftsfr/fed_yield_curve")' chartbook.lock
jq '.records[] | select(.id=="ftsfr/fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon")' chartbook.lock
```

```text
  "id": "ftsfr/fed_yield_curve",
  "via": "store",
  "location": { "kind": "store", "commit": "5bb7217c30f422bab5c9b87e3fc3410461ea4391",
                "repository": "https://github.com/ftsfr/fed_yield_curve", ... },
  "snapshot": "2026-11-04T05:46:01.793770Z",
  "pinned_hash": "sha256:29b80f24a77d83736a5bd0343abae385097bd47340c76b493530686d7e636280"

  "id": "ftsfr/fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon",
  "location": { "kind": "file", "path": "./_data/ftsfr_treas_yield_curve_zero_coupon.parquet" },
  "content_hash": "sha256:4c04214626a01006a131ff11fa391453a52c6f1dc15d5daede2d7704642a2506",
  "size_bytes": 5376252
```

Four fields to know by name:

| Field | Says |
|---|---|
| `commit` | Which code built it: the upstream's commit, `5bb7217` |
| `snapshot` | When the snapshot was made. This is the snapshot's name, not when you pinned it; the lockfile records no time of its own |
| `pinned_hash` | One hash for the whole dependency: a hash over every asset pinned from it (step C) |
| `content_hash` | One hash per file: the SHA-256 of its bytes |

Now the walk up from the lockfile:

```console
chartbook lineage
```

```text
finm-32800/finm_midterm_report (this folder)
├── ftsfr/fed_yield_curve: ftsfr/fed_yield_curve 5bb7217, made 2026-11-04 05:46:01 UTC, pins checked
│   ... (6 lines)
6 snapshot(s) upstream; no gaps.
```

Each line is a snapshot in a store, checked: it is there, it holds what was
pinned from it at those hashes, and its own pins cover its own dependencies.
Then read the data, and ask where it came from:

```console
python -c 'from chartbook import data
df = data.load("fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon", format="pandas")
print(df.shape, df.ds.max())
print(data.path("fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon"))'
```

```text
(382802, 3) 2026-10-30 00:00:00
<store>/pipelines/ftsfr+fed_yield_curve/2026-11-04T054601Z_5bb7217c30f4/_data/ftsfr_treas_yield_curve_zero_coupon.parquet
```

The path is in the store, under the snapshot's name (its time and commit),
and the report's code never wrote it down: `data.load` resolved the
reference through `chartbook.lock`. Both spellings of the reference work,
`fed_yield_curve:...` and `ftsfr/fed_yield_curve:...`; the exhibits use the
long one. `doit` watches that same file:

```console
doit info exhibit:yield_curve
```

```text
file_dep   :
 - ./src/settings.py
 - <store>/pipelines/ftsfr+fed_yield_curve/2026-11-04T054601Z_5bb7217c30f4/_data/ftsfr_treas_yield_curve_zero_coupon.parquet
 - ./src/chart_style.py
 - ./src/exhibits/yield_curve.py
```

`dodo.py` builds that list with `data.path(...)` for every reference in the
exhibit's `UPSTREAM`. Build it:

```console
uv run doit
doit forget exhibit:yield_curve && doit
```

```text
.  exhibit:yield_curve
```

A full build from clean takes about a minute; a second `doit` does nothing.
After `doit forget`, only that exhibit reruns, and nothing after it: the PNG
came out byte-identical, and `doit` compares inputs by hash. Open
`reports/class_report.pdf` at the yield curve page: those are the numbers.

The point: the report names data, never a path. The path comes from the
pin, `doit` watches the pinned file, and the exhibit reruns when, and only
when, the pin moves. Data validation would catch bad data; the pin catches
*different* data.

(walkthrough-hashes-by-hand)=
## C. The hashes, by hand

What you are about to see: every hash in the lockfile can be recomputed
with `shasum` and `jq`. First the file:

```console
shasum -a 256 "$(python -c 'from chartbook import data; print(data.path("fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon"))')"
```

```text
4c04214626a01006a131ff11fa391453a52c6f1dc15d5daede2d7704642a2506  <store>/.../ftsfr_treas_yield_curve_zero_coupon.parquet
```

That is the `content_hash` from step B. Then the dependency's hash, which is
a hash over a listing, one line per pinned asset, `<hex>  <ID>`, sorted by
ID. Write the listing first, then hash it:

```console
jq -r '.records[]
    | select(.pipeline == "ftsfr/fed_yield_curve" and .content_hash)
    | "\(.content_hash | ltrimstr("sha256:"))  \(.id)"' chartbook.lock \
    | LC_ALL=C sort -k2
```

```text
a2eb227eec63003141a823dddac484f27a320749a2061140c5a42f7532abadb7  ftsfr/fed_yield_curve:chart/yield_curve_replication
4c04214626a01006a131ff11fa391453a52c6f1dc15d5daede2d7704642a2506  ftsfr/fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon
103efb4b65d78515b9c4434fc6f75aca17e20298db6d5db7d7bb4d382959e53a  ftsfr/fed_yield_curve:notebook/gsw_crsp_replication_ipynb
cf4e6b4f98afa7705141ca6b8e13bfa56ef0514cda5b48c08e8a2e5004d70a45  ftsfr/fed_yield_curve:notebook/summary_fed_yield_curve_ipynb
```

Four assets: a chart, the dataframe, two notebooks. Pipe the same listing
into `shasum`, and read the lockfile's answer next to it:

```console
jq -r '.records[]
    | select(.pipeline == "ftsfr/fed_yield_curve" and .content_hash)
    | "\(.content_hash | ltrimstr("sha256:"))  \(.id)"' chartbook.lock \
    | LC_ALL=C sort -k2 | shasum -a 256
jq -r '.records[0].dependencies[] | select(.id == "ftsfr/fed_yield_curve") | .pinned_hash' chartbook.lock
```

```text
29b80f24a77d83736a5bd0343abae385097bd47340c76b493530686d7e636280  -
sha256:29b80f24a77d83736a5bd0343abae385097bd47340c76b493530686d7e636280
```

The same hash. (The recipe matches for all six dependencies in the
report's lockfile.) Change one byte of one file upstream and the file's
`content_hash` changes, so the listing changes, so the `pinned_hash`
changes: one line of a diff says something moved.

Now git, for contrast. A commit is a hash over its tree and its parent; a
tree is a hash over the names and hashes of what is inside it.

```console
git cat-file -p HEAD
git cat-file -p 'HEAD^{tree}' | head -12
git rev-parse HEAD:chartbook.lock
printf 'blob %s\0' $(wc -c < chartbook.lock | tr -d ' ') | cat - chartbook.lock | shasum -a 1
```

```text
tree df84ef48f8d89245a81da7b13581f98ae4c7b4b5
parent c914aea725d756022dbb129bce564a1e6c2f8afc
author Jeremy Bejarano <...> 1794324214 -0600
...
100644 blob dff2c50609d63181fc80dba80e5f685a78b4aea3	chartbook.lock
100644 blob 712f4033781c74ec6d75cf412b6e9b2b47344f9d	chartbook.toml
040000 tree ab75f65e5b03acf8a83ac63997dd3b7ed29690f5	data_manual
dff2c50609d63181fc80dba80e5f685a78b4aea3
dff2c50609d63181fc80dba80e5f685a78b4aea3  -
```

The last two lines are git's hash of `chartbook.lock` and the same hash made
by hand: SHA-1 of `blob <size>\0` followed by the file. The lockfile's own
hash sits in the tree, the tree's in the commit, the commit's in its child.

The point: git's one commit hash covers every file and all of history, a
full Merkle tree. ChartBook stops a level short: a `pinned_hash` covers one
dependency's files, and a snapshot is named by its time and commit, not by a
root hash. `chartbook lineage` checks the graph edge by edge instead.
(`chartbook lineage --rehash` also reads every byte again, and
`chartbook store verify` rehashes a whole store.)

## D. A live git dependency

What you are about to see: a public branch that grows by a row a day
becomes a pinned upstream, with no snapshot made by its owner. The example
is the
[Yield Spread Monitor](https://github.com/finm-32800/yield_spread_monitor)
from [week 5](../Week5/live_monitor.md), whose `monitor-history` branch
holds `spread_history.csv`. For HW 4, your version of this step is your own
HW 3 monitor; *Make Your Homework an Upstream* on the [HW 4](../HW4.md) page
has the exact files for it.

That branch carries three small files beside the CSV, and they are what
make it a ChartBook pipeline. (To add such files to a branch of your own,
use GitHub's web interface: switch to the branch, then **Add file**,
committing to that branch and not a new one.) The daily workflow writes
only `spread_history.csv` there, so the files stay. `chartbook.toml`:

```toml
[project]
name = "Yield Spread Monitor (history)"
description = "The 10-year minus 2-year Treasury yield spread, one row per business day since June 1976, recorded by the monitor's daily workflow."
maintainer = "Jeremiah Bejarano"
contributors = ["Jeremiah Bejarano"]
repo_url = "https://github.com/finm-32800/yield_spread_monitor"
os_compatibility = ["Windows", "Linux", "macOS"]

[dataframes.spread_history]
name = "10-Year Minus 2-Year Treasury Spread"
description = "Daily 10-year minus 2-year Treasury yield spread, in percentage points, from FRED's DGS10 and DGS2."
sources = ["FRED"]
providers = ["Federal Reserve Bank of St. Louis"]
provider_links = ["https://fred.stlouisfed.org/"]
tags = ["Rates", "Treasury"]
pull_method = "FRED API, src/pull_yields.py on main, run daily by GitHub Actions"
path = "spread_history.csv"
date_col = "date"
docs = "Two columns: date, and spread (DGS10 minus DGS2, in percentage points). The daily workflow on main appends one row each business day; nothing else writes this branch."
```

`.gitattributes`, one line, so that git on Windows does not rewrite the
CSV's line endings on checkout (which would change its bytes, and its hash):

```text
* -text
```

And `README.md`:

```markdown
# Yield Spread Monitor: history

The state the daily workflow carries from one day to the next, declared as a ChartBook dataframe. The code is on `main`.

To read it from another ChartBook pipeline, add this line to `[dependencies]` in its `chartbook.toml`, then run `chartbook pin`: ...
```

There is no `[scripts] build`: this branch builds nothing. Now, in the
report, on a branch of your own, add the dependency and pin it:

```console
git switch -c demo/yield-spread
echo '"finm-32800/yield_spread_monitor" = { git = "https://github.com/finm-32800/yield_spread_monitor", rev = "monitor-history" }' >> chartbook.toml
chartbook pin
git diff chartbook.lock
```

```text
finm-32800/yield_spread_monitor: fetched https://github.com/finm-32800/yield_spread_monitor at aff8f6d (monitor-history)
finm-32800/yield_spread_monitor: 1 pinned
... 6 lines "already pinned"
Wrote chartbook.lock.
+          "id": "finm-32800/yield_spread_monitor",
+          "via": "rev",
+          "location": { "kind": "git", "url": "...", "rev": "monitor-history",
+            "commit": "aff8f6d5e6f2afa885ea6b431442702d2337fab9" },
+          "pinned_hash": "sha256:4385c35b8571..."
+      "id": "finm-32800/yield_spread_monitor:spread_history",
+      "location": { "kind": "file", "path": "spread_history.csv" },
+      "content_hash": "sha256:40a4699920c6...", "size_bytes": 202541
```

`chartbook pin` cloned the repository, read the files the branch's
`chartbook.toml` declares, copied them into your own store (the first one,
which is why it must be writable), and recorded the commit and the hash.
The six other dependencies say `already pinned`: nothing else moved,
which is what a reviewer of your pull request checks. The key on the left
of `=` is your name for the dependency, and references use it; use the ID
ChartBook derives from the URL, the owner as it is and the repository name
with `-` written `_`, so that the two agree. Read it, and commit:

```console
python -c 'from chartbook import data
df = data.load("yield_spread_monitor:spread_history", format="pandas"); print(df.tail(3), len(df))'
git commit -qam "Depend on the yield spread monitor history"
chartbook lineage | grep spread
```

```text
             date  spread
12603  2026-11-04    0.32
12604  2026-11-05    0.38
12605  2026-11-06    0.35 12606
├── finm-32800/yield_spread_monitor: pinned from its git clone at aff8f6d; not walked further
```

`date` comes back as strings, because the file is a CSV; an exhibit calls
`pd.to_datetime`. Lineage stops at a git dependency: there is no snapshot
to walk into.

**Then the branch moves.** The daily run adds a row at 22:00 UTC. (For
this page, the branch was moved by editing its README instead, so that the
commit changed and the data did not.) `pin` and `update` do
different things:

```console
chartbook pin | grep spread
chartbook update yield_spread_monitor
git diff chartbook.lock
```

```text
finm-32800/yield_spread_monitor: 1 already pinned
chartbook.lock is unchanged.

finm-32800/yield_spread_monitor: fetched ... at f2180f9 (monitor-history)
-            "commit": "aff8f6d5e6f2afa885ea6b431442702d2337fab9"
+            "commit": "f2180f93cfa533511c420786e1060e1b3c3a5bc5"
             "pinned_hash": "sha256:4385c35b8571..."      (unchanged)
```

`pin` moved nothing. `update` fetched again, followed the branch to its new
commit, and the diff shows exactly that: the commit line moved and no hash
did, because the data did not change. `data.path` still points at the same
stored file, so `doit` rebuilds no exhibit. After a day with a new row, the
same two commands give a diff with a moved hash, of this shape:

```text
finm-32800/yield_spread_monitor: 1 moved
  finm-32800/yield_spread_monitor:spread_history
    sha256:40a4699920c6… -> sha256:<the new file's>…
-            "commit": "f2180f93cfa533511c420786e1060e1b3c3a5bc5"
+            "commit": "<the day's commit>"
-          "pinned_hash": "sha256:4385c35b8571..."
+          "pinned_hash": "sha256:<new>..."
-      "size_bytes": 202541
+      "size_bytes": 202557
```

Sixteen more bytes: one row. The point: a pin never moves by itself. When it
moves, the lockfile diff says what moved, and that diff is what you commit
and what your reviewers read.

## E. Rebuild versus read

What you are about to see: the same code, run at a later point in time,
gives a different hash, and the hash tells you so. Step B read a snapshot
from a store. Now build the upstream yourself, at the commit the lockfile
names. The output in this step is from November 2, when the report pinned
commit `3904ad6` and a snapshot that ended October 2; your commit is the
one your lockfile names (`5bb7217` since November 3), and your dates are
later. In a second terminal, so that the report's environment stays active
in the first:

```console
git clone https://github.com/ftsfr/fed_yield_curve
cd fed_yield_curve
git switch --detach 5bb7217
python3.12 -m venv ../.venv && source ../.venv/bin/activate
pip install -r requirements.txt
chartbook trust
chartbook run --snapshot
```

(`5bb7217` is the `commit` from step B; read it from the lockfile with
`jq -r '.records[0].dependencies[] | select(.id=="ftsfr/fed_yield_curve") | .location.commit' chartbook.lock`
in the report. Python 3.12, because the pipeline pins `numpy==1.26.4`;
its `requirements.txt` pins `pyarrow==20.0.0`, which is what wrote the
pinned file, and that matters at the end of this step. The environment
goes beside the clone, not inside it: the pipeline's `.gitignore` does not
list `.venv`, and `chartbook run --snapshot` refuses a folder with
uncommitted files.) `chartbook trust` says this folder's
build script may run on your machine. `chartbook run --snapshot` checks that
everything is committed, builds (about a minute: it pulls the Fed's yield
curve file, formats it, runs the tests and two notebooks, and draws the
chart), and copies what it built into your store:

```text
ftsfr/fed_yield_curve: build
...
ftsfr/fed_yield_curve at 3904ad6: 4 asset(s) in the store
Snapshot made. A pipeline that depends on this one gets it with `chartbook update`.
Stores: default
```

Compare what you built with what the report pinned:

```console
shasum -a 256 _data/ftsfr_treas_yield_curve_zero_coupon.parquet
```

```text
bc106bbafd76e3ac33ee0b60bc0c8a1ccf96b09153d0773529853427766ec005  _data/ftsfr_treas_yield_curve_zero_coupon.parquet
```

Pinned `b63da040...`, rebuilt `bc106bba...`. Same commit, different bytes.
The rebuild runs through October 23 (382,652 rows); the pin ends October 2
(382,232 rows). The rows they share agree exactly: the Fed published three
more weeks and revised nothing. Back in the report, ChartBook sees the newer
snapshot in your store and says so, without moving anything:

```console
chartbook pin | grep -A5 fed_yield
```

```text
ftsfr/fed_yield_curve: 4 changed upstream since it was pinned
  changed upstream: ftsfr/fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon
  ... (chart, two notebooks)
4 pinned asset(s) have changed upstream. `chartbook update` moves the pins.
```

What you see depends on the day. If the Fed has published nothing since
the pin, as on November 10, the rebuilt parquet is byte for byte the pinned
one: `chartbook pin` then says `1 already pinned, 3 changed upstream` (the
chart and the two notebooks, which every build renders anew), and no
exhibit rebuilds after an update.

To see what moving the pin does, move it, build, and undo:

```console
chartbook update ftsfr/fed_yield_curve && git diff chartbook.lock | head -30
doit
git restore chartbook.lock && doit
```

```text
ftsfr/fed_yield_curve: 4 moved
  ftsfr/fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon
    sha256:b63da0404cf9… -> sha256:bc106bbafd76…
  From its snapshot at 3904ad6, made 2026-11-02 22:51 UTC, in store default.
-          "snapshot": "2026-11-02T05:22:19.659609Z",
+          "snapshot": "2026-11-02T22:51:43.146901Z",
-          "pinned_hash": "sha256:aff4ea7deb55..."
+          "pinned_hash": "sha256:1b172b12d4c4..."
-- exhibit:factor_returns  -- exhibit:gdp_growth  -- exhibit:size_value_returns
.  exhibit:yield_curve
.  compile_report  .  compile_style_guides  .  build_chartbook_site  .  run_pytest
```

The commit line did not move; the snapshot's time and every hash did.
Exactly the one exhibit that reads this pipeline rebuilt, and the PDF after
it. Do not commit this in the shared report: the snapshot exists in your
store only, so every classmate's build, your reviewers' included, would
fail at `chartbook pin --frozen`. The HW 4 rule is that the pins in
`chartbook.lock` are shared; if your exhibit needs the newer data, run
`chartbook update <name>` and say so in your pull request, where the
lockfile diff shows what moved.

Finally, does the rebuild reproduce the pin if you cut it at the pinned
date (the last date the pin holds, which step B printed)? Save this as `trim_and_hash.py` and run it in the clone, with the
clone's environment:

```python
"""Cut a rebuilt yield-curve file at the pinned date and hash what remains."""
import hashlib
import sys
from pathlib import Path

import pandas as pd
import pyarrow

src = Path(sys.argv[1])            # the rebuilt file
cutoff = sys.argv[2]               # the last date the pin holds
out = Path("trimmed.parquet")

df = pd.read_parquet(src)
df = df[df["ds"] <= cutoff]        # keep the index: the pipeline wrote it
df.to_parquet(out)
digest = hashlib.sha256(out.read_bytes()).hexdigest()
print(f"rows {len(df):,}, through {df['ds'].max():%Y-%m-%d}, "
      f"pyarrow {pyarrow.__version__}, pandas {pd.__version__}")
print(f"sha256:{digest}")
```

```console
python trim_and_hash.py _data/ftsfr_treas_yield_curve_zero_coupon.parquet 2026-10-02
```

```text
rows 382,232, through 2026-10-02, pyarrow 20.0.0, pandas 2.2.3
sha256:4c04214626a01006a131ff11fa391453a52c6f1dc15d5daede2d7704642a2506
```

Byte for byte the pin. The same rows written by pyarrow 26.0.0 hash to
`62a8f97f5b01...`, and by pyarrow 26.0.0 with pandas 3.1.0 to
`5bb9af54f6d1...`: same rows, three writers, three hashes.

The point: the hash pins the bytes, not the numbers. A rebuild reproduces a
pin only with the same code, the same inputs, and the same environment,
which is what `uv.lock` is for. When the hashes differ, the snapshot's
record says which commit made it and when, and that is where you start
looking.

## F. When do pins move

| Command | Pins |
|---|---|
| `chartbook add <ID>`, `chartbook pin` | Create a pin for a dependency that has none. Never move one; say `changed upstream` when a newer snapshot exists |
| `chartbook run` (and `uv run doit` in the report) | Never move one. The build reads the pinned snapshot. `run` also pins what has no pin yet, and builds a git dependency once |
| `chartbook update [<name>]` | Move: to the newest snapshot, or to what a branch means now. The lockfile diff is the record; commit it on its own |
| `chartbook pin --frozen`, `chartbook run --frozen` | Write nothing. Refuse if any pin is missing or not in a store. What CI runs |
| A git dependency with `rev = "<commit>"` | Cannot move: `update` fetches again and the commit means the same thing. A tag likewise; a branch moves |
| `chartbook store gc` | Keeps every snapshot a lockfile pins, the newest of each pipeline, and anything from the last seven days; removes the rest, after asking |

For HW 4: `chartbook pin` after you add a line to `[dependencies]`, read the
diff, commit `chartbook.lock`, and leave the other pins where they are.
