# Midterm Report

This course has **no exams**. In place of a midterm exam, the entire class collaboratively produces one shared, publication-style **Midterm Report** on the current economy and financial markets. Each student contributes one exhibit and one short write-up, and we assemble these into a single polished PDF.

This is also a practical exercise in the social coding workflow we cover in class: claiming work through an issue tracker, contributing through pull requests, reviewing other people's work, and following a shared style guide so that many authors produce one coherent document.

## What You Contribute

Each student contributes, individually:

- **One figure** that says something concrete about the economy or financial markets over the last 6 to 12 months, generated reproducibly by the repo's pipeline.
- **One paragraph** that explains the figure: what the data shows, why it matters, and what the reader should take away.
- **A citation** that establishes the topic's importance, such as a news article or research piece that discusses it.

Your contribution must follow the repo's style guides so it integrates cleanly with everyone else's work: `CODE_STYLE.md` for code organization, plus the report and chart style guides in `reports/`.

## Workflow

The report lives in a private GitHub repository, [finm-32800/finm_midterm_report](https://github.com/finm-32800/finm_midterm_report). Every student receives a collaborator invite by email.

1. **Claim an issue.** Each open issue is a coverage area or a dataset, deliberately broad. The specific chart and angle are yours to choose. Assign the issue to yourself, one student per issue. You may also propose your own topic by opening a new issue and coordinating with me there.
2. **Build it reproducibly.** Work on a branch. Your exhibit gets its own file in `src/`, is wired into `dodo.py`, and pulls data through FRED, the OFR API, or the chartbook catalog (the ftsfr pipelines). Details are in `CODE_STYLE.md`.
3. **Open a pull request and request review from two classmates.** Your reviewers must verify that your code actually runs and builds your exhibit. Address their comments.
4. **Instructor review.** After both classmate reviews are complete, I review and merge.
5. **Review two others.** You must also serve as a reviewer on two classmates' pull requests.

All of this must be complete by the deadline: **Monday, August 10 at 11:59 pm CT**.

## Grading

The Midterm Report is 10% of the course grade. Your contribution is graded out of 10 points.

| Criterion | Points |
|---|---|
| **Topic and importance.** The exhibit addresses your claimed issue, speaks to the last 6 to 12 months, and cites a source that shows why it matters. | 2 |
| **Message.** The figure and paragraph carry one clear, coherent point. | 2 |
| **Style.** The chart matches the chart style guide and the code follows `CODE_STYLE.md`. Cross-references to sections and exhibits use `\label`/`\ref`, never hand-typed numbers. | 2 |
| **It works.** The pipeline builds your exhibit end to end, and your reviewers verified this before I saw it. | 2 |
| **Peer review.** Two classmates reviewed your PR, and you completed reviews for two others. | 2 |

Credit requires each step to happen before the deadline. A contribution that is still unmerged at the deadline, or that skipped peer review, cannot earn full credit no matter its quality.
