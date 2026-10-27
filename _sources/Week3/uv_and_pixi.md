# Modern Environment Tools: uv and pixi

In [Week 1](../Week1/virtual_environments.md) we settled on **`conda` + `pip`** as
the course default: a blank `conda` environment for isolation and Python-version
control, then `pip install -r requirements.txt` for fast package installs. We also
previewed two newer tools, `uv` and `pixi`. This chapter is the promised closer look, and
since HW 3 it is more than a look: the homework now runs with `uv`.

Both tools ship a `run` subcommand that executes a command inside the project's environment,
creating the environment first if it has to. So they blur the line between an environment
manager and a lightweight task runner like `doit`.

## Why the homework uses uv

From HW 3 on, the homework and the case studies ship a `pyproject.toml` and a `uv.lock`, and
every pipeline starts with one command:

```bash
uv run doit
```

Four reasons, each of which you have already run into.

- **One command on a machine that has nothing.** The first `uv run` downloads Python 3.12 if
  it is missing, creates `.venv/`, installs the locked packages, and then runs the command.
  That is exactly what a GitHub Actions runner needs. It starts empty every time, so the
  [live monitor](../Week5/live_monitor.md)'s workflow builds its whole environment in two
  steps:

  ```yaml
  - name: Set up uv
    uses: astral-sh/setup-uv@v7
    with:
      enable-cache: true
  - name: Update the monitor
    run: uv run doit latest
  ```

- **The same versions everywhere.** `uv.lock` pins every package the project uses, the ones
  you named and everything they depend on, to an exact version and a checksum. Your laptop,
  the course's grading runner, and GitHub's runner all get the same environment. A
  hand-written `requirements.txt` pins only what someone wrote down, and the rest floats: the
  same file can install a different environment next month.
- **Fast enough to rebuild on every run.** `uv` resolves and installs in seconds, and
  `setup-uv` caches the downloads between runs. When the environment is rebuilt from scratch
  every morning, that speed is what makes rebuilding it practical.
- **Standard files.** `pyproject.toml` is the standard Python project manifest, the same file
  the packaging tools read. Nothing in it is specific to `uv`, and `uv export` still writes a
  `requirements.txt` for anyone who prefers the `finm` conda environment. HW 3 ships one.

## Same code, four toolchains

