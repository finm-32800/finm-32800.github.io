# Week 6: Python Packages, FINM, and the ChartBook Catalog

```{toctree}
:maxdepth: 1
Week6/chartbook_catalog.md
Week6/python_packaging_with_hatch.md
```

## Learning Outcomes

- See the next two final-project proposals and give substantive peer feedback.
- Understand the ChartBook *catalog*: how it pins together data dependencies between repositories, and how to set one up on your own machine ([The ChartBook Catalog](./Week6/chartbook_catalog.md)) using cloned pipelines from the [FTSFR organization](https://github.com/orgs/ftsfr/repositories).
- Understand what changed in this week's ChartBook 0.1.x release (manifest format v2, catalog auto-discovery, single install) and how a release like this is actually cut: changelog, semantic versioning, `hatch build`, publish to PyPI.
- Understand the anatomy of a published Python package (`pyproject.toml`, a build backend, versioning, CLI entry points), and know the packages we'll keep working with: [`chartbook`](https://pypi.org/project/chartbook/), [`finm`](https://pypi.org/project/finm/), and the TestPyPI practice package [`stockbeta`](https://test.pypi.org/project/stockbeta/).
- Know the plan for your Midterm Report figure: build it on data produced by an FTSFR pipeline, consumed through your catalog with `data.load(...)`.

## Announcements

- [HW 3](./HW3.md) is due **this Friday, July 24**.
- **Upgrade ChartBook**: a new major release shipped this week. Run `pip install --upgrade chartbook` (you want 0.1.1 or later). It contains breaking changes we'll discuss tonight; see [The ChartBook Catalog](./Week6/chartbook_catalog.md).
- **Midterm report: claim your topic now if you haven't.** Every candidate topic is an open issue in [finm-32800/finm_midterm_report](https://github.com/finm-32800/finm_midterm_report); assign one to yourself to claim it. Your merged contribution is due **Monday, August 10 at 11:59 pm CT**. Don't wait to open your pull request: an early draft PR gets you review comments while there is still time to act on them.
- **Peer-feedback survey** for tonight's two groups: same as last week. Submit during the quiet time after each presentation; the [form](./FinalProject/proposal_feedback_survey.md) stays open until **Friday at 11:59 PM** and doubles as the attendance record.
- **If you present July 30**, your instructor consultation must happen this week; [book it now](https://finm-32800.youcanbook.me/) if you haven't.

## Agenda Item 1: Proposal Presentations (Two Groups)

Two more groups present tonight, in the order on the [schedule](./FinalProject/proposal_presentation_schedule.md): 10 minutes to present, 5 minutes of Q&A, then ~5 minutes of quiet time to submit the [peer-feedback survey](./FinalProject/proposal_feedback_survey.md). Same reminder as last week: the presentation is an *advertisement* for the product you'll build ([rubric](./FinalProject/proposal_presentation_rubric.md)).

## Agenda Item 2: The Midterm Report and the ChartBook Catalog

First, a working check-in on the [Midterm Report](./midterm_report.md): confirm every topic issue has an owner, then review one or two open pull requests live, applying the [PR workflow](./Week6/GitHub_pull_requests.md) from last week.

Then the architecture question the report forces on us: **what should your contribution do when it needs data that another repository already produces?** Copying pull code duplicates work and drifts out of date. The answer is the ChartBook *catalog*, covered in tonight's new chapter, [The ChartBook Catalog: Data Dependencies Across Repositories](./Week6/chartbook_catalog.md):

- Pipelines vs. catalogs: a catalog is a `chartbook.toml` with a `[pipelines]` registry, and the [FTSFR organization](https://github.com/orgs/ftsfr/repositories) (~20 pipeline repos plus one [`catalog`](https://github.com/ftsfr/catalog) repo) is the production-scale example.
- **Hands-on**: clone FTSFR pipeline repos, register them in your global catalog (`chartbook catalog add`, or `members` auto-discovery), build and browse the combined site, and load another repo's dataframe by name with `data.load(pipeline=..., dataframe=...)`.
- **For your midterm figure**: build it on data an FTSFR pipeline already produces. The chapter lists exactly which repos run on free public data or WRDS alone (eleven of them); the Bloomberg-dependent ones are off the menu.
- **Live demo**: wire one midterm-report contribution to consume an FTSFR dataframe this way, so the report becomes a downstream consumer of maintained pipelines rather than a pile of one-off pulls.

## Agenda Item 3: Shipping a Release: ChartBook 0.1.0 as a Case Study

You have been `pip install`-ing [`chartbook`](https://pypi.org/project/chartbook/) all quarter, and this week it shipped a breaking release (0.1.0 on July 22, 0.1.1 on July 23). That makes it a live case study in how real packages evolve and how a release actually happens:

- **What changed and why**: read the [changelog](https://backofficedev.github.io/chartbook/changelog.html) together. Manifest format v2 (one `[project]` table, every field optional, de-prefixed entity names), scoped pipeline IDs like `ftsfr/crsp_treasury`, catalog auto-discovery, and the retirement of the `[data]`/`[plotting]`/`[all]` extras in favor of a single install.
- **Versioning as a contract**: semantic versioning, why a breaking change in a `0.x` package bumps the minor version (`0.0.21` → `0.1.0`), and how a migration script eases users across a breaking change.
- **How the release was cut**: the walk from a green test suite to a published version — changelog entry, version bump, `hatch build` producing the wheel and sdist, publish to PyPI. I'll show the actual process I used for this release.
- **Anatomy of the package** along the way, from the [chartbook repo](https://github.com/backofficedev/chartbook): `pyproject.toml` as the single source of truth, the Hatchling build backend, dynamic versioning, and the one-line `[project.scripts]` entry (`chartbook = "chartbook.cli:main"`) that creates the CLI command.

## Agenda Item 4: `finm`, `stockbeta`, and Writing Your Own Package

With a real release fresh in mind, work through [Writing and Publishing Your Own Python Packages](./Week6/python_packaging_with_hatch.md) and the packages you can practice on:

- [`finm`](https://pypi.org/project/finm/) ([docs](https://jeremybejarano.com/finm/)) is the course's own published package: `finm.fixedincome` (the yield-curve functions you imported in HW 3), `finm.data` (Fama-French, Federal Reserve, He-Kelly-Manela, and other loaders), and `finm.analytics` (factor analysis). It's the low-stakes place to practice the contribution loop on a package you don't own: fork, branch, PR, review, merge — the same mechanics as the midterm report, now crossing a repository boundary you don't control.
- [`stockbeta`](https://github.com/finm-32800/stockbeta) is a previous year's practice package, published to [TestPyPI](https://test.pypi.org/project/stockbeta/) but not the real PyPI. TestPyPI is the rehearsal space: the full publish workflow, with none of the consequences of claiming a real package name.
- Where this is heading: next week's HW 4 (a minimal options case study) is built from the same ChartBook template as your other repos, and these packaging workflows are the muscles it exercises.

% INSTRUCTOR-ONLY (does NOT render).
% Run of show (3-hour class): 0:00 announcements (incl. pip install -U chartbook) ·
% 0:10 group 3 (10 present + 5 Q&A + 5 silent feedback) · 0:30 group 4 · 0:50
% midterm check-in + chartbook_catalog.md hands-on (clone ftsfr repos, catalog add,
% ls, data.load; live demo wiring one contribution) · 1:45 break · 1:55 release
% case study (changelog walk, semver, hatch build, PyPI publish — tell the 0.1.0
% release story) · 2:25 finm + stockbeta + hatch chapter · 2:50 wrap-up reminders
% (HW3 due Fri July 24, survey Fri 11:59 PM, July 30 consultations this week,
% HW4 next week).
% - BEFORE CLASS: migrate finm_midterm_report's chartbook.toml to format v2 — it is
%   still v1 (chartbook_format_version = "0.0.2") and chartbook 0.1.x errors on it.
%   Run: python scripts/migrate_toml_v2.py <path> (script in the chartbook repo).
% - ftsfr repos students can run (public/WRDS only, from each repo's manifest):
%   fed_yield_curve, ken_french_data_library, he_kelly_manela, nyu_call_report,
%   corp_bond_returns, crsp_treasury, wrds_crsp_compustat, wrds_bank_premium,
%   options, cds_returns, cds_bond_basis. Bloomberg-gated (excluded):
%   basis_tips_treas, basis_treas_sf, basis_treas_swap, cip, commodities,
%   foreign_exchange, sovereign_bonds.
% - HW4 launch moved to Week 7 (options case study, finm-32800/case_study_options);
%   NOT released tonight. Its notebooks' toctree entries live in overview_w7.md.
%   Post assignment link + due date on Canvas next week.
% - Data vendors (LSEG Datastream, Databento) remain in Week 7; toctree entries in
%   overview_w7.md. Bloomberg stays in the Appendix.
% - Possible finm live exercise: merge a small options utility (payoff/Black-Scholes
%   helpers) via PR that next week's HW4 notebooks could import; decide before then.
