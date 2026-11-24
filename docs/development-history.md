# Development history

Statistics computed from the private repository with git, as of 18 November 2026 (the date of the most recent commit on any branch).

## Repository statistics

| Measure | Value |
|---|---|
| Commits across all branches | 302 |
| Commits on the main branch | 114 |
| First commit | 29 September 2026 |
| Most recent commit | 18 November 2026 |
| Branches (including main) | 33 |
| Database migration files on main | 31 |
| Distinct database migration files across all branches | 63 |
| Test files on main | 30 |
| Distinct test files across all branches | 101 |

Commits by month: 27 in September (29 and 30 September only), 118 in October, 157 in November.

How these were counted:

- Commits: `git rev-list --all --count`, and `git rev-list --count` on the main branch.
- First and last commit: `git log --all` sorted by author date.
- Branches: remote branches, excluding the HEAD pointer.
- Migration files: `.sql` files in the migrations folder, on main and as distinct paths across every branch.
- Test files: files named `*.test.*` (TypeScript and JavaScript), on main and as distinct paths across every branch. SQL test scripts are not included in this count.

## What was built, by version

The summary below is drawn from commit messages and release-report titles. Version numbers are the project's own.

### Late September: foundation and first production release (V1 to V0.9.1)

- The first version of the application shell and workspace layout.
- Data readiness work, then the HR Document Studio for generating letters from templates (V0.3).
- The operational backend on Postgres and the Action Centre connected to real operational records (V0.4).
- Staging integration and verification (V0.5).
- Google Drive integration: an audit of existing staff folders and a metadata-only dry run of matching files to people (V0.6).
- The first real workforce import into staging, with every row validated and classed before import (V0.7).
- Operational completion: per-employee Drive folders, bulk location fixes, generated document lifecycle, organisation currency settings (V0.8).
- Production readiness: production acceptance checks, mandatory two-factor sign-in on the hosted site, password reset, and validation of the empty production environment before any data was loaded (V0.9 and V0.9.1).

### October: production use and new modules (V0.10 to V0.18.x)

- Workforce and attendance in production, with import provenance (V0.10).
- Access hierarchy and controlled documents, including disciplinary documents (V0.11).
- Operational readiness and onboarding of the first real users (V0.12 and V0.12.1).
- Final data, document and attendance reconciliation, and a document catalogue (V0.13).
- Payroll access scoped by legal sponsor, the Temporary Personnel workspace, and changes from real-user feedback such as dashboard drill-downs and persistent filters (V0.14).
- Invitation reliability (V0.14.2).
- Company Renewals: activation, a controlled import, and a daily digest routed by legal entity (V0.15.1 to V0.15.5).
- A redesigned overview dashboard (V0.15.6).
- The Data Centre resolution queue and the designer workspace (V0.16).
- The group asset register, then asset custody and operational memos (V0.17 to V0.17.2).
- Unified operations: Drive document sync, backups and designer timesheets (V0.18 to V0.18.3).

### November: engineering quality and review branches

Work in November was done on separate branches for review before merging. It included:

- Continuous integration checks on every pull request (type checking, lint, tests, build, dependency audit).
- Database tests that apply every migration to an in-process Postgres and run the application's routes against the result.
- Two-way Google Drive synchronisation with a reviewer step for incoming files.
- A staging harness for repeatable release checks.
