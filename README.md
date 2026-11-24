# AANKA PeopleOS

PeopleOS is the internal workforce system for AANKA Group, a family business in Dubai with several brands and companies. This repository is an overview of the system. It contains documentation only.

## Status

In production, used by the group's admin team. The source repository is private because it is an internal HR system holding real staff records.

The sign-in page is at https://people.aankagroup.com. Access is by invitation only.

## The problem it replaced

Before PeopleOS, staff information lived in many places at once: employee spreadsheets kept per business, passport and visa scans in shared Drive folders, and follow-ups agreed in chat. There was no single view of who works where, under which company's sponsorship, and which documents were about to expire.

PeopleOS puts the records, the documents and the follow-up work in one place, with access limited by role and by company.

## What it does

These are the modules in the application's main navigation.

| Module | What it is for |
|---|---|
| Overview | Dashboard of the workforce by status, with drill-downs into the records behind each figure. |
| People | The employee directory and each person's profile: employment, work locations, salary, immigration, documents, assigned assets, compliance, visa process, exit process and warnings. Filters by operational entity, brand, location, legal sponsor, personnel type and working status. |
| Temporary Personnel | People working on a temporary or visit-visa basis, tracked as engagements with start and end dates, a sponsoring entity, a responsible manager and a review step. Kept separate from regular employees. |
| Attendance | Attendance imported from spreadsheet exports, with a mapping preview so columns and locations are matched to employees before anything is saved. |
| Designers workspace | A scoped workspace for the design business: projects, participants and timesheets. |
| Visa & Compliance | Passport, residence visa, labour card, Emirates ID and insurance dates, compliance records, and visa processes with their steps. |
| Documents | Employee documents (passport copies, visas, contracts and so on) with type, dates and verification status. |
| Data Centre | A resolution queue for incomplete or conflicting records (missing documents, unlinked files, missing information) and a data-quality view. Fixes can require approval. |
| Action Centre | A generated work queue of things that need attention, such as documents nearing expiry, visa blockers, exits in progress and data problems, ranked by urgency and assigned to the right team. |
| Company Renewals | Company-level renewals such as trade licences and establishment cards, with a workflow status, reminders, attachments and version history. A daily digest emails approved recipients about what is due for the companies they cover. |
| Assets | A group asset register: categories, assignment to people, transfers, custody acknowledgement, incidents, maintenance and bulk import. |
| Warnings & Fines | Disciplinary warnings and fines with a review step and links to the related letters. |
| Generated Documents | HR letters produced from approved templates (offer letters, salary and employment certificates, experience certificates, transfer, warning and termination letters). Each one goes through draft, review and issue. |
| Organisation | The structure the rest of the system hangs off: legal entities, operational entities, brands and locations. |
| Settings | Users, roles and capabilities, access scopes, notification preferences, document templates and security events. |

## Stack

- Front end: React 19 with the Next.js App Router model, built with Vite through vinext. UI components from shadcn and Radix, styled with Tailwind CSS 4.
- Server: server routes in the same app, run on a Cloudflare Workers-compatible runtime (Wrangler is used for local runs).
- Database and auth: Supabase (Postgres with row-level security, Supabase Auth with TOTP two-factor sign-in, and private storage).
- Documents: Google Drive integration for existing staff folders; Word (.docx) generation for HR letters; spreadsheet (.xlsx) reading for imports.
- Language and tooling: TypeScript, ESLint, Node's built-in test runner.

## How it was built

I set the requirements and check the results. AI tools write most of the code.

In practice that means I write what a release has to do and what "done" looks like, the AI tools write the code, tests and migrations, and releases are checked against a written acceptance list in a staging environment before they reach production. Releases are recorded in written reports in the private repository.

## More detail

- [Architecture](docs/architecture.md)
- [Data model](docs/data-model.md)
- [Testing and release](docs/testing-and-release.md)
- [Development history](docs/development-history.md)

Author: Ayaan Mandelia
