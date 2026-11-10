# GitHub Issues and Pull Requests: Enhancing Collaborative Development

GitHub, a pivotal platform in the software development domain, leverages *issues* for tracking tasks and bugs, and *pull requests* as a fundamental mechanism for fostering collaboration, code quality, and project management. While issues help teams organize and discuss requirements and problems, pull requests provide a formalized way to propose and review code changes. Through these integrated features, team members can coordinate work, review modifications, and merge changes into the main branch while maintaining clear connections between problem reports and their solutions. This chapter briefly introduces both features. 

- **GitHub Issue Tracker**: A collaborative tool for reporting bugs, requesting features, and tracking project tasks. Issues serve as discussion forums for technical problems and act as a central hub for work item management.
- **Pull Request (PR)**: A formal proposal to merge code changes from a development branch into a project's main branch. PRs provide a structured interface for code review, continuous integration testing, and maintaining documentation of changes.

## GitHub Issues

GitHub's issue tracker provides a powerful complement to pull requests in managing software development workflows. The issue tracker is more than just a "to-do" list. It integrates with the pull request feature. The two features integrate seamlessly through:

- **Issue-Reference Links**: Pull requests can reference issues using keywords like `fixes #123` or `closes [issue number]` in descriptions. When merged, this automatically closes the linked issue and creates a visible connection between them.

- **Contextual Tracking**: Each pull request shows linked issues in the sidebar, while issues display associated PRs, maintaining full visibility of related work.

- **Project Management**: Teams often use GitHub Projects to create boards that track both open issues and their corresponding pull requests, providing a unified view of work in progress.

- **Discussion Threads**: Both issues and pull requests maintain comment threads, allowing teams to continue design discussions from initial problem reporting (issue) through implementation (PR).

## Use Cases of GitHub Pull Requests

 - **Code Review and Quality Assurance**

Pull requests are instrumental in the code review process. When a developer proposes changes by creating a pull request, it initiates a structured platform for reviewing code. Peers can comment on specific lines of code, suggest improvements, and identify potential bugs before the code is merged. This process not only improves code quality but also fosters learning and knowledge sharing among team members.

 - **Collaborative Development**

GitHub pull requests support collaborative development by allowing multiple developers to work on different features simultaneously. By using separate branches for each feature and creating pull requests for these branches, teams can effectively manage concurrent development efforts. This approach ensures that the main branch remains stable and that new features are thoroughly reviewed and tested before integration.

 - **Project Management**

Pull requests serve as checkpoints in the project management process. They can be linked to specific issues or tasks, providing a clear trail of what changes were made and why. This linkage facilitates project tracking and helps in organizing the development workflow around the project's objectives.



## Workflow Integration

Integrating pull requests into a development team's workflow involves several key practices:

- **Branching Strategy**: Adopting a branching strategy, such as Git Flow or GitHub Flow, provides a structured approach for managing branches and pull requests. This strategy dictates how features, releases, and hotfixes are handled, ensuring a clean and organized repository.

- **Continuous Integration (CI)**: Pull requests can be integrated with CI tools to automatically build and test proposed changes. This integration provides immediate feedback on the impact of changes, helping to maintain code quality and stability.

- **Documentation**: Including detailed descriptions and documentation in pull requests helps reviewers understand the context and rationale behind the changes. This practice is crucial for effective collaboration and decision-making.

## Examples of Usefulness

- **Feature Development**: A developer working on a new feature creates a feature branch and later submits a pull request. The pull request triggers a review process, allowing the team to provide feedback and request changes if necessary, ensuring that the new feature aligns with the project standards and goals.
  
- **Bug Tracking**: When addressing a reported bug from the issue tracker, developers include `fixes #456` in their PR description. This automatically links the solution to the original problem report and closes the issue when merged.

- **Code Refactoring**: Pull requests are also valuable for code refactoring efforts. A developer can propose refactoring changes that improve code maintainability and performance. The team can review these changes in isolation, ensuring that refactoring does not introduce regressions.

## Example: Reviewing a Classmate's Pull Request from the Command Line

In [HW 4](../HW4.md) you review two classmates' pull requests, and a review
starts by building the change yourself. Reading the diff on GitHub tells you
what changed; only running it tells you whether the report still builds.
Suppose a classmate asks you to review pull request #142 in the report
repository.

````{admonition} Install the GitHub CLI and connect it to your account
:class: tip

