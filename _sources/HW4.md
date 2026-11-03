# Homework 4: The Class Report

- **Launched:** Tuesday, November 3, 2026, in the week 6 session.
  **Due:** Friday, November 20, 2026, at 11:59 pm Chicago time. Also posted
  on Canvas.
- **Link to Assignment:** the private repository
  [finm-32800/finm_midterm_report](https://github.com/finm-32800/finm_midterm_report).
  Every student gets a collaborator invite to it by email. Accept it from the
  email or at [github.com/notifications](https://github.com/notifications).
- **Goes with:** week 6, and the pull requests of week 7

## Overview

The whole class writes one report on the economy and the financial
markets, in four parts:

- the state of the economy,
- the state of the financial system,
- the analytics of key industries,
- investment indicators.

It comes out two ways, one publication-style PDF and one ChartBook site,
and one command, `uv run doit`, builds both from a clean clone.

Your part is one exhibit: a figure or a table, with a short write-up that
says what the reader should take away. You choose the part and the topic.

What is new is where the data come from. In HW 1 to HW 3 every repository
pulled its own data. The report pulls almost none. Each exhibit reads data
that another repository produces, through a ChartBook dependency declared in
the report's `chartbook.toml` and pinned by content hash in
`chartbook.lock`. A change upstream reaches the report only when someone
moves the pin, and the move shows up as a diff of the lockfile in a pull
request. Week 6 covers the machinery:
[ChartBook: Pipelines, Stores, and Lineage](./Week6/chartbook_catalog.md).
The Summer 2026
edition of the report used ChartBook 0.1, which had no snapshots or pins.
This edition is built on them.

The report is also an exercise in code review. Every exhibit goes in through
a pull request that two classmates approve, and you review two of theirs.

The assignment is worth 10 points, plus 2 bonus points. The record on the
report repository settles six of the ten: the static checks, the reviews,
and the builds that the reviewers and the instructor report; the
instructor grades the other four.
**Nothing counts unless your pull request is merged by the deadline.**

**Budget your time.** The code is small: one Python file and one LaTeX
file. The hours go elsewhere. Choosing an exhibit worth a page of a report,
and finding the upstream that carries its data, takes the most thought. It
takes the most work when the upstream is your own homework, which is the
point of the bonus. The review runs on other people's calendars: two
classmates must read, build, and approve your pull request, and you must
answer their comments. **Open your pull request by Thursday, November 12,**
so the reviews can happen before the deadline.

## Learning Outcomes

- Depend on data that another repository produces, pinned by content hash,
  instead of copying its pull code.
- Read a lockfile diff and say what moved upstream.
- Explain the difference between lineage and provenance, using your own
  exhibit as the example.
- Review someone else's code, and answer a review of your own.
- Write an exhibit and a paragraph that carry one clear point, to a shared
  style guide.

## Before You Start

- **Accept your invite** to
  [finm-32800/finm_midterm_report](https://github.com/finm-32800/finm_midterm_report)
  and clone it.
- **Upgrade to ChartBook 0.5 or newer.** All homework moves to 0.5 this week,
  including any repository of yours that you turn into an upstream. Follow
  *Upgrade to ChartBook 0.5* in
  [ChartBook: Pipelines, Stores, and Lineage](./Week6/chartbook_catalog.md).
  `chartbook --version` should print `0.5.0` or newer.
- **ChartBook's skill for Claude Code** (optional). If you use Claude Code,
  `chartbook install claude-skill` installs ChartBook's skill for every
  session on your machine, and `chartbook install claude-skill --project`
  puts it in the current repository instead, to commit with it.
- **`uv`**, as in HW 3. In the report repository, `uv run doit` installs the
  locked environment the first time and builds the report.
- **LaTeX.** The PDF is compiled with XeLaTeX. *Set up once* in the
  repository's README lists the packages it needs.
- **A store of your own.** The report reads each upstream's snapshot from a
  store on your machine, never from the upstream's working folder, so an
  upstream you are also editing cannot change beneath the report. ChartBook
  writes snapshots to the first store in your catalog, so register one
  before anything else:

  ```console
  mkdir -p ~/.chartbook/store
  chartbook store add mine "~/.chartbook/store"
  chartbook store info
  ```

  Every upstream you build lands there.

## The Data Rule

Every exhibit's data comes through a ChartBook 0.5 dependency. Pull code
copied from another repository, a file copied by hand, or a download inside
your exhibit's script does not count. There are three kinds of upstream:

1. **A pipeline you build yourself.** Clone it, check out the commit the
   report pins, run it with your own credentials, and `chartbook run
   --snapshot` puts its snapshot in your store. The FTSFR pipelines
   (`ftsfr/fed_yield_curve`, `ftsfr/ken_french_data_library`, and the
   others the report declares) are built this way: every leg of the report
   is a public repository you can run.
2. **A public git repository.** For example your own HW 3 monitor, whose
   history grows by a row every day on its `monitor-history` branch (the
   course does not provide HW 3's outputs; this is the only way they reach
   the report), or a public final-project repository. ChartBook pins the commit and the hash
   of every file it reads. The steps are below, under
   *Make Your Homework an Upstream*.
3. **The report's own FRED pull,** for public macroeconomic series. List the
   series your exhibit needs in `FRED_SERIES`, and `src/pull_fred.py` pulls
   them with everyone else's.

**Derived quantities only.** No raw licensed data in any exhibit or
commit: no rows of CRSP, no option settlement prices, no Databento prices. A
portfolio return, a fitted yield curve, a factor, or a probability is
derived; a security's price from a licensed vendor is not. The report is
read by people who have no license to that data, and so is every
repository it is built from.

**A committed cutoff.** Every upstream the report depends on has a committed
cutoff date in its settings (`END_DATE` in `src/settings.py`), and its code
reads nothing past it; the report's own window is `START_DATE` and
`END_DATE` in `src/settings.py`. A window is never a `.env` entry: two
people with different `.env` files who build the same commit must get the
same bytes, and ChartBook names a snapshot by its commit, so the commit has
to determine the bytes.

## Use Your Previous Homework

The report is meant to be built on what you have already built. HW 1 made
market and factor returns from CRSP, HW 2 fitted the yield curve and read
the policy path from fed funds futures, and HW 3 made a daily series of
option-implied crash probabilities. Each of those is a derived series that
the report could use, and each can become an upstream that others pin. The
FTSFR pipelines and past students' public final projects are upstreams of
the same kind.

## Make Your Homework an Upstream

This is the bonus route. The recipe below makes your HW 3 monitor an
upstream of the report. *Depending on a public repository* in
[ChartBook: Pipelines, Stores, and Lineage](./Week6/chartbook_catalog.md)
explains what `chartbook pin` does with a `git` dependency.

1. **The monitor repository is public,** in your own account, and has
   deployed at least once, so its `monitor-history` branch exists.
2. **Add three files to the `monitor-history` branch,** not to `main`:
   - a `chartbook.toml` that declares only the history: a `[project]` table
     with a `name` and a `description`, and a
     `[dataframes.svix_monitor_history]` table with a `name`, a
     `description`, `path = "svix_monitor_history.csv"`,
     `date_col = "date"`, and `docs`;
   - a `.gitattributes` containing `* -text`, so that line endings on
     Windows do not change the file's bytes, and with them its hash;
   - a one-line `README.md`.

   Add them in the GitHub web interface (switch to the branch, then **Add
   file**), or check the branch out in a second folder with
   `git worktree add ../history monitor-history`, commit, and push. The
   workflow's daily record step leaves these files alone. Never force-push
   that branch, and do not push around the time the daily run starts.
3. **In the report,** add one line under `[dependencies]` in
   `chartbook.toml`:

   ```toml
   "<you>/svix_monitor" = { git = "https://github.com/<you>/<repo>", rev = "monitor-history" }
   ```

   The key on the left is your own name for the dependency, and references
   use it (the ID ChartBook derives from the URL is `<you>/<repo>` with
   `-` written `_`). Then `chartbook pin`, and commit `chartbook.lock`. Your exhibit
   reads the history with
   `data.load("<you>/svix_monitor:svix_monitor_history", format="pandas")`,
   and `chartbook update <you>/svix_monitor` moves the pin to newer days.

A public final-project repository works the same way. Commit its derived
tables to a data branch (for example `report-data`), with a
`chartbook.toml` that declares only those files and has no
`[scripts] build`. Only data you may redistribute goes on that branch.

## Rebuild the Upstreams; the Report Reads Your Snapshots

The pin is the contract: the lockfile names each upstream's commit and the
hash of every file the report reads from it. You meet it by rebuilding each
upstream yourself. Clone the pipeline, check out the commit the lockfile
names, and run it with your own credentials (your WRDS account, for
example). `chartbook run --snapshot` there puts your build in your own
store, and its hashes say whether it matches the pin. For a `git`
dependency, `chartbook run` in the report does the build for you, once,
from the pinned commit. A rebuild runs the upstream's code with your
environment, so rebuild only repositories whose code you would run.

The report then reads the snapshot in your store, not the upstream's
working folder, with `uv run doit`. That is what the store is for: you can
keep editing the upstream, or rebuild it next week with newer data, and the
report keeps reading the bytes the pin names until you move the pin on
purpose. Reviewers build a pull request the same way, from the snapshots in
their own stores.

A hash ties each snapshot to its code at one commit and to a point in time,
so when the upstream's build is deterministic your rebuild produces the
bytes the pin names, and the hash is how you find out. When it does not
(the Fed has published more days since the snapshot, say), `chartbook pin`
reports that the pinned asset changed upstream. The pins committed in
`chartbook.lock` are shared by every exhibit, so do not move them in your
pull request unless your exhibit needs the newer data; if it does, run
`chartbook update <name>` and say so in the pull request, where the
lockfile diff shows exactly what moved. *Your rebuild and the pin* in
[ChartBook: Pipelines, Stores, and Lineage](./Week6/chartbook_catalog.md)
has the details, and
[Walkthrough: From a Working Folder to a Pin](./Week6/pins_walkthrough.md)
rebuilds `ftsfr/fed_yield_curve` and checks it against the pin, command by
command, with the output you should see.

**Final-project groups:** aim for at least one member's exhibit to relate to
your group's project, so that the work on the data counts twice. This is
encouraged, not graded.

## What You Do

1. **Claim an issue.** Each open issue is a topic in one of the four parts.
   Assign it to yourself, one student per issue. To propose your own topic,
   open an issue and say which part it belongs to.
2. **Work on a branch.**
3. **Write the exhibit** in `src/exhibits/<slug>.py`, where `<slug>` is a
   short name in lowercase letters, digits, and underscores. Copy the worked
   example, `src/exhibits/gdp_growth.py`. At the top, the file declares
   what it reads, where it goes, and who wrote it:

   ```python
   UPSTREAM = ["ftsfr/fed_yield_curve:ftsfr_treas_yield_curve_zero_coupon"]
   PART = "02_financial_system"
   AUTHOR = "<your GitHub username>"
   ```

   `PART` is one of `01_economy`, `02_financial_system`, `03_industries`,
   and `04_investment_indicators`. An exhibit that reads the FRED pull sets
   `UPSTREAM = ["fred"]` and lists its series in `FRED_SERIES`; a table sets
   `TABLE = True`. Read the data by name with `data.load(...)`, never with a
   path into someone else's folder. The script writes the plot area to
   `_output/exhibits/<slug>.png`; the title, subtitle, and source note are
   typeset by LaTeX.
4. **Write the write-up** in `reports/sections/<part>/<slug>.tex`: the
   exhibit, its title and source note, and a short paragraph that says what
   the reader should take away. Cite a source that shows why the topic
   matters; new references go in `reports/sections/<part>/<slug>.bib`, so
   that no two pull requests edit the same bibliography file. Refer to
   exhibits and sections with `\label` and `\ref`, never with typed numbers.
5. **Declare and pin a new upstream.** The FTSFR pipelines are declared
   and pinned already, so an exhibit that reads one changes nothing here.
   For
   any other upstream, add one line at the end of `[dependencies]` in
   `chartbook.toml`, run `chartbook pin`, and commit `chartbook.lock`. Read
   the lockfile diff before you commit it: it should add your upstream and
   move nothing else.
6. **Build it from clean.** The whole report must build with your exhibit
   in it, from the pinned snapshots in your store (`uv run doit`); an
   exhibit that adds a `git` dependency builds it first with
   `chartbook run`. Follow
   `CODE_STYLE.md` and the report and chart style guides in `reports/`, and
   run `ruff`.
7. **Open a pull request** and request review from two classmates. They
   build it and check that it reads well; you address their comments. The
   repository's *Static checks* run on GitHub and read files only (the
   declarations, the pins, the labels, the citations, `ruff`); they run
   none of your code, so they cannot say whether the report builds. Your
   reviewers say that. When both reviewers have built it and approved, and
   the static checks are green, the instructor builds it, reviews, and
   merges. If `chartbook.lock` conflicts after `main` moves, follow *When
   chartbook.lock conflicts* in the README. The mechanics are in
   [GitHub Issues and Pull Requests](./Week6/GitHub_pull_requests.md).
8. **Review two classmates' pull requests.** Clone the branch fresh and
   build it with `uv run doit`, read the exhibit and the write-up, and
   leave comments that would make it better. Your review says that it
   built (the pull request template has the line) and makes two
   substantive comments. Approve when it is ready.

## How It's Graded

10 points, plus 2 bonus points.

| Criterion | What it means | Points | Checked by |
|---|---|---|---|
| **It works** | The report builds from a clean clone through pinned upstreams, for both reviewers and the instructor. | 2 | Reviewers and instructor |
| **Style mechanics** | The chart style, the file layout, the `\label`, the citation, and `ruff`. | 2 | Static checks (CI) and reviewers |
| **Peer review** | Two approvals received, and two substantive reviews written. | 2 | GitHub's review record |
| **Topic and importance** | The exhibit says something about the current economy or markets that matters, and the citation shows why. | 2 | Instructor |
| **Message** | The write-up says what the reader should take away, and the exhibit shows it. | 2 | Instructor |
| **Bonus** | The exhibit's upstream is your own homework or final-project pipeline, as a public git dependency. | +2 | Instructor |

Credit requires each step to happen before the deadline. A pull request that
is unmerged at 11:59 pm on Friday, November 20, earns nothing, whatever its quality.
