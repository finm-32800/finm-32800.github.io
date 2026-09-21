What Is This Course About? Why Data Pipelines?
==============================================

## What does "Data Pipelines for Quantitative Research" mean?

Open a job posting for the data engineers who work alongside quantitative
researchers and you will find duties like these two, from Citadel Securities'
posting for a Senior Research Engineer (Data):

> "Partner with researchers to produce high-value datasets"
>
> "Translate high-level market research concepts into scalable processes that
> further transform the data"

This is the job that this course trains students to do. Different companies
assign different titles to this role. Commonly used titles include research
engineer, data engineer, and quantitative developer on a data team, but the work
is the same: take research that lives in a researcher's head or in a published
paper and turn it into a production data pipeline that runs reliably every day.
In short, this course teaches students to *productionize* research, to borrow
industry's word for it.

A data pipeline is the path from raw source data to a finished analytical
product: extraction, cleaning, transformation, validation, analysis,
documentation, and publication, automated end to end. *Quantitative research*
is who that path serves. Every case study in this course is one of these
pipelines, and the final project is one built from scratch.

The tools involved are common across computing and data science: build
automation and CI/CD, dependency management, SQL, unit testing and automated
data-quality checks, the Linux command line, SSH and working with remote
machines, Git for version control, peer review through GitHub pull requests,
and experiment tracking and model monitoring (basic MLOps). My hope is that
these tools will serve as a technical foundation for any subfield of financial
computing or data science that the student wishes to pursue. For the hiring
evidence, straight from the firms' own postings, see the
[course map](../Week0/course_map.md) and the
[archived job postings](../Appendix/job_postings.md).


## Objectives

The work described in postings like the one above has two halves, building the
pipeline and knowing the data it carries. The course takes its two objectives
from them directly:

1.	**Build the pipeline.** Teach a core set of software tools and the
principles behind assembling them into analytical workflows that are
reproducible and scalable from end to end. In production, an analysis creates
value only when it is repeatable and its pipeline runs reliably and
predictably, and the same conviction underlies the whole toolkit:
reproducibility is fundamental to good science. This toolkit serves as a
technical foundation for whatever subfield of quantitative finance or data
science a student goes on to pursue.

2.	**Know the data.** Give students hands-on experience with the financial
data sets quantitative researchers use every day, including CRSP, Compustat,
FRED, FINRA TRACE, OptionMetrics, NYSE TAQ, and CME Globex order book data.
Not only does each data set require some degree of domain specific knowledge
(e.g., to clean and interpret properly), but each data set also poses different
computational challenges to use effectively. For example, FINRA TRACE means
working with transaction records at a scale where pandas begins to struggle,
and CME Globex's intraday order book feeds mean confronting exchange-specific
quirks head on. Each case study is designed to give students the basic
knowledge and tools to use these data sets effectively.


## Motivation

### The Core Skill Set: A Bridge Between Financial Computing and Data Science

Given the increasing complexity of the computational sciences, the ability to
produce readable, reusable, and reproducible code and analyses is increasingly
important for backend developers and also for researchers looking to produce
good science and to add value within an organization. The core set of skills
taught in this course are those that I believe bridge the gap between financial
computing and data science. Specifically, they are those that make up the
elements of the analytical pipeline (e.g., reproducible analytical pipelines): 
from data extraction and cleaning to
exploratory analysis, visualization, and modeling, and finally, publication and
deployment. This course aims to teach the tools and principles behind creating
reproducible and scalable workflows, including build automation, dependency
management, unit testing, the command-line environment, shell scripting, Git for
version control, and GitHub for team collaboration. Furthermore, given that this
course is aimed towards students studying quantitative finance, these tools are
taught through a series of case studies that are designed to give students
practical experience with key financial data sets and sources, such as CRSP and
Compustat for pricing and financials, bond transactions from FINRA TRACE, options data
from OptionMetrics, Treasury auction data from TreasuryDirect,
textual data from EDGAR, and high-frequency trade and quote data from NYSE. 

