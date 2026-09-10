# 03 — Data Ingestion

## Purpose

Define how real price data enters the system, from any source, without ever
allowing fabricated, unverified, or fake data to reach the public site.

## Source Types

1. **Official clinic website** — a clinic's own published price list or
   pricing page.
2. **Clinic supplied directly** — information given to udiz by the clinic
   itself (email, phone call, form, future clinic portal), where no public URL
   exists for that information.
3. **User submission** — a member of the public reports a price or correction.
4. **Other explicitly identified source** — anything not covered above must
   be named and described in `source_reference`; "unknown" is not an
   acceptable source.

## Ingestion Paths

### A. Official website sourcing
1. A known pricing URL is identified and recorded (see
   `04-WEEKLY-MONITORING.md` for the monitoring side of this).
2. Data extracted from the page is captured as a **candidate** Price/Plan
   record, never written directly to the live/public Price table.
3. The candidate stores: exact source URL (the specific pricing page, not the
   homepage, when one exists), the raw extracted text/value, and a captured
   timestamp.
4. The candidate enters the `VerificationQueue` for human review before
   becoming a live Price.

### B. Clinic-supplied data
1. Recorded as a `ClinicSubmission`, with channel (email/phone/form/other),
   submitted-by, and date.
2. On admin approval, becomes a live Price/Plan with `source_type = "Supplied
   directly by clinic"` and `source_reference` describing the submission (no
   URL is invented).

### C. User-supplied data
1. Recorded as a `UserSubmission`, referencing either an existing Price (as a
   proposed correction) or a free-text proposal for a new branch/service.
2. Never published without admin review. A user submission alone is never
   sufficient evidence to change a live price.

## Validation Rules (apply to every path)

- **No €0 without explicit, documented evidence of genuine free pricing.**
  A validation step must reject any candidate with `amount = 0` unless a
  reviewer explicitly confirms it and records the evidence in `notes`.
- **No missing-price defaults to €0.** Unknown/blank pricing must map to
  `amount = null`, `price_type = "Variable"` — never to zero.
- **Source URL required for website-sourced candidates** when a specific
  pricing page exists on the clinic's site. A generic homepage URL is
  rejected if a more specific page is available.
- **Normalization, not alteration of meaning.** Wording differences ("Neuter"
  vs "Castration/Spay", "Booster" vs "Annual Vaccination") are mapped via
  `ServiceAlias` to a canonical `Service`, but the underlying price, weight
  condition, and any plan/discount context must be preserved exactly as
  sourced — normalization must never quietly change what was actually
  offered.
- **Materially different variants are not merged.** If weight, age, or
  package composition differs in a way that changes the price or scope, the
  candidate becomes (or maps to) a distinct `ServiceVariant`, not a rounded-off
  match to an existing one.
- **Duplicate checking before creation.** New ClinicBranch/ClinicGroup
  candidates are checked against the duplicate-detection fields in
  `02-DATA-MODEL.md` before being created.
- **No fabrication to fill gaps.** If a field (e.g. weight range, age group,
  Eircode) is not stated by the source, it is left blank/"Not specified" —
  never inferred or guessed and presented as fact.

## What Ingestion Explicitly Does Not Do At Launch

- It does not automatically publish anything. Every candidate, regardless of
  source, passes through `VerificationQueue` review before becoming a live,
  public Price/Plan.
- It does not attempt open-ended discovery of new clinics/pages (see
  `04-WEEKLY-MONITORING.md` §Future Discovery) — Phase 0/launch scope is
  monitoring a known, curated list of URLs.
- It does not scrape or store data from Northern Ireland clinics.
- It does not translate or alter currency — EUR only.

## Known Source References (for future ingestion — not data to copy in now)

See `00-MASTER-REQUIREMENTS.md` §23 for the current list of identified
official pricing URLs (Village Vets, MyVet, Urban Vets, Dublin Bay Vets,
Donnybrook Vets, Marina Vets, Value Vets, Abbeyville). These are starting
points for a future, properly reviewed ingestion pass — they must not be used
to fabricate current price records.

## Open Questions

- Exact technical extraction method (HTML parsing rules, PDF price lists,
  etc.) per source — to be documented per-clinic once ingestion is actually
  built, since each clinic's page structure differs.
- Whether a lightweight "quick clinic add" path (name + address + one known
  price, fully sourced) is needed before full ingestion tooling exists, to
  seed the initial ~40 branches mentioned in the master requirements.
