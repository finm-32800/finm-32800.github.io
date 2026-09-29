Course Syllabus: FINM 32800, Autumn 2026
========================================

**FINM 32800, Data Pipelines for Quantitative Research**

A PDF version of this syllabus is available for download [here](https://finm-32800.github.io/_static/syllabus_finm_32800_data_pipelines_for_quantitative_research.pdf).

##  Summary

**Course Description** *Data Pipelines for Quantitative Research* is a hands-on course centered on building reproducible analytical pipelines: automated and fully reproducible workflows that carry a quantitative analysis from raw data to published result. This course examines every stage of the pipeline, from data extraction and cleaning (extract, transform, load, or ETL), through data validation, exploratory analysis, visualization, modeling, and finally to publication and deployment. In industry, this work is sometimes called *productionizing* research, pairing developers with researchers to translate ideas into practice. The course teaches the core set of tools used to build such pipelines, tools which are common across computing and data science: build automation and CI/CD, dependency management, SQL, unit testing and automated data-quality checks, the Linux command line, SSH and working with remote machines, Git for version control, peer review through GitHub pull requests, and experiment tracking and model monitoring (basic MLOps).

These skills are taught through a series of case studies, each of which introduces students to a new set of tools and a new key financial data set: pricing and fundamentals from CRSP and Compustat, options data from OptionMetrics, corporate bond transactions from FINRA TRACE, intraday trades and quotes from NYSE TAQ, and order book data from CME Globex.

*Prior experience at an intermediate level with Python and the PyData stack is assumed.*

This is an in-person course. We put extra emphasis on class participation and on
getting to know your classmates. Networking with your peers is one of the most
valuable parts of the university experience, both for learning and for your
career, so the course is built around live, interactive components. The main
vehicles for this are the live programming exercises in class, the group
**final project**, and the collaborative **class report** (a single economic
report that the whole class builds together as one of the homework
assignments, where you review two classmates' work and two classmates review
yours).

- **Class:** One session per week, in person. The day, time, and room are listed
  on Canvas.
- **Lecturer:** Jeremy Bejarano, jbejarano@uchicago.edu
- **Instructor Office Hours:** By appointment. Schedule a 1-on-1 consultation
  using the booking link posted on Canvas.
- **Teaching Assistant:**
  - To be announced on Canvas.
  - Note: Students are strongly encouraged to post questions on the discussion
    page of the class GitHub repository (see below) rather than emailing, so that
    the whole class can benefit from the answers.

- **TA Office Hours:**
  - To be announced. See the Canvas calendar for the schedule.

- **Textbook:** The text for the course will be published incrementally here:
  https://finm-32800.github.io/
- **Website:** Canvas will be used for grades, announcements, and links
  only. Homework and notes will be posted on the course textbook above. All
  code related to the course, including the code that generates the textbook and the code
  for the homework assignments will be posted in GitHub repos within the finm-32800 
  organization here: https://github.com/finm-32800.
  Questions and other discussion should be posted on the GitHub discussion
page here: https://github.com/orgs/finm-32800/discussions
  Class-related discussions should be posted here as well.
- **In-class examples:** Many lectures are accompanied by small, runnable code
  examples collected in a companion repository, the **in-class examples repo**:
  https://github.com/finm-32800/inclass_examples. We use this repository
  throughout the course---individual textbook chapters link to the relevant
  subfolders (for example, `software_environments/`, `pydoit/`, `env_vars/`,
  `sphinx/`, and `unit_tests/`). Clone it and follow along during class.

### Coursework at a Glance

The graded work has three parts: homework, one midterm, and a group project.
From week 4 on, one homework or exam is due each week, and never two.

| Week | Date | Due |
| --- | --- | --- |
| 4 | Tuesday, October 20 | [HW 1](HW1.md): CRSP, the CAPM, and Fama-French |
| 5 | Tuesday, October 27 | [HW 2](HW2.md): the yield curve and the policy path |
| 6 | Tuesday, November 3 | [HW 3](HW3.md): option-implied crash probabilities |
| 7 | Tuesday, November 10 | [HW 4](HW4.md): order book validation and flow toxicity |
| 8 | Tuesday, November 17 | Midterm, in class |
| | Tuesday, November 24 | No class: Thanksgiving break |
| 9 | Tuesday, December 1 | [HW 5](HW5.md): your figure for the class report |
| 10 | Week of December 8 | Final project presentation |

The homework comes early on purpose. The four coding assignments are finished
by week 7, and the midterm follows a week later, before Thanksgiving. What
remains after the break is your figure for the class report and your project.

### Homework

- There are five homework assignments: four coding assignments, and your
  contribution to the class report, which is one figure and one paragraph.
  [HW 0](HW0.md) is ungraded setup; please complete it as soon as possible.
- Homework is distributed and collected through GitHub, and it is due at the
  start of class. The assignments launch in class in weeks 1, 3, 4, 5, and 6,
  each in the week its material is taught, and the lectures keep teaching the
  pieces of an open assignment after it launches.
- Assignments are automatically graded by an autograder that runs on GitHub
  Actions, and solutions will be released shortly after. This means that the due date is
  strict. Late assignments will not be accepted.
- Each student is to individually submit their assignment (unless otherwise
  specified). Students are encouraged to work in groups, but students are not
  allowed to copy each other's code. Each student must write their own solutions
  individually.
- After assignments are graded, solutions will be posted in separate GitHub
  repos, found under the `finm-32800` GitHub organization page here: 
  https://github.com/finm-32800

### Midterm Exam

There is one exam, a **midterm**, held in class in week 8. It is in-person,
closed-book, multiple choice, and answered on a bubble sheet. Each question
has four options and **one or more may be correct**; a question earns its
point only if every bubble is right. It covers the material through week 7, so
it comes after all four coding assignments are done.
Practice questions in the same format, with their answer key, are handed out
ahead of time. There is no final exam; the final project is the capstone. See
[Exam Preparation](exam_prep.md) for the scope and format.

### Final Project

In place of a final exam, students will be organized into groups of exactly four
and will each complete a course project. Students cannot work alone, and a group
of any other size requires the instructor's permission in advance. The project
has two steps:

1. **Consultation.** Each group schedules a 1-on-1 meeting with the instructor,
   in weeks 4 through 6, to get individual feedback on their plan: the data
   sources and the reusable "product" they intend to build.
2. **Final presentation.** In week 10, each group presents the completed
   project and each member is individually quizzed in an oral defense of both
   their analysis and the tools used to build it.

See the [Final Project Instructions and Rubric](FinalProject/final_project_rubric.md)
for full details.

## Assessment

Grades will be based on the following components:

| Component | Weight |
| --- | --- |
| Homework | 30% |
| Midterm Exam | 25% |
| Final Project | 40% |
| Participation | 5% |

- **Homework (30%)** is submitted individually and graded using
  GitHub's automated testing tools (the autograder runs as a CI/CD workflow on
  GitHub Actions). Because you can re-run the autograder until your code passes,
  the assignments are best thought of as teaching vehicles—the grade primarily
  reflects whether you did the work to complete them. One assignment is your
  contribution to the collaborative class report: one figure and one paragraph,
  submitted as a pull request and reviewed by two classmates.
