# Study Guide: Week 2

Open-ended questions on the material from week 2. The exam asks
multiple-choice versions of questions like these. No answers are given: each
question can be answered in a few sentences from the pages listed under its
heading. The exam's format is described in
[Exam Preparation](../exam_prep.md).

## Fama and French (1993)

*Read: [From the CAPM to Multifactor Models](../notebooks/_08_CAPM_to_multifactor_models_ipynb.ipynb),
[HW 1 Guide D: Constructing the Fama-French Factors](../notebooks/_05_Fama_French_1993_ipynb.ipynb),
and [HW 1 Guide E: Testing the CAPM and the Three-Factor Model](../notebooks/_06_CAPM_and_Fama_French_ipynb.ipynb).*

1. Fama and French compute size and book-to-market breakpoints from NYSE
   stocks only but assign NYSE, AMEX, and NASDAQ stocks to the portfolios.
   Why? What would change if the breakpoints used all three exchanges?
2. Describe how Fama and French build book equity from Compustat, and explain
   why the sort in June of year $t$ pairs book equity from fiscal year $t-1$
   with market equity from December of $t-1$.
3. A regression of a portfolio's excess return on the market's excess return
   has an intercept, called alpha, and the CAPM says it should be zero. Fama
   and French find alphas that are not zero on portfolios sorted by size and
   book-to-market, and smaller alphas once SMB and HML are added to the
   regression. What does each finding mean?
4. CRSP identifies a security by PERMNO and Compustat identifies a company by
   GVKEY. Why can you not merge the two on ticker, and what does the
   CRSP-Compustat link table provide?

## SQL

*Read: [Basics of SQL](../notebooks/_02_basics_of_SQL_ipynb.ipynb).*

5. What does each of these SQL keywords do: `SELECT`, `FROM`, `WHERE`,
   `GROUP BY`, `ORDER BY`, `LIMIT`?
6. Describe in words what this query returns. What is one row of the result?

   ```sql
   SELECT date, AVG(ret) AS avg_return
   FROM crspm.msf
   WHERE date BETWEEN '2023-01-01' AND '2023-12-31'
   GROUP BY date;
   ```

7. This query attaches a company name to each monthly return. Why
   `LEFT JOIN` and not a plain `JOIN`? What happens under each to a row of
   `msf` that has no matching name?

   ```sql
   SELECT msf.date, msf.permno, msf.ret, msenames.comnam
   FROM crspm.msf AS msf
   LEFT JOIN crspm.msenames AS msenames
     ON  msf.permno = msenames.permno
     AND msenames.namedt <= msf.date
     AND msf.date <= msenames.nameendt;
   ```

8. In the query in question 7, the `ON` clause has two conditions on dates.
   What are they for? What would go wrong for a company that changed its name
   if you joined on `permno` alone?

## Task Runners and PyDoit

*Read: [What is a build system or task runner?](../Week3/what_is_a_task_runner.md)
and the [PyDoit Examples Walkthrough](../Week3/doit_examples.md).*

9. What problem does a task runner solve? Name two things that go wrong when
   you run a multi-step project by hand.
10. What is a DAG, and why is a data pipeline one?

Questions 11 to 15 use this `dodo.py`. It defines three tasks, each of which
runs one Python script.

```python
def task_create_data():
    return {
        'actions': ['python create_data.py'],
        'file_dep': ['create_data.py'],
        'targets': ['_output/data.txt'],
    }

def task_process_data():
    return {
        'actions': ['python process_data.py'],
        'file_dep': ['process_data.py', '_output/data.txt'],
        'targets': ['_output/processed.txt'],
    }

def task_create_report():
    return {
        'actions': ['python create_report.py'],
        'file_dep': ['create_report.py', '_output/processed.txt'],
        'targets': ['_output/report.txt'],
    }
```

11. What do `actions`, `file_dep`, and `targets` each mean?
12. You run `doit` in a fresh clone, then run `doit` again without changing
    anything. What happens each time?
13. You edit `process_data.py` and run `doit`. Which tasks run and which do
    not? Why?
14. You delete `_output/report.txt` and run `doit`. Which tasks run?
15. Suppose `task_process_data` left `'_output/data.txt'` out of its
    `file_dep`. What goes wrong the next time the data changes?
16. How is a `dodo.py` like a Makefile? Give one reason the course uses
    PyDoit instead of Make.
