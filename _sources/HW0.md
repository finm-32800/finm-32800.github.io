# Homework 0

- **Due date:** None. This is an ungraded practice homework. Try to finish it during the first week, because HW 1 assumes that everything here works.
- **Repository:** <https://github.com/finm-32800/hw0>
- **Goes with:** [Lecture 0](./overview_w0.md)

HW 0 has two purposes. It gets your computer and your accounts ready for the rest of the quarter. And it takes you once through the cycle that every later homework repeats: clone a repository, install its environment, run it, edit the code until the tests pass, and push.

## Part 1: Set up your computing environment

Follow [Getting Set Up](./Week0/getting_set_up.md) to install the software and create the accounts. **Apply for your WRDS account first,** because approval takes several days and HW 1 requires it.

## Part 2: Get connected to the course

- "Watch" the [course website repository](https://github.com/finm-32800/finm-32800.github.io) so that you are notified of new posts on the course discussion board. Click the "Watch" button at the top right of the page.
- Consider posting an introduction on the discussion board: https://github.com/orgs/finm-32800/discussions/2 . Say that you're looking for a partner to work on the homework and the final project with.
- Skim the [tour of the course](./Week0/tour_of_the_course.md) and open one of the [past final projects](./FinalProject/past_final_projects.md).

## Part 3: Clone and run the HW 0 repository

1. Make your own copy of <https://github.com/finm-32800/hw0>. On that page, click **Fork**. (Once you are enrolled, I will create a private copy of each graded homework for you. HW 0 is public, so a fork works.)
2. Clone your fork and install its environment, as described in [Clone and Run](./Week0/clone_and_run.md).
3. From the base directory of the repository, run `doit`. This downloads a small extract of monthly stock returns from CRSP into the `_data` folder, then executes the notebooks and saves HTML copies of them in `_output`. Once your WRDS account is approved, try pulling the same data directly from WRDS instead: copy `.env.example` to `.env`, fill in your `WRDS_USERNAME`, set `NO_CACHE=True`, and run `doit` again.
4. Launch the dashboard with `streamlit run src/app.py`. In the "Two assets" tab, drag the correlation slider toward -1. In the "Many assets" tab, shorten the estimation window and watch what happens to the tangency portfolio weights. Then check the "No short sales" box and compare the two frontiers.
5. Read through the [portfolio selection notebook](notebooks/_01_markowitz.ipynb) and its [appendix](notebooks/_02_markowitz_derivation.ipynb), which derives the formulas. The sources for both are in the repository's `src` folder.

## Part 4: Make the tests pass

**Which files should I edit?**

In order to complete the homework, you need to adjust the source files so that the unit tests pass. The unit tests are implemented in the files that start with `test_`. In HW 0, you will need to edit `src/port_opt.py` and `src/github_skills.py`.

**NOTE:** You should not make any edits to the test files. In the graded homeworks, if an edit is made to a test file, you will be required to edit the history of your commits to remove any trace of the edits to these files.

### Portfolio optimization

The notebook computes the global minimum variance portfolio and the tangency portfolio inline. Move that logic into the two functions marked `TODO` in `src/port_opt.py`. Then, from the base directory of the project, run

```bash
pytest
```

The five tests in `test_port_opt.py` should now pass. They use small made-up inputs with known answers, so they do not need the data. (The tests in `test_mean_variance.py` pass from the start. They check the frontier code that I wrote.)

### GitHub Skills tutorials

In order to start mastering the many features of GitHub, please complete the following tutorials from the [GitHub Skills](https://skills.github.com/) page. Please make sure to use **public repositories** for this in your own GitHub user account.

 - [Introduction to GitHub](https://github.com/skills/introduction-to-github)
 - [Communicate using Markdown](https://github.com/skills/communicate-using-markdown)
   - This will give you some ideas on how to more effectively communicate on the course [discussion board](https://github.com/orgs/finm-32800/discussions).
 - [GitHub Pages](https://github.com/skills/github-pages)
   - Later in this course, we will use GitHub Pages to host a website. The textbook for this course is hosted on GitHub Pages.

Once you have completed these tutorials, record them by editing `src/github_skills.py`: add the URL of each of your completed skills repositories and mark each one as finished.

### Push, and watch the tests run on GitHub

Commit your changes and push them to your fork. Then open the **Actions** tab of your fork on GitHub. (The first time, GitHub asks you to enable workflows on a fork. Click the button to do so, then push again or press "Run workflow.") The same tests that you ran on your laptop now run on a fresh machine in the cloud. A green check mark next to your commit means that they passed. In the graded homeworks, this is how your work is scored.

## Additional Notes about the HW

### Why are we doing it this way?

The point of structuring the assignment this way is to give you experience with unit testing, CI/CD, and the concept of test-driven development. These are concepts that you should learn to level-up your software development skills. These are real-world development concepts and getting experience with them should move you beyond the over-simplified approach that you might find in a typical university problem set.

We discussed unit tests and test-driven development in class a little. If you'd like more in-depth information, please watch these YouTube videos:

["Software Testing Explained in 100 Seconds"](https://www.youtube.com/watch?v=u6QfIXgjwGQ) (This short video is written from the perspective of a web developer, but the same concepts apply to us.)

<iframe width="560" height="315" src="https://www.youtube.com/embed/u6QfIXgjwGQ?si=YfGOSYf0jVvi89Dl" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

["Test-Driven Development In Python // The Power of Red-Green-Refactor"](https://www.youtube.com/watch?v=B1j6k2j2eJg) (This video discussing unit testing with Python and PyTest.)

<iframe width="560" height="315" src="https://www.youtube.com/embed/B1j6k2j2eJg?si=9055n2Kcf-c3zNJV" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### How can I check if my tests are passing?

From the command-line, in the base directory of the project, run `pytest`. This is a local test. In the graded homeworks, your grade will depend on whether the tests pass in GitHub Actions.

### More information about Git and GitHub

Also, if you're looking for more instruction about how to use Git, here are two videos that I might recommend:

- [What is Version Control? (Learn Git Video Course)](https://www.youtube.com/watch?v=M-O8ZNW9icQ)
- [Using Git with Visual Studio Code (Official Beginner Tutorial)](https://www.youtube.com/watch?v=i_23KUAEtUM)