How does this relate to data science? A common characterization of data science describes it as a combination of coding/computer skills, mathematics and statistics, and domain specific knowledge (see the following figure). 

!["The Data Science Venn Diagram", by Drew Conway](assets/data_science_venn_diagram.png)


These computer skills, colloquially referred to as "hacking skills," are called
such because they refer to skills that are often not taught in a traditional
computer science curriculum. A computer science curriculum might include
theoretical courses on algorithms, operating systems, or programming languages,
whereas the practical skills related to the computing ecosystem---the
command-line environment, shell scripting, data wrangling and visualization, or
version control---are often left to the individual to learn on their own. (This
was even the motivation for a supplementary CS course at MIT, 
["The Missing Semester of Your CS Education."](https://missing.csail.mit.edu/)) 
In this
course, I provide a structured overview of these important tools and
demonstrate, by example, how they can be used to (1) collect, clean, and analyze
economic and financial data; (2) facilitate collaboration among groups of
researchers; and (3) to create analyses that are easy for outside users to
reproduce. All of the examples and/or case studies used to illustrate these
concepts and to teach these tools come from common tasks or applications within
quantitative finance, including data wrangling, feature engineering, time series
forecasting, performance evaluation, portfolio optimization, back testing, and
reporting results. 

### An Aside On The Data Science And This History Of Science

The concept of reproducibility has been a cornerstone in the development of the
scientific method and, by extension, the broader history of science.
Reproducibility refers to the ability of an experiment or study to be repeated
with similar results by different researchers, using the same methods under the
same conditions. This principle is crucial for validating the reliability and
universality of scientific findings.

As an example, consider the accomplishments of the historical astronomer Galileo
Galilei. Galileo's work exemplifies how reproducible findings are fundamental
for advancing scientific knowledge and challenging established theories. In the
early 17th century, Galileo improved the design of the telescope and made
astronomical observations that challenged the prevailing geocentric model, which
placed the Earth at the center of the universe. He observed, among other things,
the moons of Jupiter and the phases of Venus. These observations were
reproducible in the sense that other scientists could and did construct similar
telescopes to see the same phenomena. This reproducibility was crucial for the
acceptance of the heliocentric model, which posits the Sun at the center of the
solar system. 


```{admonition} Discussion
:class: note 
What are ways in which principles of reproducibility in the hard sciences might be reflected in data science?
```

<!-- 

- **The Scientific Revolution**: The 17th century's Scientific Revolution further solidified reproducibility as a scientific norm. The works of scientists like Kepler, Galileo, and Newton were often replicated by their contemporaries, which helped establish the credibility of their findings. In data science, our work gains more credibility when it is easy to reproduce, given the right resources.

- **Standardization of Methods**: The 20th century also saw the development of standardized methods and protocols in various scientific fields, which further reinforced the reproducibility of experiments. In data science, we want to adopt the common state-of-the art software tools used by other data scientist for ease of communication and reproducibility. 

- **Reliability of Results**: Reproducibility ensures that scientific findings are not the result of chance, bias, or unique conditions. It is a test of the validity of the findings.

- **Scientific Progress**: Science advances by building on previous work. Reproducibility allows subsequent scientists to rely on existing findings as they push the boundaries of knowledge. As an example, the open source movement facilitates scientific progress by making research methods, data, and tools publicly accessible, allowing scientists to easily replicate and build upon previous work. This transparency and availability of resources enhance the reproducibility and reliability of scientific findings, accelerating the advancement of knowledge. For example, you will find many projects available as open source projects on GitHub. The open science movement, as an example, emphasizes transparency, open access to data and methods, and collaborative research, partly as a response to reproducibility issues.

-->

## Which Tools?

This course focuses on a specific set of skills and tools. 
It is not necessarily a course about methodologies or statistical
concepts.  Rather, it is a set of tools that is applicable to a wide range of
topics that you might study within the field of data science or in financial 
computing. And, as described
previously, these tools are designed to improve reproducibility, help enable
better collaboration within teams of increasing size, and allow researchers to
handle increasingly complex workflows.


- **Command-Line Environment (Example: Bash and the Linux/Unix Command Line
  Environment)**
  - Command-line proficiency is crucial for programmers as it enables the
    automation of repetitive operations through powerful scripting, and provides
    access to a suite of advanced development tools and utilities. It's
    especially important in fields like data science and finance, where direct
    manipulation of the environment and remote server management are often
    required. Additionally, command-line skills are a standard industry
    expectation, integral to effective development and system administration. 


<figure style="text-align:center;">
  <video id="myVideo01" width="75%" controls loop muted>
    <source src="https://missing.csail.mit.edu/static/media/demos/history.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption style="color:grey;">
    Shell demo from "The Missing Semester of Your CS Education"
  </figcaption>
</figure>

<script>
document.addEventListener("scroll", function() {
  var myVideo = document.getElementById('myVideo01');
  var videoPosition = myVideo.getBoundingClientRect().top;
  var screenPosition = window.innerHeight / 2;
  if (videoPosition < screenPosition) {
    myVideo.play();
  } else {
    myVideo.pause();
  }
});
</script>


<!-- Isn't centered
<video width="500" controls>
  <source src="https://missing.csail.mit.edu/static/media/demos/history.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video> 
-->

<!-- Only still image of first frame of video
:::{figure} https://missing.csail.mit.edu/static/media/demos/history.mp4
:width: 50%
An embedded video with a caption!
::: -->

<!-- :::{iframe} https://missing.csail.mit.edu/static/media/demos/history.mp4
:width: 50%
Get up and running with MyST in Jupyter!
::: -->

<!-- Too big
![](https://missing.csail.mit.edu/static/media/demos/history.mp4) 
-->

- **Shells and Shell Scripting (Example: Bash and Bash Scripting)**
  - [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) is the default shell
    for many Linux distributions. Bash scripting in Linux allows for automating
    repetitive tasks. This is particularly useful in quantitative finance for
    automating data retrieval, preprocessing, and analysis tasks, increasing
    efficiency and reducing the potential for human error.


- **Build/Task Automation (Example: `Make` or `PyDoit`)**
  - [`Make`](https://en.wikipedia.org/wiki/Make_(software)) is a build
    automation tool that automatically builds executable programs and libraries
    from source code. [PyDoit](https://pydoit.org/) is similar to make, but
    offers a more Pythonic approach to task automation and dependency
    management, with capabilities like defining tasks in pure Python and better
    handling of file dependencies and Python functions. Such tools are essential
    for quantitative finance researchers because it streamlines the compilation
    and testing of financial models, ensuring efficient and consistent builds,
    particularly in complex Python-based projects.


- **Working with Remote Machines (Example: SSH)**
  - [SSH](https://en.wikipedia.org/wiki/Secure_Shell), or Secure Shell, is a
    protocol used to securely access and manage remote systems. For data
    scientists, SSH is crucial for tasks like running computations on remote
    servers, accessing cloud-based data storage, or managing virtual machines.
    Its secure nature ensures safe transmission of sensitive data, which is a
    common requirement in data science projects, particularly those involving
    confidential financial or personal data. This makes SSH an indispensable
    tool for working efficiently in distributed computing environments and for
    executing tasks on remote machines without compromising security.


<figure style="text-align:center;">
  <video id="myVideo_ssh" width="75%" controls loop muted>
    <source src="https://missing.csail.mit.edu/static/media/demos/ssh.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption style="color:grey;">
    SSH demo from "The Missing Semester of Your CS Education"
  </figcaption>
</figure>

<script>
document.addEventListener("scroll", function() {
  var myVideo = document.getElementById('myVideo_ssh');
  var videoPosition = myVideo.getBoundingClientRect().top;
  var screenPosition = window.innerHeight / 2;
  if (videoPosition < screenPosition) {
    myVideo.play();
  } else {
    myVideo.pause();
  }
});
</script>

```{admonition} Discussion
:class: note 
How many people here have used the Linux command line before? How often? Do you feel comfortable with it? How about `Make` or `ssh`?
```

- **HPC Workload Manager (Example: SLURM)**
  - [SLURM](https://en.wikipedia.org/wiki/Slurm_Workload_Manager) (Simple Linux
    Utility for Resource Management) is a highly configurable open-source
    workload manager designed for Linux clusters of any size. It's critical for
    data scientists working in high-performance computing (HPC) environments, as
    it efficiently schedules and manages computational jobs. In the context of
    data science, especially in fields like quantitative finance that require
    intensive computations, SLURM is invaluable for optimizing resource
    utilization, managing job queues, and ensuring that large-scale data
    processing tasks are executed effectively and efficiently. This contributes
    to enhanced productivity in processing large datasets and complex
    computational tasks, crucial for advanced data analysis and financial
    modeling.

- **Package and Dependency Management (Example: pip)**
  - `pip` is the standard package manager for Python, used for installing and
    managing Python libraries from the Python Package Index (PyPI). `pip` is
    essential for efficiently adding and updating Python libraries. Conda is
    capable of handling non-Python dependencies as well, while pip is
    specifically a Python package manager, focusing on installing and managing
    Python libraries.

- **Virtual Environments (Example: Conda Environments)**
  - [Conda
    environments](https://docs.conda.io/projects/conda/en/latest/user-guide/concepts/environments.html)
    specialize in managing and isolating project-specific dependencies,
    including both Python and non-Python packages. In the realm of quantitative
    finance, this is invaluable as it allows for the creation of distinct,
    reproducible environments for different financial models or analyses,
    preventing conflicts between project dependencies and ensuring consistent
    results. Some similar tools are `virtualenv` or `pipenv`.

- **Unit Testing (Example: pytest)**
  - [pytest](https://docs.pytest.org/en/7.4.x/) is a framework that makes it
    easy to write simple and scalable test cases for Python code. In
    quantitative finance, accurate and reliable unit testing with pytest is
    crucial to ensure that financial models and algorithms perform as expected
    under various scenarios. Unit tests are crucial in more complex coding
    environments and when working in teams. For complex projects, unit tests are
    a way to give yourself confidence that your code is correct. It is also
    important for proving to others that your code is correct and that it
    doesn't break when changes in other parts of the codebase are made.

- **Documentation Generation (Example: Sphinx)**
  - [Sphinx](https://www.sphinx-doc.org/en/master/) is a tool that makes it easy
    to create intelligent and beautiful documentation for software projects,
    particularly written in Python. Good documentation is crucial for ensuring
    that complex data models and algorithms are understandable and usable by
    others, enhancing collaboration and the long-term usability of the work.
    Sphinx supports various output formats, integrates with version control
    systems, and can automatically generate documentation from source code,
    making it a fundamental tool for maintaining clear and effective
    documentation in data science projects. For example of the output, see
    [here.](https://www.statsmodels.org/stable/generated/statsmodels.regression.linear_model.OLS.html#statsmodels.regression.linear_model.OLS)

- **Version Control (Example: Git)**
  - [Git](https://git-scm.com/) is a version control system crucial for managing
    code in development, regardless of whether you're working alone or in a
    team. It enables efficient tracking and management of code changes,
    facilitates easy collaboration with others, and provides a safety net to
    revert to previous versions if needed. Moreover, it's a cornerstone of
    modern software development, crucial for maintaining organization and
    consistency in code projects, especially as they grow in complexity.


<figure style="text-align:center;">
  <video id="myVideo02" width="75%" controls loop muted>
    <source src="https://missing.csail.mit.edu/static/media/demos/git.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption style="color:grey;">
    Git and pytest demo from "The Missing Semester of Your CS Education"
  </figcaption>
</figure>

<script>
document.addEventListener("scroll", function() {
  var myVideo = document.getElementById('myVideo02');
  var videoPosition = myVideo.getBoundingClientRect().top;
  var screenPosition = window.innerHeight / 2;
  if (videoPosition < screenPosition) {
    myVideo.play();
  } else {
    myVideo.pause();
  }
});
</script>


- **Collaborative Coding Platforms (Example: GitHub)**
  - GitHub is a platform for hosting and collaborating on Git repositories. For
    data scientists, GitHub is essential for collaborative development, code
    review, and project management, especially when working on team-based
    projects. It can also serve as a valuable resource for code sharing,
    learning from the wider programming community, and showcasing one’s work,
    making it an essential tool for personal and professional development in
    coding.

## Motivation

Within the context of the academic economics research, these processes have
received much more attention over the last decade. I refer to the following
motivation by economists Matthew Gentzkow and Jesse M. Shapiro in ["Code and
Data for the Social Sciences: A Practitioner's
Guide"](https://web.stanford.edu/~gentzkow/research/CodeAndData.pdf):

> Though we all write code for a living, few of the economists, political
> scientists, psychologists, sociologists, or other empirical researchers we
> know have any formal training in computer science. Most of them picked up the
> basics of programming without much effort, and have never given it much
> thought since. Saying they should spend more time thinking about the way they
> write code would be like telling a novelist that she should spend more time
> thinking about how best to use Microsoft Word. Sure, there are people who take
> whole courses in how to change fonts or do mail merge, but anyone moderately
> clever just opens the thing up and figures out how it works along the way.
> This manual began with a growing sense that our own version of this
> self-taught seat-of-the pants approach to computing was hitting its limits… 
> 
> Here is a good rule of thumb: If you are trying to solve a problem, and there
> are multi-billion dollar firms whose entire business model depends on solving
> the same problem, and there are whole courses at your university devoted to
> how to solve that problem, you might want to figure out what the experts do
> and see if you can’t learn something from it. This [course] is about
> translating insights from experts in code and data into practical terms for
> empirical social scientists.

Within the finance industry, I've seen statements such as the following (see
[here](https://www.efinancialcareers.com/news/2023/07/data-science-jobs-in-finance)
and
[here](https://etnaresearch.notion.site/The-ultimate-guide-for-financial-data-science-931c693a3ff1420688aafbf91ebd1828)):

> We have been in financial data science long enough, when it was called
> quantitative finance, to see some clear patterns that we believe are
> detrimental to the career of a financial data scientist: lack of fundamental
> core knowledge in computer science, lack of SQL knowledge, … lack of
> visualization abilities to share business insights… Technical knowledge is, in
> itself, not what's most important; it's an understanding of technical
> functions within a production environment that truly prove a candidate's
> worth… Data scientists can be super smart, but if no lines of their code will
> go into production, it's a waste of talent.

This course is designed to explore the core tools and techniques of data
science, with an emphasis on the modern software tools and techniques that make
such messy, large-scale projects not only feasible, but also easy to reproduce
and reuse. This includes shell scripting, build automation, dependency
management, unit testing, version control with Git, and effective collaboration
with GitHub. We will explore these tools from the perspective of a quantitative
researcher or data scientist working in finance, drawing on real-world
applications from finance and economics, with an emphasis on hands-on work with
actual data sets. 


### The Missing Semester of Your CS Education

This class, focusing on this list of tools, is inspired by a great course taught
by members of the Computer Science Department at MIT called ["The Missing
Semester of Your CS Education"](https://missing.csail.mit.edu/). They motivate
their course as follows:

>Classes teach you all about advanced topics within CS, from operating systems
>to machine learning, but there’s one critical subject that’s rarely covered,
>and is instead left to students to figure out on their own: proficiency with
>their tools. We’ll teach you how to master the command-line, use a powerful
>text editor, use fancy features of version control systems, and much more!
>
>Students spend hundreds of hours using these tools over the course of their
>education (and thousands over their career), so it makes sense to make the
>experience as fluid and frictionless as possible. Mastering these tools not
>only enables you to spend less time on figuring out how to bend your tools to
>your will, but it also lets you solve problems that would previously seem
>impossibly complex.

I highly recommend taking some time to browse their website and read more about
their motivation [here.](https://missing.csail.mit.edu/about/)