If you have not set up the [GitHub CLI](https://cli.github.com/) (`gh`) yet,
please do. It is very helpful: pull requests, issues, reviews, and private
repositories all work from the terminal, and it handles your GitHub login
for `git` too. Install it (`brew install gh` on a Mac,
`winget install --id GitHub.cli` on Windows; other systems are on the
[install page](https://github.com/cli/cli#installation)), then connect it to
your account once:

```bash
gh auth login      # choose GitHub.com, HTTPS, and log in with a browser
gh auth status     # confirms which account you are logged in as
```

If you would rather use plain `git`, everything below has a `git` version,
shown after the `gh` commands, and the review itself can be written on the
pull request's page on GitHub.
````

With `gh`, start from a fresh clone. This part is optional, but it is the
safest way to test HW 4's "builds from a clean clone": your own uncommitted
work and leftover build outputs cannot leak into the build, and it is cheap,
because the data comes from your ChartBook store, not the repository. If you
would rather use the clone you already have, commit or stash your own work
first so that `git status` is clean, and skip the first two lines:

```bash
gh repo clone finm-32800/finm_midterm_report review-142
cd review-142

gh pr list                # open pull requests: number, title, branch
gh pr view 142            # the description and the linked issue
gh pr checkout 142        # create a local branch with the PR's commits
uv run doit               # build the report exactly as the PR leaves it
```

`gh pr checkout` fetches the pull request's branch and switches to it. If
your classmate pushes new commits after your first comments, run it again,
or `git pull`, to get them. `gh pr diff 142` prints the change in the
terminal.

Without `gh`, plain `git` does the same. Clone as usual, then fetch the pull
request by number; GitHub keeps every pull request at `pull/<number>/head`:

```bash
git clone https://github.com/finm-32800/finm_midterm_report.git review-142
cd review-142
git fetch origin pull/142/head:pr-142
git switch pr-142
uv run doit
```

To pick up your classmate's later commits, fetch again with
`git fetch origin pull/142/head` and `git merge FETCH_HEAD`. Then read the
change on the pull request's **Files changed** tab and write the review
there.

**If your store is missing an upstream.** The report reads every upstream
from your ChartBook store, not from the internet. If you have not built one
of them yet, or the pull request adds a new one under `[dependencies]` in
`chartbook.toml`, the build fails. Check from the report folder:

```bash
chartbook pin --frozen     # names any pin in chartbook.lock that is not in a store
```

Then build the missing upstream into your store. For example, for
`ftsfr/fed_yield_curve`, which needs no credentials:

```bash
cd ..
git clone https://github.com/ftsfr/fed_yield_curve
cd fed_yield_curve
git checkout <commit>      # the commit chartbook.lock names for this pipeline
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
chartbook trust
chartbook run --snapshot   # builds it and puts the snapshot in your store
deactivate

cd ../review-142
chartbook pin --frozen     # now finds it
uv run doit
```

The commit is in `chartbook.lock`, in that pipeline's entry under
`"commit"`. Each upstream's README says what credentials it needs
(`ftsfr/options`, for example, needs WRDS). You build an upstream once: the
snapshot stays in your store for every later review and build. An upstream
declared as a `git` dependency, such as a classmate's homework repository,
is simpler: `chartbook run` in the report folder builds it for you from the
pinned commit.

Once it builds and you have read the exhibit and its write-up, leave the
review. Comments on specific lines are easiest on the pull request's
**Files changed** tab. The verdict can come from the terminal:

```bash
gh pr review 142 --comment --body "Built cleanly with uv run doit. Two suggestions inline."
gh pr review 142 --request-changes --body "The build fails at task X; see the log I pasted."
gh pr review 142 --approve --body "Built cleanly; my comments are addressed."
```

When you are done, `git switch main`, or delete the `review-142` folder.

## Conclusion

GitHub pull requests are a cornerstone of modern software development workflows, enabling code review, collaborative development, and project management. By leveraging pull requests, development teams can improve code quality, facilitate communication, and streamline their development process. Adopting best practices around pull requests is essential for maximizing their benefits and ensuring a productive and efficient development environment.


**Extra Notes:**

 - Pull Requests are a great tool and often a necessary part of a given workflow. But they shouldn't necessarily be a part of every workflow. A lot depends on the context. See here: [Why Pull Requests Are A BAD IDEA](https://www.youtube.com/watch?v=ASOSEiJCyEM)