- **Midterm Exam (25%)** is in-person, closed-book, and multiple choice, with
  one or more correct options per question and no partial credit. It tests the
  tools, the data sets, and the papers covered in the notes and the homework
  through week 7. See [Exam Preparation](exam_prep.md).
- **Final Project (40%)** is completed in groups of exactly four; any other size
  requires the instructor's permission in advance. Students choose their
  project from among the options provided at the beginning of the quarter. It is
  graded not only on how well it accomplishes the assigned data cleaning and
  analysis task, but primarily on whether (1) the steps to reproduce it are fully
  automated and well documented, (2) the code is written in a clean and reusable
  fashion, and (3) the results are presented clearly and convincingly. At the
  week-10 presentation, **each group member is individually quizzed in an oral
  defense**—you will be asked to defend your analysis and design choices and to
  demonstrate that you can actually run and modify the project (e.g., managing
  the conda environment, running the pipeline, using SSH).
- **Participation (5%)** depends on the positive impact you have on the class.
  This includes participating in in-class discussions and/or answering questions
  on the class GitHub page (or on Canvas). Students are in no way penalized for
  giving wrong answers in these discussions, nor is there any penalty for asking
  for help—asking for help is often the best way to learn!


## Schedule

The course runs for ten weeks. There are **9 weeks of lectures** (one per week,
with the meeting schedule on Canvas), followed by **final project presentations in week 10**.
There is no class on November 24, during Thanksgiving break, so week 9 meets on
December 1. The lecture schedule follows the ordering of the chapters listed in the GitHub
book found here: https://finm-32800.github.io/. Each week is its own chapter and
the agenda is listed in the first sub-section of the chapter.

The due dates are in the table under Coursework at a Glance, above, and on
Canvas. Each assignment's page gives its launch date and its due date.

