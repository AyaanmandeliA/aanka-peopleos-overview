# Data model

The main entities in PeopleOS and how they relate, in general terms. Field lists are summaries, not the full schema.

## Organisation structure

```
Organisation
 ├── Legal entities        (companies that employ and sponsor staff)
 ├── Operational entities  (how the business is run day to day)
 ├── Brands ── Locations   (a brand operates at one or more locations)
 └── Users and their access scopes
```

- **Organisation**: the top-level tenant. Almost every record belongs to one, and access is always resolved inside it.
- **Legal entity**: a registered company in the group. It is the employer and visa sponsor on paper, and it owns company renewals.
- **Operational entity**: the business unit a person actually works for. It can differ from the legal sponsor.
- **Brand** and **location**: the trading brands and the physical sites. Brands and locations are linked many-to-many.

## People

- **Employee**: the core record. Employee code, name, profession, nationality, gender, date of birth, joining date, contact details and working status (for example active, on leave, notice period, exited).
- **Employee assignment**: where a person works and who sponsors them: primary brand, primary location and sponsoring legal entity. Assignments are dated (valid from, valid to) so history is kept when someone moves. Secondary locations can be added.
- **Compensation**: salary and allowance lines with effective dates, so the current figure and its history are both available.
- **Status history** and **personnel category history**: dated changes to working status and to whether someone is a regular employee or temporary.
- **Temporary personnel engagement**: a dated engagement for someone working on a temporary or visit-visa basis, with the operational entity, the legal sponsor, the responsible manager, supporting documents and a review outcome.
- **Operational memos** and **operational flags**: short internal notes and markers attached to a person.

## Immigration and compliance

- **Immigration record**: passport, residence visa, labour card, Emirates ID and insurance details with their expiry dates. Records are dated so the current one is clear and earlier ones are kept.
- **Compliance type** and **compliance record**: other tracked items per person (type, reference, issue and expiry dates, issuing authority). A record can supersede an earlier one. Applicability rules decide which compliance types apply to whom.
- **Visa process** and **visa process steps**: a visa application or renewal as a workflow with ordered steps and a target completion date.
- **Exit process**: the steps when someone leaves (last working day, labour and immigration cancellation, passport release, final settlement, insurance cancellation).

## Documents

- **Employee document**: a file linked to a person, with document type, title, reference, issue and expiry dates, where it is stored (private storage or Google Drive) and a verification status.
- **Employee Drive folder** and **Drive match review**: the link between a person and their existing Drive folder, and the review queue for files that could not be matched automatically.
- **Document sync items, events, cursors, conflicts and outbox**: the bookkeeping that keeps Drive and PeopleOS in step and records each change.
- **Document template** and **template settings**: approved letter templates and their versions.
- **Generated document**: a letter produced from a template for a person. It records the template and version used, the legal entity, brand and location it was issued under, where each filled-in value came from, and its lifecycle (draft, reviewed, issued, superseded or cancelled) with who reviewed and issued it.

## Work queues and data quality

- **Data quality issue** and **data resolution issue**: problems found in the records (missing document, unlinked file, missing information, conflict), each with a priority, an owner, a due date and a status (open, in progress, pending approval, resolved, rejected, reopened).
- **Data resolution events**: the history of each issue.
- **Issue ownership defaults**: which team picks up which kind of issue.

The Action Centre queue itself is computed from the records above (expiry dates, open processes, data issues) rather than stored.

## Company renewals and notifications

- **Company renewal**: a company-level renewal such as a trade licence or establishment card, linked to a legal entity, with issue and expiry dates, reminder lead times, a workflow status (upcoming, action required, in progress, submitted, renewed, overdue, not applicable), an attachment and version history.
- **Notification preferences**, **digest routes by legal entity** and **digest delivery history**: who receives the daily digest, for which companies, and a record of what was sent.

## Attendance

- **Attendance import batch** and **import issues**: each uploaded attendance file and the problems found in it.
- **Attendance employee mappings** and **location aliases**: how names and site labels in the source file map to people and locations.
- **Attendance daily locations**: the resulting per-person, per-day record.

## Assets

- **Asset** and **asset category**: items in the group asset register.
- **Asset assignment** and **asset transfer**: who holds an item and how it moved between people or departments, including custody acknowledgement.
- **Asset incident**, **asset maintenance** and **asset documents**: what happened to an item and its supporting files.
- **Asset import batches and rows**: validated bulk imports.

## Discipline

- **Disciplinary action**: a warning or fine for a person, with category, reason, amount and currency for fines, a status, and links to the related document or generated letter.

## Designer workspace

- **Projects**, **project participants**, **time entries** and **time entry history**, plus workspace access: the timesheet workspace used by the design business.

## Access and audit

- **App user**: a person who can sign in, with their role.
- **User capabilities**: individual capability grants on top of the role preset.
- **User access by legal entity, brand and location**, plus a separate **compensation access by legal entity**: the user's scope.
- **Audit event**: who changed what, on which record, when, with the changed fields.
- **Security event**: security-related events, kept apart from the general audit trail.
- **Import batch**: the record of each bulk import and its provenance.

## Conventions

- Tables use generated identifiers and record when rows were created, and in most cases when they were last updated.
- Records that change over time are dated (valid from, valid to, current flag) or versioned (supersedes an earlier record) rather than overwritten.
- Operational records carry a data scope that separates real data from synthetic test data.
