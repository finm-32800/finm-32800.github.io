# Final Project Instructions and Rubric


In this project, you'll replicate tables, figures, or data series from a well known finance paper using the principles of reproducible analytical pipelines (RAPs) learned in this class. Your replication must be automated from end-to-end, formatted using the [cookiecutter chartbook template](https://github.com/backofficedev/cookiecutter_chartbook). To create a new project, use [cruft](https://cruft.github.io/cruft/): `cruft create https://github.com/backofficedev/cookiecutter_chartbook`. See [Project Structure: "Chartbook" Template](../Week3/project_structure.md) for more details.

The final project is more than a replication—it is a **product**. You replicate the core exhibits of your assigned paper, bring them up to the present, and then build something on top of them that other people would actually want to use. A central part of the project is making the case for that product: who would clone your repository and run your code, and why.

## How the Project Runs

1. **Consultation (weeks 4 through 6).** A 1-on-1 meeting with the instructor, booked at [https://finm-32800.youcanbook.me/](https://finm-32800.youcanbook.me/). The consultation is ungraded. It is a low-stakes chance to get feedback on your plan, and you leave it with an agreed extension (see *The Extension Agreement* below).
2. **Submission (24 hours before your final project presentation).** Before we meet, I will clone your repository, run it on my own machine, and read your report and your project site. **I grade the commit on your `main` branch as it stood 24 hours before your meeting.** Anything pushed after that is not graded. Your project must run from a clean clone by following your README alone. Reproducing your results without your help is the whole point of the course.
3. **Final project presentation (week 10, no later than Friday, December 11).** A 30-minute meeting with your whole group, booked at the same link. You do not present slides or give a demo: I will already have run your code. The whole meeting is spent questioning each member individually, with questions written from your repository and report (see *The Final Project Presentation* below).

## Receiving Your Assigned Project

  - You will be assigned a project from the list found [here.](./potential_final_projects.md)
  - Your preference will be taken into account when assigning the projects. You can rank your preferred projects using a Google Form: **TBD** (the link will be posted on Canvas). Please, only submit one response per group. **Your group must have exactly 4 people in it.** Students can not choose to work alone, and a group of any other size requires my permission in advance. I will use your responses to assign each group a project.

## The Extension Agreement

Come to the consultation with a one-page pitch that answers five questions:

1. What does the paper claim? Say it in one sentence.
2. What does the paper *not* do that someone would want?
3. What will you build?
4. Who would clone it, and what would they do with it?
5. How will we know it worked?

Also say what your data validation tests will check (see the rubric below).

We agree on the extension in the meeting. Afterward, add an **Extension** section to your README that records what we agreed. That README section is what your extension is graded against. If your plan changes substantially later, check with me first.

For an example of an extension pitched this way, see [Project Previews](project_previews.md).

## Update Versus Extension

These are graded separately.

**The update** recomputes the paper's exhibits with data through the most recent date available, and says in a sentence or two whether the paper's result held up after publication. Once the pipeline works, the update should be close to mechanical. That is a sign the pipeline is built well, but it is not the extension.

**The extension** adds something the paper does not have. Examples of what can count:

- An out-of-sample or regime test that the paper could not run (did the effect survive decimalization, zero commissions, or the 2022 rate hikes?)
- A cleaned, documented dataset that others in the field would use, with the code that keeps it current
- A signal or monitor that updates on fresh data, such as a daily series or a dashboard
- A head-to-head comparison against a competing measure on the same sample
- The paper's method applied to a new market, asset class, or frequency
- A conceptual gap in the paper's reasoning, with a measurement that addresses it

On their own, these do not count: extending the sample (that is the update), restyled versions of the paper's exhibits, or summary statistics of the data you already replicated.

## The Final Project Presentation

Each member of the group is questioned individually, for about seven minutes each. Questions may or may not come from parts of the project you wrote. If your name is on the project, you are expected to understand every part of it, not only the parts you wrote. Expect questions of these kinds:

- **The paper**: what it claims, why it matters, and the economics behind the main result.
- **The data**: where each source comes from, what each filter does and why it is there, and what would break without it.
- **Design choices**: why the pipeline is organized the way it is, how you chose your test tolerances, and what you would change.
- **The extension**: why someone would use it, where it stops being reliable, and what you would build next.
- **Working with the code**: for example, "If I changed `END_DATE`, what reruns, and how does `doit` know?"

Treat it like a job interview where this project is on your resume. An interviewer won't ask you to recite the paper. They will ask what you built, why you built it that way, and whether you can tell them something useful they didn't already know. Answer like that. [Project Previews](project_previews.md) has example questions for three past projects.

## Final Project Grading Rubric

There are **200 points** in total. Each item is prorated by its degree of completion. For example, if you replicate 50% of the numbers from a table, you'll get 50% of the points for that table. Some adjustments may be made by discretion based on the difficulty of the task.

### Group items (115 points)

- **20/20 Replication.** Replicate the series, tables, and/or figures listed for your assigned project. Choose a reasonable tolerance and construct unit tests to ensure that your numbers match the paper's within this tolerance. Fix the tolerance before you see your results, not after.
- **10/10 Update.** Recompute the same series, tables, and/or figures with data up until the present (or at least the most recently available data), and state whether the paper's result held up.
- **10/10 Data validation.** Write unit tests that validate the data itself, not just the code: tests that check your data against something you know independently. For example, your series reconciles with a second source (CRSP market cap against the S&P 500 index level, your TAQ volume against CRSP daily volume, or your measure against a series the authors post); an identity the data must satisfy holds (put-call parity, or portfolio weights that sum to one); each filter removes about as many observations as you expect; and coverage through time has no unexplained breaks. These tests run as part of the pipeline. Then build at least one table and one figure of your own that show what those checks found, such as the reconciliation against the second source, the filter-by-filter observation counts, or coverage through time. Each caption says what the check found and what it means for trusting your results.
- **30/30 Extension.** Did you deliver the extension agreed at the consultation and recorded in your README? Is it useful for a reason you can state? Is it tested and documented?
- **9/9 Report and published site.**
  - **(5)** A single LaTeX document that briefly describes the project and contains all the tables and charts your code produces. Give a high-level overview of how the project went: where you found success, where the challenges were, and which data sources you used. It should not contain code snippets. Every table and figure needs a caption that tells me what I should learn or take away from it.
  - **(4)** A ChartBook site, built by the pipeline and published on GitHub Pages, with the project's dataframes and charts registered in `chartbook.toml` and each dataframe's sources recorded, so a reader can follow the data from the raw pulls to the final tables (see the published site on [Project Previews](project_previews.md)). The site includes at least one Jupyter notebook that gives a brief tour of the cleaned data and of the analysis performed in the code. Code snippets are welcome in the notebook. It should look something like one of the HW guides we used, e.g. [HW 1 Guide D: Constructing the Fama-French Factors](../notebooks/_05_Fama_French_1993_ipynb.ipynb).
- **36/36 Engineering standards.**
  - **(8)** The project runs end to end with PyDoit from a clean clone on my machine, following only your README.
  - **(4)** Data cleaning lives in its own file or files, whose only purpose is to put the data into a tidy format. The analysis is kept in separate files.
  - **(4)** Every statistic in the LaTeX document is generated automatically by the code.
  - **(4)** Unit tests are well motivated, each has a purpose, and none are unnecessary or repetitive. A GitHub Actions workflow runs the tests on every push and pull request. Tests that need WRDS or other credentials may be skipped in CI, but the rest must run there, on small samples if needed, and pass.
  - **(4)** The repository and its entire Git history are free of copyrighted material (e.g., raw data), secrets such as API keys, and any trace of a `.env` file.
  - **(4)** Configuration uses a `.env` file plus reasonable defaults in `settings.py` (e.g., the data directory, the API keys, and the `START_DATE` and `END_DATE` of the analysis), and the required variables are described in a `.env.example` file.
  - **(4)** The project was scaffolded from the [cookiecutter chartbook template](https://github.com/backofficedev/cookiecutter_chartbook) with `cruft create https://github.com/backofficedev/cookiecutter_chartbook`, the README describes the project and gives clear instructions for running it end to end (environment setup and `doit`), and a `requirements.txt` file lists the required packages.
  - **(4)** Each Python file has a docstring at the top that describes what the file does, and each function has a reasonably descriptive name and, when appropriate, a docstring. (No need to go overboard, but the code should be reasonably clean.)

### Individual items (85 points)

- **35/35 Individual contribution.** Did I contribute a substantial part of the code? I look at the commit history and at pull requests: who authored them, who reviewed them, and who merged them. Every pull request must be reviewed by another member of the group before it is merged, and every member must have authored at least one pull request that was merged and reviewed at least one written by a teammate.
- **50/50 Final project presentation.** Graded individually from your answers in the 30-minute meeting, as described in *The Final Project Presentation* above.
