# Testing and release

How a change to PeopleOS gets from an idea to production. This is a general description of the process.

## Releases are written down first

Work is organised into numbered versions (for example V0.15.1 or V0.17.2). Before a version is built I write what it has to do and how it will be accepted. After it is built, the release gets a written report: what changed, what was checked, and what was accepted. Smaller follow-up changes go out as point releases with their own reports.

## Automated tests

The application has several kinds of automated tests, run with Node's built-in test runner:

- **Logic tests** for the rules the business depends on: the action queue and its urgency levels, import validation and row classification, attendance spreadsheet parsing, data completion, HR document generation, email templates and the operations layer.
- **Permission and isolation tests** that check what each role and capability can and cannot see or do, and that one tenant or scope cannot read another's records.
- **Release tests** for each version, which check the behaviour that release introduced so later work does not undo it.
- **Database tests** that apply the full set of migrations to an in-process Postgres (PGlite) and run the application's routes against the resulting schema. These also check that migrations apply in order on a fresh database and on an upgraded one, and that the previous version of the application still works against the new schema.
- **SQL test scripts** for staging, which exercise database changes against the staging database inside a transaction that is rolled back afterwards.

The application has a demo mode backed by synthetic fixtures. The automated test suite and the CI checks run on synthetic data, not staff records.

## Continuous integration

Pull requests run the same checks every time: type checking, lint, the test suite, a production build, and a dependency audit. The checks run without any secrets and never touch a database or deploy anything.

## Database migrations

All schema changes are versioned migration files in the repository, named by date and release so their order is fixed. Schema changes go through these files rather than being made by hand. Before a migration is applied, its effect is checked against the database tests above, and a preflight compares the planned migrations with what has already been applied.

## Staging before production

There are separate staging and production environments with separate databases. A release goes to staging first. Staging has its own test users, provisioned by script, and a verification script that walks through the release's checks. Imports of real data are rehearsed in staging as a dry run before they are run in production.

## Acceptance

A release is accepted against its written checklist: each feature is exercised in staging, permissions are checked with users of different roles, and the result is recorded in the release report. Only then is it deployed to production, where a smaller set of checks confirms the deployment.

## Data imports

Bulk imports follow their own release discipline. The source file is validated and every row is classed (ready, ready with warnings, review required or blocked) before anything is written. Only approved rows are imported, in a recorded batch, and the result is reconciled against the source. Rows that need a decision go to a person, not to a default.
