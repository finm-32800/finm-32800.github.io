# GitHub Pages and Tearsheets

## What is GitHub Pages?

[GitHub Pages](https://pages.github.com/) is a free static site hosting service provided by GitHub. It serves HTML, CSS, and JavaScript files directly from a repository, making it an easy way to publish documentation, portfolios, or project pages to the web.

Key features:
- **Free hosting** for static content
- **Custom domains** supported (or use `<username>.github.io/<repo-name>`)
- **No server-side code** - just static files (HTML, CSS, JS, images)

One important limitation: on GitHub's **free tier, Pages is only available for public repositories**. Publishing Pages from a *private* repo requires a paid plan (GitHub Pro for personal accounts, or GitHub Team/Enterprise for organizations). And even then, the published site itself is always publicly accessible — Pages does not password-protect anything.

Because your assignment repos in this course are private and must stay that way (they contain your graded solutions), the workflow we use is: **keep the code in the private assignment repo, and publish the built site from a separate public repo** under your personal account. The steps below show how.

In this course, we use GitHub Pages to publish analysis results and tearsheets, giving you practice with a common workflow for sharing research outputs.


## What is a Tearsheet?

A **tearsheet** is a one-page (or multi-page) performance summary of an investment strategy. The name comes from the practice of literally tearing a single page from a larger report to share key information quickly.

In finance, tearsheets are commonly used to communicate:
- **Performance metrics**: Sharpe ratio, CAGR, volatility, maximum drawdown
- **Risk characteristics**: Value at Risk (VaR), beta, correlation to benchmarks
- **Return distributions**: Monthly/yearly returns, drawdown periods
- **Visualizations**: Cumulative returns, rolling statistics, heatmaps

Tearsheets provide a standardized way for portfolio managers and analysts to present strategy performance to investors, colleagues, or stakeholders.


## Creating a Tearsheet with `quantstats_lumi`

The [`quantstats_lumi`](https://github.com/Lumiwealth/quantstats_lumi) package is a Python library for portfolio analytics. It can generate comprehensive tearsheets with a single function call.

### Installation

```bash
pip install quantstats_lumi
```

### Basic Usage

```python
import quantstats_lumi as qs

qs.extend_pandas()

# Assuming `returns` is a pandas Series of daily returns with a DatetimeIndex
qs.reports.full(returns, benchmark="SPY")
```

The `qs.reports.full()` function generates a comprehensive set of performance metrics and visualizations directly in your Jupyter notebook, including:
- Summary statistics (Sharpe, Sortino, max drawdown, etc.)
- Cumulative returns plot
- Drawdown analysis
- Monthly returns heatmap
- Rolling volatility and Sharpe ratio
- And more


## Publishing to GitHub Pages

Follow these steps to publish your tearsheet (or any Jupyter notebook) to GitHub Pages.

### Step 1: Create a Jupyter Notebook

Create a notebook that generates your tearsheet. For example:

```python
import quantstats_lumi as qs

qs.extend_pandas()

# Load your portfolio returns (daily returns as a pandas Series)
# Example: returns = pd.read_csv("returns.csv", index_col=0, parse_dates=True).squeeze()

qs.reports.full(returns, benchmark="SPY")
```

### Step 2: Run the Notebook

Execute all cells in your notebook. Ensure all visualizations render correctly before proceeding.

### Step 3: Export to HTML

Export the notebook as an HTML file:

- **In VS Code**: Click the "..." menu in the top-right corner of the notebook, then select **Export** > **HTML**
- **In JupyterLab**: Go to **File** > **Save and Export Notebook As** > **HTML**

Name the exported file `index.html`.

### Step 4: Create a Separate Public Repo and Set Up the `./docs/` Folder

Create a **new public repository under your personal GitHub account** to host the site (for example, `my-tearsheet`). This repo holds only the built static site — never your source code, tests, or `.env` file. Your private assignment repo stays private.

In the new public repository:

1. Create a `docs/` folder in the repo root
2. Place your `index.html` file inside `docs/`
3. Create an empty file called `.nojekyll` in `docs/`

The `.nojekyll` file tells GitHub not to process your files with Jekyll (GitHub's default static site generator). This is important because Jekyll can interfere with certain file names and paths.

Your folder structure should look like:

```
your-repo/
├── docs/
│   ├── index.html
│   └── .nojekyll
├── src/
│   └── ...
└── ...
```

### Step 5: Commit and Push

```bash
git add docs/
git commit -m "Add tearsheet to GitHub Pages"
git push
```

You can also do this using GitKraken, VS Code's Source Control panel, or GitHub Desktop.

### Step 6: Enable GitHub Pages

1. Go to your repository on GitHub
2. Navigate to **Settings** > **Pages**
3. Under "Source", select **Deploy from a branch**
4. Set Branch to `main` and Folder to `/docs`
5. Click **Save**

After a few minutes, your site will be live at:

```
https://<org-or-username>.github.io/<repo-name>/
```

**Note**: The repo hosting the Pages site must be public (on the free tier), which is why we publish from a separate site-only repo. Your assignment repo — with your code, tests, and git history — remains private.


## Example

See a completed tearsheet example here: https://finm-32800.github.io/hw3--solutions/
