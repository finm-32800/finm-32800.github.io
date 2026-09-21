# Clone and Run: What `requirements.txt` Is For

A large share of this course comes down to one promise: *if you clone my
repository and follow a few commands, you will get the results I got.* This
page walks through those commands with the [HW 0](../HW0.md) repository.

## The four steps

```bash
git clone https://github.com/finm-32800/hw0.git
cd hw0
conda create -n finm python=3.12
conda activate finm
pip install -r requirements.txt
```

1. **`git clone`** copies the repository, including its entire history, to your
   computer.
2. **`conda create`** makes a new, empty *virtual environment* named `finm` with
   its own copy of Python 3.12. Nothing you install into it can disturb the
   other Python projects on your machine.
3. **`conda activate`** switches your terminal into that environment.
4. **`pip install -r requirements.txt`** installs the packages that the project
   lists in its `requirements.txt` file.

Now get the data and run the project's dashboard:

```bash
doit
streamlit run src/app.py
```

`doit` is a task runner. It reads the file `dodo.py`, which lists the steps of
the pipeline and what each one depends on: get the data, then execute the
notebooks. We study it properly in week 2.

A browser window opens with an interactive version of this week's analysis. You
did not write any of that code, and you did not have to guess which packages it
needs. The repository told your computer what to install.

## Why the version numbers matter

Open `requirements.txt`. Each line names a package and an exact version:

```text
numpy==2.5.3
pandas==2.2.3
streamlit==1.64.0
```

The `==` is a *pin*. Compare it with a *floor*, such as `pandas>=2.0`, which
accepts any newer version. A floor is convenient. A pin is reproducible.
Software changes, and sometimes a new version of a package changes your numbers
without raising an error. One example from the packages used in finance
classes: the `yfinance` package changed the default of the `auto_adjust` option
of its `download` function from `False` to `True`. After the upgrade, the same
line of code returned prices adjusted for dividends and splits where it used to
return raw closing prices, and any returns computed from that column changed
with it.

In this week's paper, small differences matter. The optimal portfolio weights
are very sensitive to their inputs. If your collaborator's environment differs
from yours, you may both be "right" and still disagree.

```{admonition} Discussion
:class: tip
Look closely at the pinned versions. The `wrds` package requires an older
version of `pandas` than the newest one available. `pip` worked that out for
you when it resolved the dependencies. What would happen to this project in two
years if the versions were not pinned?
```

## Different projects, different environments

Try a second repository:

```bash
git clone https://github.com/jmbejara/streamlit_finance_chart.git
```

It has its own `requirements.txt`, with different packages and versions.
Installing both projects into the same environment will eventually produce a
conflict. The habit to build is one environment per project. We cover this
properly, including `conda` environment files and the newer tools `uv` and
`pixi`, in [Virtual Environments](../Week1/virtual_environments.md) in week 1.
