# The ChartBook Catalog: Data Dependencies Across Repositories

**Objectives of This Chapter**

1. Understand the difference between a ChartBook *pipeline* and a ChartBook *catalog*, and why a catalog is the right tool for sharing data between repositories.
2. Upgrade to ChartBook 0.1.x and understand what changed in this release (a breaking manifest redesign, catalog auto-discovery, and a simpler install).
3. Set up a personal catalog on your own machine, register cloned pipeline repos in it, and load their data by name with `data.load(...)`.
4. Apply this to the collaborative report ([HW 5](../HW5.md)): build your figure on top of data produced by the [FTSFR pipeline repos](https://github.com/orgs/ftsfr/repositories) instead of re-writing data pulls from scratch.

## The Problem: Dependencies Between Repositories

By now you have built several *pipelines*: repositories where `doit` pulls raw data, transforms it, and produces registered dataframes, charts, and notebook reports. Each pipeline is self-contained, which is exactly what makes it reproducible.

But real analytical work is rarely one repository deep. Your collaborative-report figure might need consolidated CRSP Treasury data, or the Fed's published yield curve, or Markit CDS spreads. Some other repository has *already* solved that data problem, carefully. Your options:

1. **Copy the pull code** into your repo. Fast today, painful forever: the copy drifts out of date, bugs get fixed in one place but not the other, and every project pays the full data-cleaning cost again.
2. **Copy the output file** by hand. Now nobody knows how that parquet was made. This violates everything this course stands for.
3. **Declare a dependency** on the other repository's *registered output*, and resolve it by name at load time.

Option 3 is what the ChartBook **catalog** does. A pipeline registers its dataframes in its `chartbook.toml`; a catalog aggregates many pipelines and gives every dataset a stable, loadable address: `pipeline` + `dataframe`. Downstream code then says *what* it needs, not *where it lives on your disk*:

```python
from chartbook import data

df = data.load(pipeline="ftsfr/fed_yield_curve", dataframe="fed_yield_curve", format="pandas")
```

This is the same discipline that package managers brought to code dependencies (`import finm` instead of copy-pasting functions), applied to data.

## ChartBook 0.1.x: What Just Changed

This week ChartBook shipped its biggest release so far (`0.1.0` on July 22, `0.1.1` on July 23). Upgrade before class:

```console
pip install --upgrade chartbook
```

Highlights of the release, from the [changelog](https://backofficedev.github.io/chartbook/changelog.html):

- **Single install.** `pip install chartbook` now installs everything: data loading, plotting, the Sphinx documentation toolchain, and the `chartbook init` scaffolder. The old `[data]`, `[plotting]`, `[sphinx]`, and `[all]` extras are deprecated no-op aliases.
- **Breaking: manifest format v2.** The `chartbook.toml` format was redesigned, and v1 files are no longer supported. The old `[config]`, `[site]`, and `[pipeline]` sections collapse into one `[project]` table; every field is now optional with sensible defaults (the minimal valid pipeline manifest is an *empty file*); and entity fields are de-prefixed (`chart_name` → `name`, `path_to_parquet_data` → `path`, `topic_tags` → `tags`, and so on). A migration script in the chartbook repo (`python scripts/migrate_toml_v2.py <project>`) rewrites a v1 file for you.
- **Scoped pipeline identity.** Pipelines are now canonically identified as `scope/name` — for example `ftsfr/crsp_treasury` — derived from the repo's git remote and directory name. Bare names still work when unambiguous.
- **Catalog auto-discovery.** A catalog can declare its members as glob patterns (`members = ["../ftsfr_repos/*"]`) so that new clones join automatically, with no bookkeeping.

Version numbers are a *contract*. This release breaks old manifests, and the version says so: under [semantic versioning](https://semver.org/), a `0.x` project signals breaking changes by bumping the minor version, `0.0.21` → `0.1.0`. The [changelog](https://backofficedev.github.io/chartbook/changelog.html) records what each version changed and why, following the [Keep a Changelog](https://keepachangelog.com/) convention. When we discuss packaging this week, we'll walk through how this release was actually cut and published to PyPI.

## Pipelines vs. Catalogs

Both are described by a `chartbook.toml` manifest. The difference is one table:

- A **pipeline** manifest describes one project: its `[project]` metadata and its registered `[dataframes]`, `[charts]`, and `[notebooks]`.
- A **catalog** manifest has a `[pipelines]` table — a registry of member pipelines. That table's presence is what makes it a catalog.

The [FTSFR organization](https://github.com/orgs/ftsfr/repositories) is the production-scale example: roughly twenty pipeline repos (Treasury data, CDS returns, options, FX, and more), each registering its own dataframes, plus one [`catalog`](https://github.com/ftsfr/catalog) repo that pins them together:

```toml
[project]
type = "catalog"
name = "ChartBook Catalog"

[pipelines."ftsfr/crsp_treasury"]
path = "../crsp_treasury"

[pipelines."ftsfr/fed_yield_curve"]
path = "../fed_yield_curve"

# ... one entry per member pipeline
```

`chartbook build` inside the catalog directory renders one combined site: chart and dataframe listings across all members, per-pipeline pages, and a diagnostics page showing metadata completeness. Multiple pipelines, one catalog.

## Setting Up Your Own Catalog

### Step 1: Clone the pipelines you need

Make a dedicated folder and clone the FTSFR repos you want to draw from (see the table below for which ones you can actually run):

```console
mkdir -p ~/GitRepositories/ftsfr_repos
cd ~/GitRepositories/ftsfr_repos
git clone https://github.com/ftsfr/fed_yield_curve
git clone https://github.com/ftsfr/ken_french_data_library
git clone https://github.com/ftsfr/crsp_treasury
```

### Step 2: Register them in your global catalog

ChartBook keeps a ready-made personal catalog at `~/.chartbook/chartbook.toml`, with its own command group:

```console
chartbook catalog init                          # create it (first time only)
chartbook catalog add ~/GitRepositories/ftsfr_repos/*   # register pipelines (globs work)
chartbook catalog build                         # build the combined site into ~/.chartbook/docs/
chartbook catalog browse                        # open that site in your browser
```

`catalog add` derives each scoped ID from the repo's git remote (`ftsfr/crsp_treasury` from `github.com/ftsfr/crsp_treasury`) and skips duplicates. Alternatively, skip per-repo bookkeeping entirely with auto-discovery — edit `~/.chartbook/chartbook.toml` to read:

```toml
[pipelines]
members = ["~/GitRepositories/ftsfr_repos/*"]
```

and every pipeline you ever clone into that folder joins the catalog automatically. `chartbook catalog disable <id>` / `enable <id>` switch members off and on without deleting anything.

Check what's registered:

```console
chartbook ls              # tree of every pipeline, dataframe, and chart
chartbook ls dataframes   # flat list of dataframes
```

### Step 3: Run the pipeline once

Registering a pipeline tells the catalog where its outputs *will* be; it does not produce them. Inside each cloned repo, install its requirements and run it:

```console
cd ~/GitRepositories/ftsfr_repos/fed_yield_curve
pip install -r requirements.txt
doit
```

For the WRDS-based repos you will need your WRDS credentials in a `.env` file, exactly as in HW 3.

### Step 4: Load data by name

Once a pipeline has run, its dataframes are loadable from anywhere on your machine:

```python
from chartbook import data

# default: a polars LazyFrame
lf = data.load(pipeline="ftsfr/fed_yield_curve", dataframe="fed_yield_curve")

# or ask for pandas
df = data.load(pipeline="ftsfr/fed_yield_curve", dataframe="fed_yield_curve", format="pandas")

# where is the underlying parquet?
path = data.get_data_path(pipeline="ftsfr/fed_yield_curve", dataframe="fed_yield_curve")
```

Pipeline references accept three forms: a bare name (`"fed_yield_curve"`, fine when unambiguous), the canonical scoped name (`"ftsfr/fed_yield_curve"`), or the repository URL. The same lookups exist on the command line:

```console
chartbook data get-path --pipeline ftsfr/fed_yield_curve --dataframe fed_yield_curve
chartbook data get-docs --pipeline ftsfr/fed_yield_curve --dataframe fed_yield_curve
```

## Using the Catalog for Your Collaborative Report Contribution

This is the workflow for your collaborative report ([HW 5](../HW5.md)) figure: rather than writing a new data pull from scratch, build on a dataset an FTSFR pipeline already produces. Clone the repo, register it, run it, then `data.load(...)` inside your contribution's code in the collaborative-report repo. Your contribution stays reproducible (the data's provenance is a registered, documented pipeline), and the report becomes a *downstream consumer* of maintained data rather than a pile of one-off scripts.

### Which FTSFR repos can you use?

Not every FTSFR pipeline is runnable by students: some require a Bloomberg Terminal subscription, which this course doesn't provide. The repos below rely only on **free public data** or on **WRDS** (which you have access to through the course), so you can run them end to end:

| Repo | Data | Access |
|---|---|---|
| [`ftsfr/fed_yield_curve`](https://github.com/ftsfr/fed_yield_curve) | The Fed's published (GSW) Treasury yield curve | Public |
| [`ftsfr/ken_french_data_library`](https://github.com/ftsfr/ken_french_data_library) | Ken French Data Library factor and portfolio returns | Public |
| [`ftsfr/he_kelly_manela`](https://github.com/ftsfr/he_kelly_manela) | He-Kelly-Manela intermediary asset pricing factors | Public |
| [`ftsfr/nyu_call_report`](https://github.com/ftsfr/nyu_call_report) | NYU-hosted bank Call Report data | Public |
| [`ftsfr/corp_bond_returns`](https://github.com/ftsfr/corp_bond_returns) | Corporate bond returns from OpenBondAssetPricing.com | Public |
| [`ftsfr/crsp_treasury`](https://github.com/ftsfr/crsp_treasury) | CRSP daily Treasury data, plus Fed yield curve and Treasury auction stats | WRDS + public |
| [`ftsfr/wrds_crsp_compustat`](https://github.com/ftsfr/wrds_crsp_compustat) | CRSP/Compustat equity data | WRDS |
| [`ftsfr/wrds_bank_premium`](https://github.com/ftsfr/wrds_bank_premium) | WRDS bank regulatory data | WRDS |
| [`ftsfr/options`](https://github.com/ftsfr/options) | OptionMetrics equity options data | WRDS |
| [`ftsfr/cds_returns`](https://github.com/ftsfr/cds_returns) | Markit CDS spreads and returns | WRDS |
| [`ftsfr/cds_bond_basis`](https://github.com/ftsfr/cds_bond_basis) | WRDS bond returns plus Markit CDS | WRDS + public |

The remaining pipelines (`basis_tips_treas`, `basis_treas_sf`, `basis_treas_swap`, `cip`, `commodities`, `foreign_exchange`, `sovereign_bonds`) require Bloomberg Terminal data and are off the menu for this exercise, though you're welcome to read their code.

Pick the pipeline closest to your claimed collaborative-report topic. Where possible, choose one that also relates to your final-project paper, so the effort compounds.
