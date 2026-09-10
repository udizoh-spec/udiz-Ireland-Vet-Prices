# 07 — Verification & History

## Purpose

Define how udiz proves a published price is trustworthy, and how it preserves
the record of how a price arrived at its current state.

## Verification Workflow

Applies uniformly regardless of how a candidate price entered the system
(website monitoring, user submission, or clinic submission):

```
Candidate created → VerificationQueue entry → Admin review → Approved/Rejected
   → (if Approved) Price published/updated → PriceHistory entry written
```

- A candidate is never published by the same automated process that created
  it. A human administrator makes the Approve/Reject decision.
- "Review" means checking the candidate against its stated source (the
  fetched page, the submission text, the clinic's message) — not simply
  trusting the extraction or submission at face value.
- Verification status on a Price/PriceHistory record is one of: Unverified,
  Reviewed, Verified, Rejected. Only Verified (or an equivalent explicit
  "Approved and published" state) results in something visible to the public.

## Audit Trail

Every change to a live Price or Plan/PlanBenefit must be attributable:

- **What changed** — previous value(s) vs. new value(s).
- **When** — timestamp of the change.
- **Why/how it was known** — source_type and source_url/source_reference at
  the time of the change.
- **Who approved it** — the AdminUser who moved it from candidate to live.

This trail exists primarily in `PriceHistory` (see `02-DATA-MODEL.md`), with
Plans/PlanBenefits needing an equivalent structure once built.

## Price History

- Every update to a Price's amount, price_type, or key conditions (weight,
  age, consultation_included) creates a new `PriceHistory` row rather than
  overwriting silently.
- History is retained indefinitely by default; there is no automatic
  deletion of historical records. Any future data-retention policy change
  must be a deliberate decision, not a side effect of a cleanup script.
- History should be viewable per-branch and per-service in the admin
  dashboard (see `06-ADMIN-DASHBOARD.md` §Price History).

## Source Evidence

- Website-sourced changes retain the exact URL fetched and, ideally, enough
  captured context (e.g. the raw extracted text/value) for a reviewer to
  confirm the extraction was accurate without having to re-fetch the page
  themselves later (pages change or disappear).
- Clinic-supplied changes retain the submission channel, submitter
  information (if given), and date — never a fabricated URL.
- User-submitted changes retain the submission as given, plus whatever
  independent corroboration the reviewer used to actually approve it (e.g.
  "confirmed against clinic's own price list, see source_url").

## Rollback / Review Concepts

- Because history is preserved rather than overwritten, "rollback" is
  conceptually: mark the current Price inactive/incorrect, and either
  restore a previous verified value or move the record back into the
  VerificationQueue for re-review — never a silent, untracked overwrite.
- **[FUTURE]** A dedicated one-click "revert to previous verified value" admin
  action. At minimum for launch, an admin must be able to manually re-enter
  the previous value with a note referencing why (e.g. "erroneous update on
  [date] reverted").

## Open Questions

- Whether PriceHistory should be exposed to the public (e.g. "this price
  changed from €X to €Y on [date]") as a trust-building feature — not
  required for launch, worth considering later since it reinforces
  transparency without requiring new data collection.
- Exact threshold for flagging a Price as "needs re-verification" in Data
  Quality (see `06-ADMIN-DASHBOARD.md`) — a specific number of weeks/months
  is an implementation decision, not fixed here.
