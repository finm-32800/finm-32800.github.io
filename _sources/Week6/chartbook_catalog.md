# ChartBook: Pipelines, Stores, and Lineage

Official documentation:
[backofficedev.github.io/chartbook](https://backofficedev.github.io/chartbook/).

**Objectives of This Chapter**

1. Upgrade to ChartBook 0.5 or newer, in your environment and in your homework
   repositories, and install its Claude Code skill.
2. Discuss terminology: pipeline, catalog, store, snapshot, lockfile, pin,
   reference, lineage, and provenance.
3. Say which question each tool answers: `doit` (what is stale), an
   orchestrator (when it runs), and ChartBook (what this data is and where it
   came from).
4. Explain what ChartBook hashes and how, recompute a pin's hash by hand, and
   say how far the Merkle-tree analogy goes.
5. Explain what a snapshot records, how a store stays small and immutable, and
   how ChartBook compares with Delta Lake, Iceberg, git, and DVC.
6. Do the [HW 4](../HW4.md) workflow: build an FTSFR pipeline and snapshot
   it into your store, depend on it, pin it, read it by name, and commit the
   lockfile.

## Upgrade to ChartBook 0.5

All homework moves to ChartBook 0.5: version 0.5.0 or any later release
(0.5.1 fixes three bugs noted on this page and ships a better project
template; either works for the homework). Upgrade before class. Check what you
have:

```console
chartbook --version
```

In a pip or conda environment:

```console
pip install --upgrade "chartbook>=0.5.0"
```

In a uv project, change the dependency and the lockfile together:

```console
uv add "chartbook>=0.5.0" "polars>=1.33"
uv run chartbook --version
```

ChartBook 0.5 requires polars 1.33 or later. Our homework repositories pin
`polars==1.9.0`, and with that pin in place `uv add "chartbook>=0.5.0"` alone
fails to resolve: naming both packages in one `uv add` replaces both pins in
`pyproject.toml` and updates `uv.lock`. With pip, the upgrade pulls in a newer
polars by itself, but the next `pip install -r requirements.txt` would put the
old pins back, so edit `requirements.txt` too (next section). Either way polars
moves forward a long way (the 2.x series, as of this writing), so run your tests
after upgrading.

### Upgrading your homework repositories

Each homework repository was written for ChartBook 0.1.1. The general steps:

1. **Requirements.** In `requirements.txt`, replace `chartbook==0.1.1` with
   `chartbook>=0.5.0` and `polars==1.9.0` with `polars>=1.33`, then
   `pip install -r requirements.txt`. In a uv project, run the `uv add` line
   above and commit `pyproject.toml` and `uv.lock`.
2. **`dodo.py`.** `chartbook build -f` is gone in 0.5. Replace it with
   `chartbook docs build -f`. `chartbook browse` is now `chartbook docs open`.
   An old spelling stops with a message that names the new one.
3. **Trust.** ChartBook runs a folder's build script only in a folder you have
   said is yours. Run `chartbook trust` once in each homework folder; until you
   do, `chartbook docs build` renders the site as it would one from a store (no
   query run, no file read from outside the folder), and `chartbook run` asks
   first.
4. **An old catalog.** If you made `~/.chartbook/chartbook.toml` with
   `chartbook catalog add` under 0.1, run `chartbook catalog migrate` once.
   Catalogs no longer list working folders; they read stores.
5. **Check and rebuild.** `chartbook check` reads the manifest, and `doit`
   rebuilds. Commit what changed.

### Help from Claude Code

ChartBook ships a skill for Claude Code that describes its commands, its
manifest, and its data API:

```console
chartbook install claude-skill              # user-level: ~/.claude/skills/chartbook/
chartbook install claude-skill --project    # this repository: ./.claude/skills/chartbook/
```

The user-level copy is available in every Claude Code session on your machine.
The `--project` copy is meant to be committed with a repository. The skill
names the ChartBook version it was written for, and after an upgrade any
`chartbook` command you run at a terminal tells you when your copy is older
than your ChartBook, so rerun the install after each upgrade.

(try-it-three-pipelines)=
## Try it: three pipelines and a catalog

Three FTSFR pipelines build from public data and need no credentials: the
Fed's fitted Treasury yield curve, the Ken French Data Library, and the He,
Kelly, and Manela intermediary factors. These commands clone the three, build
each one, snapshot it into your store, and render a catalog site that lists
all three. They take a few minutes, most of it downloading packages.

```console
mkdir ftsfr && cd ftsfr
git clone https://github.com/ftsfr/fed_yield_curve
git clone https://github.com/ftsfr/ken_french_data_library
git clone https://github.com/ftsfr/he_kelly_manela

uv venv --python 3.12
source .venv/bin/activate           # Windows: .venv\Scripts\activate
uv pip install -r fed_yield_curve/requirements.txt "pyarrow<26"

chartbook trust                     # trusts every pipeline in this folder

cd fed_yield_curve
chartbook run --snapshot            # builds it (doit), then snapshots it into your store
cd ..

cd ken_french_data_library
chartbook run --snapshot
cd ..

cd he_kelly_manela
chartbook run --snapshot
cd ..

chartbook catalog init --title "FTSFR"
chartbook catalog build
chartbook catalog open
```

No store is set up by hand: the first `chartbook run --snapshot` creates
`~/.chartbook/store`, the default, and `chartbook store info` shows it. The
catalog site is the one thing that wants a line of setup, `chartbook catalog
init`, which writes `~/.chartbook/catalog.toml` naming that store.

One environment serves all three, because `fed_yield_curve`'s requirements
include everything the other two need. Python 3.12, because the pipelines pin
NumPy 1.26.4, which has no wheels for later Pythons. `"pyarrow<26"`, because
the requirements leave pyarrow unpinned and pyarrow 26 needs NumPy 2: without
the cap, each build stops at its first Parquet write.

Then read the data by name, from any folder:

```python
from chartbook import data

curve = data.load("fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon")   # a polars LazyFrame
```

`chartbook ls dataframes` lists the seven dataframes the three pipelines
declare. The snapshots are step 1 of [the HW 4 workflow](#the-hw-4-workflow);
the rest of this page explains what each command did.

## The problem: data that other repositories made

By now you have built several pipelines, each a repository where `doit` pulls
data, transforms it, and leaves dataframes, charts, and notebooks. Real work is
rarely one repository deep. Your HW 4 contribution might need the Fed's fitted
yield curve, the Fama-French 25 portfolios, or option portfolio returns, and
another repository has already built each of them carefully. You can copy the
pull code (it drifts), copy the output file by hand (nobody knows how it was
made), or declare a dependency on the other repository's output and read it by
name. ChartBook does the third.

## Vocabulary

ChartBook uses a few words in exact senses
([its vocabulary page](https://backofficedev.github.io/chartbook/guide/vocabulary.html)).
Each one below has its ChartBook example.

| Word | What it is | In ChartBook |
|---|---|---|
| **pipeline** | One project that produces data | A folder with a `chartbook.toml` and its code in git. Its ID is `scope/name`, from the git remote: `ftsfr/fed_yield_curve` |
| **catalog** | A list of where to find pipelines, and a site over them | `~/.chartbook/catalog.toml`. Its `[stores]` table lists stores, in order. A catalog declares no data of its own and reads no working folder |
| **store** | A folder that keeps snapshots | `~/.chartbook/store`, or a folder a team shares (Google Drive, a shared disk, an S3 bucket). Each file is kept once |
| **snapshot** | What one pipeline built at one commit, declared ready by its owner | `<store>/pipelines/ftsfr+fed_yield_curve/2026-11-02T221410Z_31f453e04986/`, with its record in `_chartbook/snapshot.json` |
| **lockfile** | The exact versions a project was built against | `chartbook.lock`, committed beside `chartbook.toml`. No timestamp on purpose: a diff of it means a pin moved |
| **pin** | Freezing which snapshot of a dependency you read; also the record of it in the lockfile | `chartbook pin` makes pins and never moves one. `chartbook update` moves them |
| **reference** | The address of an asset | `pipeline:dataframe`, as in `fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon`. Charts and other kinds add the kind: `fed_yield_curve:chart/...` |
| **lineage** | The graph of where data came from: which inputs fed which outputs, at which versions | `chartbook lineage` walks it: your lockfile's pins, the pins each of those snapshots recorded, and so on up |
| **provenance** | The full record of one artifact's origin: what made it, from what, when | The provenance panel on every asset page: its hash and location, the pipeline and commit that built it, its inputs at their hashes |

ChartBook puts the relationship among its documents in one sentence: a
manifest asks, a lockfile records what was got, a snapshot records what was
made, and an index records what a site shows. The split between
`chartbook.toml` and `chartbook.lock` is the same split as between a
`pyproject.toml` and its `uv.lock`, applied to data.

## Three questions, three tools

| Question | Tool | Where its record lives |
|---|---|---|
| What is stale and must rerun? | `doit` | `.doit.db`: for each task, the MD5 of each `file_dep` when the task last ran. One machine, not committed |
| When does it run, and what happens if it fails? | An orchestrator: Apache Airflow, in [week 7](../overview_w7.md) | The orchestrator's database of runs, retries, and schedules |
| What is this data, where did it come from, and who uses it? | ChartBook | `chartbook.lock` in git, a record you commit and share; the snapshots it names, in a store on your machine |

`doit` keeps its state on one machine on purpose. Deleting `.doit.db`, or
`doit forget`, costs only a rerun, so nothing about the science may live there.
ChartBook's record is the opposite: it is committed and shared, because it is
the answer to "which data was this built from?" A month from now that question
has to have the same answer on your laptop, a teammate's, and the grader's.

The tools compose. ChartBook resolves a reference to a file in the store, and
`doit` watches that file:

```python
# dodo.py
from chartbook import data

def task_summarize():
    return {
        "file_dep": [
            data.path("fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon"),
            "src/summarize.py",
        ],
        "targets": [data.path("curve_summary")],
        "actions": ["python src/summarize.py"],
    }
```

Until you run `chartbook update`, the path and its bytes stay the same, and
`doit` skips the task. When `chartbook update` moves the pin to a newer
snapshot, the path now names other bytes, and the next `doit` reruns the task.
Nothing else upstream reaches your build. (`data.path` names one file. For
another pipeline's dataset of several files, a Hive folder, use
`data.files("pipeline:name")`, which lists every file.)

## Hashes and Merkle trees

A **cryptographic hash** such as SHA-256 maps any sequence of bytes to 256 bits.
Change one byte and the hash changes completely, and no one knows how to find
two inputs with the same hash. So a hash works as a name for content: two
files with the same SHA-256 are, for every practical purpose, the same bytes.

ChartBook names every asset by a hash, written `sha256:<hex>`, and computes it
one way (`chartbook/hashing.py`):

- **A file** is the SHA-256 of its bytes. `shasum -a 256 file.parquet` gives
  the same hex.
- **A folder or a glob** is the SHA-256 of a listing with one line per file,
  `<hex of the file's SHA-256>  <path inside the asset>`, sorted by path. That
  listing is exactly what `sha256sum` prints, so the hash can be reproduced in
  the shell:

  ```console
  cd _data/rates && find . -type f | sed 's|^\./||' \
      | grep -viE '(^|/)(\.DS_Store|Thumbs\.db|desktop\.ini|\._[^/]*)$' \
      | LC_ALL=C sort | xargs sha256sum | sha256sum
  ```

  The `grep` drops the files that operating systems create by themselves.
  (On macOS, `shasum -a 256` in both places gives the same answer.)
- **A dependency's `pinned_hash`**, one per dependency in your lockfile, is the
  SHA-256 of a listing with one line per pinned asset,
  `<hex of the asset's hash>  <asset ID>`, sorted by ID. It is a hash of
  hashes: if any pinned asset of that dependency changes, it changes.

### Recompute a pin by hand

You can check a lockfile with nothing but `jq` and `shasum`. Take the records
of one dependency, write the listing, and hash it:

```text
$ jq -r '.records[]
    | select(.pipeline == "ftsfr/fed_yield_curve" and .content_hash)
    | "\(.content_hash | ltrimstr("sha256:"))  \(.id)"' chartbook.lock \
    | LC_ALL=C sort -k2 | shasum -a 256
a5f96f21021fee0331f5c1785651cb482a0e9e7280a70337540c818723213dd5  -

$ jq -r '.records[0].dependencies[]
    | select(.id == "ftsfr/fed_yield_curve") | .pinned_hash' chartbook.lock
sha256:a5f96f21021fee0331f5c1785651cb482a0e9e7280a70337540c818723213dd5
```

The two agree. (These hashes come from a small test pipeline; yours will
differ.) Then `shasum -a 256` on the parquet file in the store gives the
`content_hash` the lockfile records for it.

### How far the Merkle analogy goes

A **Merkle tree** is a tree in which each node's name is the hash of its
children's names, so the root's hash covers every byte below it. Git is the
standard example, and a full **Merkle DAG**: a file is a *blob* named by its
hash; a folder is a *tree*, whose hash covers its entries' names and hashes;
a *commit* hashes its tree and its parent commits. One commit ID therefore
covers the whole repository and its entire history.

ChartBook uses the same idea in places and stops short of it in others:

- A folder asset's hash is a hash over its files' hashes, like a git tree
  (the listing format differs from git's).
- A `pinned_hash` is a hash over a dependency's asset hashes.
- A snapshot is **not** named by a root hash. Its name is the time it was made
  and its commit (`2026-11-02T221410Z_31f453e04986`). Its record lists the hash
  of every file it holds and, for each of its own dependencies, the commit and
  `pinned_hash` it was built against.

So lineage in ChartBook is a graph whose every edge is a hash, and it is
checked edge by edge rather than by comparing one root. `chartbook lineage`
starts from your lockfile and walks up: each snapshot named is in the store,
what was pinned from it is what it holds, each upstream `pinned_hash`
recomputed from the snapshot it names matches what was recorded, and each
snapshot's pins cover every dependency its manifest declares.
`chartbook lineage --rehash` also reads every byte again and checks each file
against its hash:

```console
$ chartbook lineage --rehash
student/my_figure (this folder)
└── fed_yield_curve: ftsfr/fed_yield_curve 31f453e, made 2026-11-02 22:14:10 UTC, pins checked
1 snapshot(s) upstream, 2 object(s) hashed again; no gaps.
```

In ChartBook 0.5.0, `--rehash` reports every object as a gap when more than
one store is registered. With the one store of the HW 4 setup below this does
not arise; with several, name the store that holds the snapshots for that
command: `CHARTBOOK_STORE="<path to that store>" chartbook lineage --rehash`.

A hash chain proves integrity, not correctness. It shows that these outputs
came from those inputs by that commit's code. Whether the code was right is
still a question for tests and review.

## Snapshots and stores

### What a snapshot checks and records

The owner of a pipeline declares a build ready with one command:

```console
chartbook run --snapshot
```

It checks that everything in the pipeline's folder is committed **before** it
builds (so you are not refused after an hour), runs the build script, and
checks again that the build changed no committed file and made no commit. It
checks that the pipeline's pins account for every dependency it declares. Then
it copies every declared asset into the store and writes the record:

| The record says | With |
|---|---|
| Which data this is | The SHA-256 of every file |
| Which code made it | The commit, and any git tags on it |
| What it was built from | For each dependency, the commit and `pinned_hash` this pipeline was pinned to |
| Under what conditions | Whether the build ran in the same command; the ChartBook, Python, and platform versions |
| What its data looks like | The pipeline's index: each dataframe's columns, row count, date range, and first rows |

The snapshot also keeps the README, `chartbook.toml`, `chartbook.lock`, and the
write-ups, taking only files git tracks, so a `.env` never reaches the store.
If nothing was rebuilt and the pins have not moved, no new snapshot is made.
New data with the same code makes a new snapshot at the same commit, which is
why a snapshot is named by its time as well as its commit.

### Immutable, small, and cleaned on request

Nothing in a store is moved or rewritten once written; only `chartbook store gc`
removes anything. A snapshot's file is the store's own bytes, a copy-on-write
clone where the disk supports it and a copy otherwise, never a link to the
owner's working file, so the owner's next build cannot change what you pinned.
`chartbook store verify` rehashes every object and reports any that no longer
match. A store keeps each file once, so a snapshot whose data grew by a year
holds only the new files and points to the rest; `chartbook store ls` calls such
a snapshot *shared*. Never delete a snapshot's folder by hand, because another
snapshot may keep files in it.

`chartbook store gc` lists what it would remove and asks first. It keeps the
newest snapshot of each pipeline, everything a lockfile pins, anything a git tag
or a model label names, and anything made in the last 7 days. The rest can go.

**Time travel** is pinning. Check out an old commit of your project, and its
`chartbook.lock` names the exact snapshots it was built against; `chartbook
add ID --commit <sha>` pins the newest snapshot made at a given commit of the
other pipeline. Either works for as long as the store still holds that snapshot,
which is what `gc`'s rules decide.

### Relatives

- **Delta Lake and Apache Iceberg** are *table formats*: a table stored as
  Parquet files plus metadata that says which files make up each version. Delta
  keeps an ordered transaction log (`_delta_log/`); Iceberg keeps a tree of
  metadata files and manifests. Both give ACID writes from many writers, time
  travel to an earlier version or timestamp, and cleanup of files no retained
  version needs (Delta's `VACUUM`, with a default retention of 7 days; Iceberg's
  snapshot expiration). ChartBook shares the ideas of files kept once across
  versions, time travel, and garbage collection with a retention window. It is
  not a table format: its unit is a whole pipeline's outputs at one commit,
  declared ready by a person, with the code commit, the documentation, and the
  lineage attached.
- **Git** is content-addressed and a full Merkle DAG, and it is where your code
  and your `chartbook.lock` live. Every clone carries every version, which is
  why large data does not go in git.
- **DVC** (Data Version Control) is the closest cousin. It keeps data in a
  content-addressed cache and a remote, commits small files that record each
  output's hash (MD5) to git, hashes a folder as a listing of its files'
  hashes, cleans with `dvc gc`, and can import data from another DVC repository
  at a recorded revision. DVC also runs pipeline stages (`dvc repro`), the job
  `doit` does here. ChartBook leaves the steps inside a repository to `doit` and
  concentrates on dependencies between repositories, their documentation, and a
  catalog over all of them.

### Why the data inside is Parquet

Parquet is a **columnar** format. A file is divided into **row groups**; within
each, the values of one column are stored together, encoded and compressed
column by column, and the file's footer records each column chunk's statistics
(minimum, maximum, null count). A reader can therefore skip row groups whose
statistics rule out a filter (**predicate pushdown**) and read only the columns
a query uses (**projection pushdown**). A Hive-partitioned folder
(`year=2024/part-0.parquet`) lets a reader skip whole files by their folder
keys. Polars exploits all three: a lazy `scan_parquet` followed by `.filter()`
and `.select()` pushes both into the scan, and `data.load` returns exactly such
a LazyFrame. [Medium-Sized Data Strategies](../Week8/medium_sized_data_strategies.md)
and the [Polars exercises](../Week8/polars_exercises.md) go further.

(the-hw-4-workflow)=
## The HW 4 workflow

The class report is many pipelines and one report. Each pipeline the report
reads is a public repository you can clone and run: the FTSFR pipelines.
You build each, snapshot it
into your store, and the report pins that snapshot. The store is yours and
stays on your machine. Its job is to hold what you built, as you
built it, so that the report reads those bytes while the upstream's working
folder goes on changing under your edits and rebuilds.

### 1. Build an upstream and snapshot it

Nothing to set up first: snapshots go to `~/.chartbook/store`, the default,
which ChartBook creates when the first one is made. `chartbook store info`
shows it.

```console
git clone https://github.com/ftsfr/fed_yield_curve
cd fed_yield_curve
git switch --detach 3904ad6          # the commit the report's lockfile names
uv venv --python 3.12 && source .venv/bin/activate && uv pip install -r requirements.txt "pyarrow<26"
chartbook trust                      # once per folder: this build script may run
chartbook run --snapshot             # builds (doit), then snapshots into your store
```

`chartbook run --snapshot` refuses if the working tree has uncommitted
changes, because the snapshot's record names the commit. The pipeline's
README says what credentials it needs; `fed_yield_curve` needs none. The
Python version and the pyarrow cap are explained [above](#try-it-three-pipelines).

### 2. Declare a dependency and pin it

In your pipeline's folder:

```console
chartbook add ftsfr/fed_yield_curve
git add chartbook.toml chartbook.lock
git commit -m "Depend on ftsfr/fed_yield_curve"
```

`chartbook add` writes `"ftsfr/fed_yield_curve" = {}` under `[dependencies]` in
`chartbook.toml` and pins the newest snapshot in your stores. You can also write
the `[dependencies]` lines yourself and run `chartbook pin`. Commit
`chartbook.lock`: it is how anyone who clones your repository knows which data
you used.

### 3. Read by name

The name before the colon is the part of the ID after the slash:

```python
from chartbook import data

curve = data.load("fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon")   # a polars LazyFrame
ff25 = data.load(
    "ken_french_data_library:ftsfr_french_portfolios_25_daily_size_and_bm",
    format="pandas",
)
```

Every FTSFR dataframe is in long format with three columns: `unique_id` (which
series), `ds` (the date), and `y` (the value). For the yield curve, `unique_id`
runs `SVENY01` to `SVENY30` and `y` is in percent. Your code names the data, not
a path on your disk, so it runs unchanged on any machine that has the store.

### 4. Check, and move forward on purpose

```console
chartbook lineage          # every snapshot upstream, each edge checked
chartbook update           # move pins to the newest snapshots; the lockfile diff shows what moved
```

When a newer snapshot appears in a store (you rebuilt the upstream, say, and
the Fed has published more days), `chartbook pin` and `chartbook run` tell you
a pinned asset changed upstream and leave your pins alone.
`chartbook update` moves them, and the `git diff chartbook.lock` shows each
asset's old and new hash. Commit that diff as its own commit.

On a machine that can reach the stores your lockfile names,
`chartbook run --frozen` builds only if every pin is complete and in the store,
writes no lockfile, and lists what is missing otherwise. That is the command a
build server runs.

### Your rebuild and the pin

The pin is the contract. Your lockfile says which snapshot of
`ftsfr/fed_yield_curve` you used, and that snapshot is tied by its record to the
commit that built it and the moment it was made. Your rebuild is what meets
it: clone the upstream, supply your own credentials where it needs them (WRDS,
for example), build it at the pinned commit, and `chartbook run --snapshot` in
your clone puts its snapshot in your store, where the report reads it. A
rebuild runs the upstream's code with your environment, as `pip install` does
with a package's code, so rebuild only repositories you would run code from.

Your rebuild gives the pinned bytes when the upstream build is deterministic:
the same commit and the same inputs give the same bytes. Often they do not
quite. FRED revises past values, a build may write today's timestamp into a
file, and a different version of the Parquet writer can write different bytes
for the same table. The hash is what reveals the difference: it tells you
whether your rebuild matches the pinned snapshot. That is what hashing is for.
When the hashes differ, the record of each snapshot says which commit made it
and when, which is where you start looking.

### Depending on a public repository

A dependency can also be a public git repository instead of a store:

```toml
[dependencies]
"<you>/<repo>" = { git = "https://github.com/<you>/<repo>", rev = "monitor-history" }
```

`rev` may be a branch, a tag, or a commit. `chartbook pin` clones the
repository, reads the files its `chartbook.toml` declares and that are
committed at that rev (such as the history a monitor commits to its
`monitor-history` branch), and copies them into your own store, the first one in
your catalog, so that store must be writable. The repository's owner makes no
snapshot. The pin stays where it is as the branch moves, until
`chartbook update`. The dependency's pipeline ID comes from its URL: the owner in
lowercase, a slash, and the repository's name in lowercase with `_` for `-`
(the owner keeps its hyphens), so `https://github.com/<you>/svix-monitor` is
`<you>/svix_monitor`, unless the repository sets `[project] id`. The key you write on the left of `=` in
`[dependencies]` is your own name for the dependency, and references such as
`data.load("<key>:<dataframe>")` use it. A private
repository fails at once, since ChartBook never asks for a password.

This is the route for data that lives in a public repository rather than in a
store. `chartbook run` also builds what such a repository builds and does not
commit, by running its build script in ChartBook's own copy of that commit
(the rebuild route in the previous section).

A repository whose data branch is read on Windows should commit a
`.gitattributes` file containing `* -text`. Otherwise git's line-ending
conversion (`core.autocrlf`) changes the bytes of text files such as CSVs on
checkout, and with them their hashes.

## FTSFR: many pipelines, one store

The [FTSFR organization](https://github.com/ftsfr) is this arrangement at
production scale: one ChartBook pipeline per dataset, each its own repository,
each buildable and snapshottable on its own, and a catalog site that lists
every pipeline with a snapshot in a store. The research behind it is in
[FTSFR](../Week3/ftsfr.md).

The report pins these; each is a repository you clone, build, and snapshot
into your store:

| Dependency | Dataframes |
|---|---|
| `ftsfr/fed_yield_curve` | `ftsfr_treas_yield_curve_zero_coupon` |
| `ftsfr/ken_french_data_library` | `ftsfr_french_portfolios_25_daily_size_and_bm`, `ftsfr_french_portfolios_25_daily_size_and_inv`, `ftsfr_french_portfolios_25_daily_size_and_op` |
| `ftsfr/he_kelly_manela` | `ftsfr_he_kelly_manela_factors_monthly`, `ftsfr_he_kelly_manela_factors_daily`, `ftsfr_he_kelly_manela_all` |
| `ftsfr/corp_bond_returns` | `ftsfr_corp_bond_portfolio_returns`, `ftsfr_corp_bond_returns` |
| `ftsfr/options` | `ftsfr_cjs_option_returns`, `ftsfr_hkm_option_returns` |
| `ftsfr/nyu_call_report` | `ftsfr_nyu_call_report_leverage`, `ftsfr_nyu_call_report_cash_liquidity`, and four more bank-level and quartile series |

`chartbook ls dataframes` lists them on your machine, and
`chartbook get-docs fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon` prints
a dataframe's write-up. Pick the dataset closest to your report topic and, where
you can, one that also serves your final project.

**Further reading:** ChartBook's guides to
[depending on another pipeline](https://backofficedev.github.io/chartbook/guide/depending-on-pipelines.html)
and [catalogs and stores](https://backofficedev.github.io/chartbook/guide/catalogs-and-data.html),
the [CLI reference](https://backofficedev.github.io/chartbook/reference/cli.html),
and the design note on the
[asset graph](https://backofficedev.github.io/chartbook/design/asset-graph.html),
which explains the choices above and the prior art (Nix, Cargo, DVC, Snakemake)
they borrow from.