The [in-class examples repository](https://github.com/finm-32800/inclass_examples) has a
[`software_environments/`](https://github.com/finm-32800/inclass_examples/tree/main/software_environments)
directory that makes the comparison concrete. The *exact same* script (`analyze.py`, a tiny
moving-average crossover demo depending on `numpy`, `pandas`, and `matplotlib`) ships in four
subdirectories. Only the tooling around installing and running it changes:

| Directory | Tool | Manifest | One-liner to run |
|-----------|------|----------|------------------|
| [`01_conda_only`](https://github.com/finm-32800/inclass_examples/tree/main/software_environments/01_conda_only) | conda alone | `environment.yml` | `conda activate ... && python analyze.py` |
| [`02_conda_and_pip`](https://github.com/finm-32800/inclass_examples/tree/main/software_environments/02_conda_and_pip) | conda + pip | `environment.yml` + `requirements.txt` | `conda activate ... && python analyze.py` |
| [`03_uv`](https://github.com/finm-32800/inclass_examples/tree/main/software_environments/03_uv) | `uv` | `pyproject.toml` | `uv run analyze.py` |
| [`04_pixi`](https://github.com/finm-32800/inclass_examples/tree/main/software_environments/04_pixi) | `pixi` | `pixi.toml` | `pixi run start` |

Clone that repo and follow along---the manifests below are taken directly from it.

## <img src="assets/logos/uv_logo.svg" alt="uv logo" class="inline-logo"/> uv

[`uv`](https://docs.astral.sh/uv/) is a single, very fast tool, written in Rust, that
replaces `pip`, `venv`, `pip-tools`, and `pyenv` all at once. One manifest
(`pyproject.toml`), one lockfile (`uv.lock`), one command to run.

### Installing uv

`uv` is a standalone binary---it doesn't even need an existing Python:

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# or, if you already have Python
pip install uv
```

### The core idea: `uv run` bootstraps everything

The headline feature is that a single command sets up the *entire* environment on demand.
In the `03_uv` example, you run:

```bash
uv run analyze.py
```

On the first run, `uv` will: download Python 3.12 if it isn't already available, create a
`.venv/`, resolve and install the dependencies, write a `uv.lock` file, and *then* run your
script. Subsequent runs are essentially instant because the environment already exists.

If you prefer an explicit two-step flow, it's:

```bash
uv sync            # create/update the environment from pyproject.toml + lock
uv run analyze.py  # run inside it
```

### Quick start: which command changes which file

Three things matter. `pyproject.toml` lists what the project asks for (`pandas>=2.2`); you
edit it, or `uv add` does. `uv.lock` records what that resolved to, every package at an
exact version; only `uv` writes it. `.venv/` holds the installed packages. Commit the first
two, and keep `.venv/` out of git.

| Command | `pyproject.toml` | `uv.lock` | `.venv/` |
|---------|------------------|-----------|----------|
| `uv add scipy` | adds `scipy` | re-locked | `scipy` installed |
| `uv lock` | read | created, or updated if it no longer matches `pyproject.toml`; versions already locked stay put | untouched |
| `uv lock --upgrade-package pandas` | read | `pandas` moved to the newest version allowed | untouched |
| `uv sync` | read | re-locked if out of date | made to match the lock exactly; extra packages removed |
| `uv run doit` | read | re-locked if out of date | missing packages added, extras left alone; then `doit` runs |
| `uv run --locked doit` | read | error if out of date | as above |

So `uv lock` is the command that writes the lock, and it is conservative: it changes a
version only when `pyproject.toml` no longer allows it, or when you ask with `--upgrade` or
`--upgrade-package`. In a freshly cloned HW repo, `uv run doit` reads the committed
`uv.lock`, checks that it still matches `pyproject.toml`, downloads Python if needed, builds
`.venv/` with exactly the locked versions, and runs `doit`. It does not choose versions; the
lock already did.

You run `uv lock` once, on whatever machine you have. The lock it writes covers macOS, Linux,
and Windows, and every Python version that `requires-python` allows. That is why a lock made
on a Mac works on GitHub's Ubuntu runner.

### Disk space: a `.venv/` per project, one shared cache

Every project gets its own `.venv/`, and `du -sh .venv` can report several hundred megabytes
for each one. Most of that is not new disk use. `uv` keeps one copy of each package in its
cache (`~/.cache/uv`) and links it into every `.venv/` that needs it: as clones on macOS,
which share disk blocks until a file is modified, and as hard links on Linux and Windows.
`du` counts a clone as a full copy, so it overstates the size. Rebuilding HW 3's environment
in a new folder on a Mac, `du` reported the new `.venv/` at 713 MB while the disk's free space
dropped by 12 MB. Linking instead of copying is also why a fresh environment installs in
seconds. (This needs the cache and the project on the same disk; otherwise `uv` falls back
to full copies and says so.)

The space that does add up is the cache itself. It keeps every version `uv` has ever
downloaded, including versions no project uses anymore, and can grow to many gigabytes.

```bash
uv cache prune   # remove cache entries that nothing refers to
uv cache clean   # empty the cache; later installs download again
```

Deleting a project's `.venv/` is safe too: the next `uv run` rebuilds it from `uv.lock`.

### The manifest: a standard `pyproject.toml`

`uv` doesn't invent its own config format---dependencies live in the standard `[project]`
table of a `pyproject.toml`:

```toml
[project]
name = "finm-env-uv"
version = "0.1.0"
description = "Moving-average crossover demo, managed by uv"
requires-python = ">=3.12"
dependencies = [
    "numpy>=2.0",
    "pandas>=2.2",
    "matplotlib>=3.9",
]
```

The companion `uv.lock` file (generated automatically) pins the *exact* resolved versions
of every package---direct and transitive---so a collaborator who runs `uv sync` gets a
byte-for-byte identical environment. This is the reproducibility win over a hand-maintained
`requirements.txt`.

### It still speaks `requirements.txt`

You don't have to adopt `pyproject.toml` to benefit from `uv`'s speed. `uv` has a
`pip`-compatible interface that reads the very same `requirements.txt` you already use:

```bash
uv venv                              # create a .venv
uv pip install -r requirements.txt   # like pip install, but much faster
```

This is the gentlest on-ramp: drop `uv` in front of your existing pip workflow and keep
everything else the same.

### Scripts that carry their own dependencies (PEP 723)

Not every piece of code needs a project. [PEP 723](https://peps.python.org/pep-0723/) lets a
single `.py` file list its own dependencies in a comment block at the top, and `uv run`
reads that block. Here is a complete script, `ten_year.py`, that pulls the 10-year Treasury
yield from FRED:

```python
# /// script
# requires-python = ">=3.12"
# dependencies = [
#     "pandas",
# ]
# ///
import pandas as pd

url = "https://fred.stlouisfed.org/graph/fredgraph.csv?id=DGS10"
rates = pd.read_csv(url, parse_dates=["observation_date"], na_values=".")
print(rates.dropna().tail())
```

```bash
uv run ten_year.py
```

`uv` builds an environment with `pandas` just for this script, caches it so later runs start
at once, and ignores the dependencies of any project the file happens to sit in. Anyone with
`uv` can run the file you send them, with nothing to set up first. You don't have to type the
header yourself:

```bash
uv init --script ten_year.py --python 3.12   # new file with an empty header
uv add --script ten_year.py pandas           # adds pandas to the header
uv lock --script ten_year.py                 # optional: pins it in ten_year.py.lock
```

The uv guide [Running scripts](https://docs.astral.sh/uv/guides/scripts/#declaring-script-dependencies)
covers the rest.

### Handy `uv` commands

| Command | Does |
|---------|------|
| `uv run analyze.py` | Sync the environment (if needed) and run the script |
| `uv sync` | Create/update `.venv` from `pyproject.toml` + lock |
| `uv add scipy` | Add a dependency (updates `pyproject.toml` + lock) |
| `uv lock` | Re-resolve and rewrite `uv.lock` |
| `uv pip install -r requirements.txt` | Fast, pip-compatible install into the env |
| `uv python install 3.12` | Install and manage a Python version |

### What uv does that conda + pip don't

Most of the difference comes from the lockfile and from `uv run` checking it every time. The
last row is where conda + pip comes out ahead.

| | conda + pip | uv |
|---|---|---|
| Lockfile | By hand: `pip freeze` or `conda env export` | `uv.lock`, kept in step with `pyproject.toml` automatically |
| Platforms the lock covers | The machine you exported from | macOS, Linux, Windows, and every Python in `requires-python`, from one `uv lock` |
| Lock out of date | Nothing checks | `uv run` re-locks, or `uv run --locked` stops with an error |
| Packages you no longer list | Stay installed: `pip install -r` only adds | `uv sync` removes them |
| Before running code | Create, activate, install | Nothing: `uv run` does it |
| Dependencies for a single script | Not supported | PEP 723 header, then `uv run script.py` |
| Run a tool without installing it | A separate tool (`pipx`) | `uvx ruff check` |
| Copies of each package | pip installs a separate copy into every environment | One shared cache, linked into each `.venv/` |
| Installs Python | conda does, pip doesn't | Yes |
| Build and publish packages | Separate tools (`build`, `twine`) | `uv build`, `uv publish` |
| Non-Python binaries (compilers, C libraries) | conda does | No: PyPI only |

### Trade-offs

- **Pro:** Extremely fast; manages Python versions too; standards-based `pyproject.toml`;
  deterministic lockfile.
- **Con:** PyPI-only. It does not install from conda channels, so the non-Python binaries
  that `conda` packages (compilers, system libraries, some scientific stacks) may not be
  available. That's exactly the gap `pixi` fills.

## <img src="assets/logos/pixi_logo.png" alt="pixi logo" class="inline-logo"/> pixi

[`pixi`](https://pixi.sh/) gives you a `uv`-style workflow---one manifest, one lockfile,
an automatic per-project environment---but it resolves packages from the **conda**
ecosystem (`conda-forge`) instead of PyPI. So you get conda's non-Python binaries with
modern speed and ergonomics. (It can also pull from PyPI when you need to.)

### Installing pixi

```bash
# macOS / Linux
curl -fsSL https://pixi.sh/install.sh | bash
```

Note that, like `uv`, `pixi` is self-contained: you do **not** need a separate `conda`
install for it to resolve conda packages.

### The manifest: `pixi.toml`, with built-in tasks

Here is the `04_pixi` manifest. Notice the `[tasks]` table at the bottom---this is where
the task-runner flavor comes in:

```toml
[project]
name = "finm-env-pixi"
version = "0.1.0"
description = "Moving-average crossover demo, managed by pixi"
channels = ["conda-forge"]
platforms = ["osx-arm64", "osx-64", "linux-64", "win-64"]

[dependencies]
python = "3.12.*"
numpy = ">=2.0"
pandas = ">=2.2"
matplotlib = ">=3.9"

[tasks]
start = "python analyze.py"
```

To run it:

```bash
pixi run start          # runs the `start` task defined above
pixi run python analyze.py   # or run anything inside the environment
pixi shell              # activate an interactive shell in the environment
```

On the first `pixi run`, pixi resolves the environment, installs it under `.pixi/`, and
writes a `pixi.lock` for exact, cross-platform reproducibility (note the explicit
`platforms` list).

### Handy `pixi` commands

| Command | Does |
|---------|------|
| `pixi run start` | Run the `start` task (`python analyze.py`) |
| `pixi run <cmd>` | Run any command inside the environment |
| `pixi shell` | Activate an interactive shell |
| `pixi add scipy` | Add a conda dependency (updates manifest + lock) |
| `pixi add --pypi some-pkg` | Add a PyPI dependency |

### When would you pick `pixi` over `uv`?

Reach for `pixi` when you need packages that live in the conda world---non-Python binaries,
or scientific packages that are easier to get from `conda-forge` than from PyPI. It's the
modern, lockfile-first successor to the plain `conda` workflow. If your project is
pure-Python and all your dependencies are on PyPI, `uv` is usually the simpler, faster pick.

## A note on the task-runner connection

You'll have noticed that both `uv run analyze.py` and `pixi run start` *run something inside
the project's environment*---which is conceptually close to what a task runner like `doit`
does. The difference in scope is worth keeping straight:

- `uv run` / `pixi run` guarantee a command executes in the correct, reproducible
  environment, and `pixi`'s `[tasks]` can give those commands friendly names.
- A dedicated task runner like `doit` adds **dependency tracking**:
  it knows which tasks depend on which files, skips work that's already up to date, and
  chains multi-step pipelines together.

In practice they compose well, and this course composes them: `doit` defines the pipeline,
and `uv run doit` makes sure it runs in the locked environment, on your laptop and on a
runner alike.

## Takeaway

| | conda only | conda + pip | uv | pixi |
|---|---|---|---|---|
| Package source | conda channels | conda (Python) + PyPI | PyPI | conda channels (+ PyPI) |
| Manages Python version | ✅ | ✅ | ✅ | ✅ |
| Non-Python binaries | ✅ | ✅ (via conda) | ❌ | ✅ |
| Lockfile | manual | manual | `uv.lock` (auto) | `pixi.lock` (auto) |
| Speed | slower | faster installs | very fast | very fast |
| Run command | `python analyze.py` | `python analyze.py` | `uv run analyze.py` | `pixi run start` |

Same code, four toolchains. `conda` is the long-standing data-science default and the only
one here that natively handles non-Python binaries on its own channels; **conda + pip** is
the pragmatic hybrid this course started with; **`uv`** and **`pixi`** are the fast, modern,
lockfile-first tools---`uv` from the PyPI world, `pixi` from the conda world. The homework
uses `uv` from HW 3 on. Whatever you reach for, your `requirements.txt` and `environment.yml` remain the portable interchange
formats that tie them all together.
