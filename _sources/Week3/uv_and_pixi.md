# Modern Environment Tools: uv and pixi

In [Week 1](../Week1/virtual_environments.md) we settled on **`conda` + `pip`** as
the course default: a blank `conda` environment for isolation and Python-version
control, then `pip install -r requirements.txt` for fast package installs. We also
previewed two newer tools, `uv` and `pixi`. This chapter is the promised closer look.

Both tools fit naturally into this week's theme. A recurring idea in Week 3 is *running*
project steps with a single command (the job of a task runner like `doit`). As we'll see,
`uv` and `pixi` each ship a `run` subcommand that executes commands inside the project's
environment---so they blur the line between an environment manager and a lightweight task
runner.

The point of this chapter is **not** to change what you use for homework. Keep using
`conda` + `pip` so everyone's setup matches. The goal is to make you fluent in the modern
alternatives you'll increasingly meet in the wild.

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

### Handy `uv` commands

| Command | Does |
|---------|------|
| `uv run analyze.py` | Sync the environment (if needed) and run the script |
| `uv sync` | Create/update `.venv` from `pyproject.toml` + lock |
| `uv add scipy` | Add a dependency (updates `pyproject.toml` + lock) |
| `uv lock` | Re-resolve and rewrite `uv.lock` |
| `uv pip install -r requirements.txt` | Fast, pip-compatible install into the env |
| `uv python install 3.12` | Install and manage a Python version |

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
- A dedicated task runner like `doit` (this week's main topic) adds **dependency tracking**:
  it knows which tasks depend on which files, skips work that's already up to date, and
  chains multi-step pipelines together.

In practice they compose well: you might use `doit` to define the pipeline and `uv run doit`
or a `pixi` task to make sure it always runs in the right environment. For this course,
though, keep `doit` as the task runner and `conda` + `pip` as the environment---this chapter
is about recognizing the alternatives, not replacing the stack.

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
the pragmatic hybrid this course recommends; **`uv`** and **`pixi`** are the fast, modern,
lockfile-first tools---`uv` from the PyPI world, `pixi` from the conda world. Whatever you
reach for, your `requirements.txt` and `environment.yml` remain the portable interchange
formats that tie them all together.
