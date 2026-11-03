# Week 6: Data Across Repositories: Snapshots, Lineage, and the Class Report

```{toctree}
:maxdepth: 1
Week3/ftsfr.md
Week6/chartbook_catalog.md
Week6/pins_walkthrough.md
Week1/collaborative_report_and_style_guides.md
Week8/medium_sized_data_strategies.md
Week6/study_guide.md
```

Every pipeline you have built so far pulls its own data. This week one
pipeline reads data that another one made. The hard part is doing that
without copying the other project's pull code and without reading whatever
happens to sit in its folder today. Git solved the same problem for code:
name every version of a file by the hash of its bytes, and commit what you
depend on. ChartBook 0.5 does the same for data. A project declares its
output ready with a snapshot, and a project that reads it pins that snapshot
in a lockfile committed next to its code.

The FTSFR paper is why it matters. When only the cleaning of a dataset
changes, the ranking of forecasting methods changes with it, so a result is
only as fixed as the data behind it. And [HW 4](./HW4.md), the class report,
launches today. It is built this way: every exhibit reads a pinned upstream,
and the best upstreams are your own homework.

## Announcements

- **From the Career Development Office.** Alumni Coffee Chats run Monday,
  November 9, through Friday, November 13. Scan the code to register.
  <!-- Shown at the start of class at the CDO's request. The slide itself is
  _announcements/Upcoming CDO Events 2026-11-03.pptx in the textbook repo. -->

  ![Upcoming CDO events: Alumni Coffee Chats, November 9 to 13. Scan the
  code to register.](./Week6/assets/upcoming_cdo_events_nov.png)

- **[HW 3](./HW3.md) is due next week,** Tuesday, November 10, at 9:30 am.
- **[HW 4](./HW4.md), the class report, launches today.** It is due
  Friday, November 20, at 11:59 pm. Open your pull
  request by Thursday, November 12, so your reviewers have time.
- **Accept your invite to the report repo.** Every student gets a GitHub
  collaborator invite to the private report repository. Check your email or
  [github.com/notifications](https://github.com/notifications) and confirm in
  class that you can open it.
- **Upgrade to ChartBook 0.5 or newer before you start HW 4.** All homework
  moves to 0.5. See *Upgrade to ChartBook 0.5* in
  [ChartBook: Pipelines, Stores, and Lineage](./Week6/chartbook_catalog.md);
  `chartbook --version` should print `0.5.0` or newer.
- **[HW 5](./HW5.md):** dates to be announced on Canvas.
- **The exam is Tuesday, December 1,** in week 9, covering weeks 0 through 8.
  See [Exam Preparation](./exam_prep.md).
- **Project consultations end next week.** If your group has not had one, book
  it today: [Microsoft Bookings](https://bookings.cloud.microsoft/bookwithme/user/256043fbc7aa4a39905fa3b2920bd8c5@UCHICAGO.EDU/meetingtype/OF6E4r_zPkmqIADL4BDtUA2?bookingcode=19494481-e86a-4fec-8be6-5ae4223d55ce&anonymous&ismsaljsauthenabled&ep=mLinkFromTile).

## Objectives

- Tell a unit test from a plausibility check from data validation.
- Say what FTSFR provides and why a comparison of methods needs pinned data.
- Define hash, snapshot, store, pin, lockfile, lineage, provenance; say
  which question `doit`, Airflow, and ChartBook each answer.
- Declare a dependency, pin it, read it by name, read a lockfile diff.
- Start HW 4.

## Agenda

1. **HW 3 check-in** ([HW 3](./HW3.md)): Part C, the five blanks, the
   first deploy's red X, `doit` vs `doit latest`, `monitor-history`.
2. **Tests, plausibility checks, data validation**
   ([Unit Tests](./Week5/unit_tests.md),
   [Data Validation](./Week5/data_validation.md)): what each one asks;
   testing a pull without pulling; stop, or flag and publish.
3. **The paper: FTSFR** ([FTSFR](./Week3/ftsfr.md),
   [slides](./Week6/assets/slides_ftsfr.pdf)): the benchmarking gap; one
   pipeline per dataset, validated; code, not data; results; Section 6,
   where cleaning reshuffles the leaderboard.
4. **Vocabulary** ([ChartBook](./Week6/chartbook_catalog.md)): three
   questions, three tools; content hash, Merkle tree, `pinned_hash`;
   snapshot, store, pin, lockfile; lineage vs provenance; rebuild or read
   the snapshot, and the committed cutoff date; DVC, Delta Lake; why Parquet
   is fast; Polars.
5. **Demo** ([walkthrough](./Week6/pins_walkthrough.md)): the `summer`
   branch; `main` and its lockfile; hashes by hand; a live git dependency,
   `pin` vs `update`; rebuild an upstream, same code, later day, different
   hash.
6. **HW 4** ([HW 4](./HW4.md)): one exhibit each; the data rule; use your
   homework; claim, branch, pin, pull request, two reviews; a look at the
   repository.

**Next week:** Apache Airflow, which decides *when* a pipeline runs, where
ChartBook records *what* it made.
