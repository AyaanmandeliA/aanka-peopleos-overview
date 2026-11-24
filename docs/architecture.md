# Architecture

This is a general description of how PeopleOS is put together. It describes the design and the principles behind it, not the configuration of the live system.

## Overview

```
Browser (React UI)
      |
      v
Server routes (same application)  --->  Email (invitations, digests)
      |                          --->  Google Drive (staff document folders)
      v
Postgres with row-level security  +  private file storage
```

PeopleOS is a single web application. The user interface and the server routes live in one codebase and are deployed together. The server routes are the only way the browser reaches data: the page never talks to the database or the file store directly.

## Front end

The interface is a React application built on the Next.js App Router model, compiled with Vite through vinext. Components come from Radix and shadcn, styled with Tailwind CSS. It is designed as a dense admin workspace: a fixed side navigation with one section per module, filter bars that persist in the address bar so a filtered view can be reopened, and employee profiles split into tabs.

The application also has a demo mode that runs entirely on synthetic fixtures. It is used for development, automated checks and walkthroughs, so real records are never needed to show or test the interface.

## Server layer

Server routes handle reads and writes. A typical route that changes data:

1. identifies the signed-in user and loads their role, capabilities and scope,
2. checks that the action is allowed for that user and for the records involved,
3. validates the input before it reaches the data layer,
4. performs the change and records it in the audit trail.

Data access is wrapped in a repository layer, so the same operations can run against the real database or the synthetic demo data.

## Database and row-level security

The data lives in Postgres (Supabase). Row-level security is enabled on the application's tables, with policies based on the user's organisation and access scope. Access rules are covered by permission and tenant-isolation tests.

Schema changes are made through versioned migration files kept in the repository.

## Roles and capabilities

Access is built from two layers:

- Roles: owner, admin, HR, manager and viewer. A role is a starting preset.
- Capabilities: fine-grained permissions such as viewing employees, viewing or editing compensation, viewing sensitive immigration data, downloading documents, generating or issuing HR letters, creating warnings or fines, importing attendance, managing company renewals, managing assets, exporting data and viewing the audit trail.

A user's effective access is their role's preset plus or minus individual capabilities. Capabilities are grouped as sensitive, operational and system access, which makes reviews easier. A small set of governance capabilities, such as managing users and roles, is reserved for owners.

## Scoping by legal entity, brand and location

The group is made up of several legal entities (the companies that employ and sponsor staff), operational entities, brands and locations. These are independent dimensions: a person can work at a location of one brand while being sponsored by a different legal entity.

Each user can be given access to all records or to a restricted scope made of specific legal entities, brands and locations. Compensation has its own separate scope by legal entity, so someone can manage records for a business without seeing salaries for it. Company renewals and the daily digest are routed by legal entity in the same way.

## Sign-in and two-factor authentication

Accounts are created by invitation only; there is no public sign-up. Two-factor sign-in with an authenticator app (TOTP) is mandatory for access to workforce records, and sensitive actions ask for a fresh two-factor check. Owners can revoke a user's sessions and reset their two-factor enrolment.

## Audit logging

Changes to employees, assignments, compensation, immigration and compliance records, visa and exit processes, documents, generated letters, company renewals, assets, disciplinary actions, data fixes and settings are recorded as audit events: who did it, what changed, which record, and when. Security-relevant events are recorded separately. Audit history can be reviewed and exported by users with the right capability.

## Documents and storage

Employee documents and generated letters are stored privately and are not published as public links. Files are returned through authorised server routes that check the user's capabilities and scope for that employee. Uploads are only accepted in an allowed set of file types.

Many staff documents already lived in Google Drive. PeopleOS keeps references to those files, links them to the right employee and document type, and tracks synchronisation between Drive and the system with conflict handling and a reviewer step.

## Generated HR documents

HR letters are produced from approved Word templates. The system fills each template from the employee's record and records where every field value came from. A generated letter moves through draft, reviewed and issued, and can later be superseded or cancelled. Issuing letters is a separate capability from generating them.

## Scheduled and background jobs

Some work runs on a schedule or in the background rather than on a click:

- A scheduled daily digest email lists upcoming and overdue company renewals. It goes only to recipients who have been approved for it, covers only the legal entities they are responsible for, and is safe to re-run without sending duplicates.
- Document synchronisation is designed as a background worker with a queue and a record of each run.
- The backup design refreshes a spreadsheet export after data changes and keeps dated daily snapshots, and a restore can be previewed and checked before it is used.

## Imports

Bulk data (employee lists, attendance exports, assets, company renewals) goes through a validation step before it is written. For employee imports:

1. Rows are normalised (names, dates, statuses, brand, location and legal entity matched against known references and aliases).
2. Each row gets a list of issues with a severity: info, warning, error or critical.
3. Each row is classed from its issues: ready, ready with warnings, review required, or blocked (for example a possible duplicate identity, a missing critical identity field, or a legal sponsor that cannot be resolved).
4. Only approved rows are imported, in a recorded batch, and the result is reconciled against the source.

Attendance spreadsheets get a preview of the column and location mapping before anything is saved.
