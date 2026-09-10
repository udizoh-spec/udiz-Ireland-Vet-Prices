# 06 — Admin Dashboard

## Purpose

Give administrators the tools to enter, review, verify, and maintain data
quality — this is the only place data moves from "candidate" to "published".
Not public; requires authentication.

## Sections

### Clinics / Groups / Branches
- Create/edit ClinicGroup and ClinicBranch records.
- Surface likely-duplicate warnings (per `02-DATA-MODEL.md` duplicate fields)
  before allowing a new branch/group to be saved.
- View a branch's full price list, plan, and submission history in one place.

### Services / Aliases / Variants
- Manage the canonical Service Catalogue (additions should be rare and
  deliberate, since the catalogue drives comparison consistency).
- Manage ServiceAlias mappings (clinic wording → canonical service).
- Manage ServiceVariant records for materially different packages/weight
  tiers, with a clear UI distinction from a plain alias.

### Prices
- Create/edit individual Price records.
- The entry form must make `amount = null` / `price_type = Variable` at least
  as easy to select as entering a number — "I don't know the price" must not
  require a workaround like typing 0.
- The form must require `source_url` when `source_type = Website` and reject
  saving without it if a specific pricing page is claimed to exist.
- Saving a change to an existing Price automatically writes the prior value
  to `PriceHistory` — this is not an optional step an admin can skip.

### Plans & Memberships
- Create/edit Plan and PlanBenefit records, kept structurally separate from
  Prices (see `02-DATA-MODEL.md`).
- UI should make it obvious that a "benefit" is not a "price" — e.g. distinct
  section, distinct field set, no `amount` field on a PlanBenefit that maps
  to a service inclusion.

### Price History
- Read-only, chronological view per Price: previous/new amount, price type,
  date, source, verification status, notes.
- Supports understanding *why* a number changed, for both internal QA and,
  potentially, a future public "last changed" indicator.

### User Submissions
- Queue of public corrections/tips, each linkable to the Price or
  branch/service it concerns.
- Approve → applies the change (writing PriceHistory) and marks the
  submission Approved.
- Reject → marks Rejected with a reason, does not touch live data.
- A submission alone (no independent source corroboration) is a signal for
  review, not automatic grounds for publishing — the reviewer's judgement,
  informed by the clinic's own site or a direct check, is what actually
  publishes it.

### Clinic Submissions
- Queue of information supplied directly by a clinic (email, phone, form).
- Approve → creates/updates Price/Plan with `source_type = "Supplied
  directly by clinic"`, no invented URL.
- Same Approve/Reject flow and audit trail as User Submissions.

### Verification Queue
- Unified inbox across DetectedChange (from monitoring), UserSubmission, and
  ClinicSubmission — so nothing waits in three different places.
- Supports assignment, priority, and status (Pending / In Review / Approved /
  Rejected).

### Analytics
- Internal view of the metrics described in `09-ANALYTICS-DONATIONS-ADS.md`
  (visitors, sessions, searches, most-searched county/pet/service,
  submission counts, system issues) — the same data that feeds the daily
  email, viewable on demand.

### Advertisements
**[LAUNCH-OFF]** CRUD for Advertisement records (title, target URL, image
reference, placement, active flag, date range) — see
`09-ANALYTICS-DONATIONS-ADS.md` for the constraint that this section must
never be wired into organic ranking logic. At launch, this section exists
but the "active" capability is switched off end-to-end (not just hidden in
the UI).

### Data Quality
- Automated flags, for example:
  - Missing `source_url` where `source_type = Website` and a specific page is
    claimed.
  - `amount = 0` on a non-genuinely-free record (should be prevented at entry,
    but this is the safety net if it ever occurs).
  - Prices not re-verified within an expected window (e.g. verification_date
    older than a configurable threshold).
  - Likely duplicate clinics/branches.
  - Repeated monitoring failures for a given source URL.
- Data Quality is a dashboard, not a silent auto-corrector — flags are
  surfaced for a human to resolve.

## Permissions

- **[Open Question]** Exact role granularity (e.g. Reviewer who can
  approve/reject vs. Editor who can create records vs. SuperAdmin who manages
  users/settings) is not finalised in Phase 0. At minimum, every admin action
  that changes public data must be attributable to an `AdminUser` for audit
  purposes.

## Moderation Principles

- Nothing a public user or a clinic submits reaches the live site without an
  identified admin approving it.
- Rejections should be able to carry a reason, both for internal record and
  potential future communication back to the submitter.
- Admin actions that touch price/plan data are logged (who, what, when) —
  this is the same mechanism as `PriceHistory` for prices, and should be
  extended equivalently for Plans/PlanBenefits.

## Open Questions

- Whether admins need bulk-edit tools (e.g. updating many branches' plan
  wording at once) — deferred until real data volume makes this necessary.
- Notification mechanism for new submissions (email vs. in-dashboard only) —
  deferred to implementation.