## References

I will provide the lecture notes that we will use in class here:
https://finm-32800.github.io/. As a prerequisite, you should have some prior
familiarity with Python and the PyData stack (e.g., Numpy, Scipy, Pandas,
Matplotlib). The following references may serve as useful refreshers:

- [Python for Data Analysis, 3rd Edition](https://wesmckinney.com/book/), by Wes
  McKinney
- [Python Data Science
  Handbook](https://jakevdp.github.io/PythonDataScienceHandbook/), by Jake
  VanderPlas
- [Python Programming for Economics and
  Finance](https://python-programming.quantecon.org/intro.html), by Thomas J.
  Sargent and John Stachurski

A significant portion of this course is inspired by ["The Missing Semester of
Your CS Education"](https://missing.csail.mit.edu/), a short course taught in
the Computer Science department at MIT. I'll rely on the material shown there
for portions of this course.


### The job-postings dataset

The claim that this course teaches what quantitative finance firms hire for is
backed by a dataset rather than by intuition. Every few months a script collects
job postings from the applicant-tracking systems that firms use to publish their
own openings, and counts which technologies the postings name. It currently covers
64 firms and a few thousand postings, and it is built so any figure can be
rebuilt from the committed data with no credentials and no network.

- **[What firms ask for](https://finm-32800.github.io/notebooks/_job_postings_findings_ipynb.html)**
  — the findings.
- **[Sample, methods and limitations](https://finm-32800.github.io/notebooks/_job_postings_methods_ipynb.html)**
  — the sample frame firm by firm, the ways of counting, the extraction rules, and
  what the data cannot support.

The collector and the corpus live in a separate private repository,
[`finm-32800/job_postings`](https://github.com/finm-32800/job_postings). Posting
text is reproduced there for educational analysis and is not republished on this
site. If you want to read the postings themselves, clone that repository and build
the browsable archive:

```bash
git clone https://github.com/finm-32800/job_postings.git
cd job_postings && pip install -r requirements.txt
doit
python -m http.server -d _output/job_postings_archive 8000
```

You can then filter by firm, firm type, tool, or whether the role is still open,
read the full text of any posting, and expand "why these tools matched" to see the
sentence behind every extracted feature. If a count looks wrong to you, that is
where to check it, and I would like to hear about it.


## Software to be used in class

Lectures will feature live programming exercises in class, so students should
have a WiFi-enabled laptop to bring to class.

Before the first class, please make sure to install the required software and
sign up for the required services. Students will need to install the following
software on their laptop. Each of these pieces of software are free:
 - [Anaconda distribution of Python](https://www.anaconda.com/download)
 - [Visual Studio Code](https://code.visualstudio.com/) (NOT Visual Studio.
   Visual Studio Code is different from Visual Studio)
 - [Git](https://git-scm.com/)
 - [GitKraken](https://www.gitkraken.com/) You will need to use GitKraken Client
   Pro, which is available for [free for
   students.](https://www.gitkraken.com/github-student-developer-pack)
 - [TeX Live](https://tug.org/texlive/)
 - [PuTTY](https://www.putty.org/)
 - [WinSCP](https://winscp.net/eng/download.php) For those using a Mac, you may
   need to find a software alternatives for WinSCP.

Students should also sign up for an account with the following websites. We will
use free versions of each of these services:
 - [GitHub](https://github.com/)
 - [Wharton Research Data Services (WRDS)](https://wrds-www.wharton.upenn.edu/)
   Apply for access through the University of Chicago, using the registration
   form [here.](https://wrds-www.wharton.upenn.edu/register/) For any issues
   that may arise, please contact the WRDS representative for UChicago's
   Mathematics department, John Zekos, zekos@math.uchicago.edu. 


## Quick Start

To quickest way to run code in this repo is to use the following steps. First, you must have the `conda`  
package manager installed (e.g., via Anaconda). Second, you 
must have TexLive (or another LaTeX distribution) installed on your computer and available in your path.
You can do this by downloading and 
installing it from here ([windows](https://tug.org/texlive/windows.html#install) 
and [mac](https://tug.org/mactex/mactex-download.html) installers).
Having done these things, open a terminal and navigate to the root directory of the project and create a 
conda environment using the following command:
```
conda create -n finm python=3.12
conda activate finm
```
and then install the dependencies with pip
```
pip install -r requirements.txt
```
Finally, you can then run 
```
doit
```
And that's it! The landing page of the textbook website will be available at `./_build/html/index.html`.

