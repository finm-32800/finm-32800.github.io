Course Syllabus: FINM 32800, Autumn 2026
========================================

**FINM 32800, Data Pipelines for Quantitative Research**

##  Summary

**Course Description** *Data Pipelines for Quantitative Research* is a hands-on course centered on building reproducible analytical pipelines: automated and fully reproducible workflows that carry a quantitative analysis from raw data to published result. This course examines every stage of the pipeline, from data extraction and cleaning (extract, transform, load, or ETL), through data validation, exploratory analysis, visualization, modeling, and finally to publication and deployment. In industry, this work is sometimes called *productionizing* research, pairing developers with researchers to translate ideas into practice. The course teaches the core set of tools used to build such pipelines, tools which are common across computing and data science: build automation and CI/CD, dependency management, SQL, unit testing and automated data-quality checks, the Linux command line, SSH and working with remote machines, Git for version control, peer review through GitHub pull requests, and experiment tracking and model monitoring (basic MLOps).

These skills are taught through a series of case studies, each of which introduces students to a new set of tools and a new key financial data set: pricing and fundamentals from CRSP and Compustat, options data from OptionMetrics, corporate bond transactions from FINRA TRACE, intraday trades and quotes from NYSE TAQ, and order book data from CME Globex.

*Prior experience at an intermediate level with Python and the PyData stack is assumed.*

This is an in-person course. We put extra emphasis on class participation and on
getting to know your classmates. Networking with your peers is one of the most
valuable parts of the university experience, both for learning and for your
career, so the course is built around live, interactive components. The two main
vehicles for this are the **final-project proposal presentations** (where you
present your project plan to the class and give and receive live feedback from
your classmates) and the collaborative **Midterm Report** (a single economic
report that the whole class builds together). Attendance at the proposal
presentations is taken.

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

### Assignments

- Assignments are distributed and collected through GitHub Classroom. Check
  GitHub Classroom for the due date for each assignment. Each assignment is
  typically distributed on the day of the lecture and due roughly a week and a
  half later.
- Assignments are automatically graded via the autograder on GitHub Classroom
  and solutions will be released shortly after. This means that the due date is
  strict. Late assignments will not be accepted.
- Each student is to individually submit their assignment (unless otherwise
  specified). Students are encouraged to work in groups, but students are not
  allowed to copy each other's code. Each student must write their own solutions
  individually.
- After assignments are graded, solutions will be posted in separate GitHub
  repos, found under the `finm-32800` GitHub organization page here: 
  https://github.com/finm-32800

### No Exams

This course has **no exams**—no midterm exam and no final exam. In their place:

- The **Midterm Report** (a collaborative, class-wide economic report) takes the
  place of a midterm. See [Midterm Report](midterm_report.md).
- The **final project**, presented and orally defended at the end of the quarter,
  takes the place of a final.

### Final Project

In lieu of a final exam, students will be organized into groups of 2 (pairs) and
will each complete a course project. The final project this term is structured
around three separate milestones:

1. **Instructor consultation** — each group schedules a 1-on-1 meeting with the
   instructor (at least one week before their proposal presentation) to get
   individual feedback on their plan.
2. **Proposal presentation** — each group presents their project plan to the
   class (in an assigned week, roughly weeks 5–8), advertising the data sources
   and the reusable "product" they intend to build. Classmates ask questions and
   submit feedback surveys.
3. **Final project presentation** — in week 10, each group presents the completed
   project and each member is individually quizzed in an oral defense of both
   their analysis and the tools used to build it.

See the [Final Project Instructions and Rubric](FinalProject/final_project_rubric.md)
and the [Proposal Presentation Rubric](FinalProject/proposal_presentation_rubric.md)
for full details.

## Assessment

Grades will be based on the following components:

| Component | Weight |
| --- | --- |
| Coding Assignments | 25% |
| Final Project | 30% |
| Proposal Presentation | 20% |
| Midterm Report | 10% |
| Proposal Attendance & Feedback | 10% |
| Participation | 5% |

- **Coding Assignments (25%)** are submitted individually and graded using
  GitHub's automated testing tools (the GitHub Classroom autograder runs as a
  CI/CD workflow). Because you can re-run the autograder until your code passes,
  the assignments are best thought of as teaching vehicles—the grade primarily
  reflects whether you did the work to complete them.
- **Final Project (30%)** is completed in groups of 2. Students choose their
  project from among the options provided at the beginning of the quarter. It is
  graded not only on how well it accomplishes the assigned data cleaning and
  analysis task, but primarily on whether (1) the steps to reproduce it are fully
  automated and well documented, (2) the code is written in a clean and reusable
  fashion, and (3) the results are presented clearly and convincingly. At the
  week-10 presentation, **each group member is individually quizzed in an oral
  defense**—you will be asked to defend your analysis and design choices and to
  demonstrate that you can actually run and modify the project (e.g., managing
  the conda environment, running the pipeline, using SSH).
- **Proposal Presentation (20%)** is graded on the ambition of your project and
  on how useful and interesting your classmates judge your proposed "product" to
  be, informed by the peer-feedback surveys.
- **Midterm Report (10%)** is your individual contribution (one figure and one
  paragraph) to the collaborative class economic report.
- **Proposal Attendance & Feedback (10%)** requires that, for **every** proposal
  presentation, you both attend (attendance is taken) **and** submit the
  peer-feedback survey. Both are required to earn the points, and there are
  **no allowed misses**—attending and giving feedback to your classmates is an
  expected part of this course.
- **Participation (5%)** depends on the positive impact you have on the class.
  This includes participating in in-class discussions and/or answering questions
  on the class GitHub page (or on Canvas). Students are in no way penalized for
  giving wrong answers in these discussions, nor is there any penalty for asking
  for help—asking for help is often the best way to learn!


## Schedule

The course runs for ten weeks. There are **9 weeks of lectures** (one per week,
with the meeting schedule on Canvas), followed by **final project presentations in week 10**.
The lecture schedule follows the ordering of the chapters listed in the GitHub
book found here: https://finm-32800.github.io/. Each week is its own chapter and
the agenda is listed in the first sub-section of the chapter.

Other key milestones (exact dates to be announced on Canvas and GitHub Classroom):

- **Midterm Report** contributions are due mid-quarter. See [Midterm Report](midterm_report.md).
- **Proposal presentations** take place in class, roughly weeks 5–8.
- **Final project presentations** take place in week 10.

### HW Due Dates

Assignments are distributed and collected through GitHub Classroom. Check GitHub
Classroom for each assignment's due date.

- [HW 0: Flexible. Please complete ASAP](HW0.md)
- [HW 1: TBD — see GitHub Classroom](HW1.md)
- [HW 2: TBD — see GitHub Classroom](HW2.md)
- [HW 3: TBD — see GitHub Classroom](HW3.md)
- [HW 4: TBD — see GitHub Classroom](HW4.md)
- [HW 5: TBD — see GitHub Classroom](HW5.md)

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

