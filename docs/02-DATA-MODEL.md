# 02 — Data Model

This document describes entities and fields conceptually. It intentionally does
not prescribe a specific database engine, ORM, or migration syntax — that is a
later-phase implementation decision that must remain consistent with the rules
below.

## Entities

### ClinicGroup
A brand or company that may operate one or more physical branches (e.g.
"Village Vets", "MyVet", "Abbeyville").

Fields (conceptual): id, name, website, normalized_name (for duplicate
detection), notes, created_at, updated_at.

### ClinicBranch
A single physical location. Always belongs to exactly one ClinicGroup (a
standalone independent clinic is a ClinicGroup with one branch).

Fields: id, clinic_group_id, branch_name, address, eircode, county, town,
phone, coordinates (lat/lng, optional), website, is_active, notes, created_at,
updated_at.

**Constraint:** a branch's identity (address/Eircode/phone/coordinates) is
never inferred from the group; it must be independently recorded.

### Service
A canonical treatment from the Service Catalogue (see
`00-MASTER-REQUIREMENTS.md` §7).

Fields: id, canonical_name, category (e.g. consultation, vaccination,
parasite, surgical, dental, diagnostic, travel), description, applies_to_pet
(Dog/Cat/Other/Any), notes.

### ServiceAlias
Maps a clinic's own wording to a canonical Service, without altering meaning.

Fields: id, service_id, alias_text, clinic_group_id (nullable — some aliases
are clinic-specific, some are general synonyms).

**Constraint:** an alias may only map to one canonical service. If a clinic's
wording genuinely doesn't match any canonical service, propose a new
ServiceVariant or, if truly novel, a new canonical Service — never force-fit
it into an unrelated one.

### ServiceVariant
A materially different version/package of a Service that must not be merged
into the base service (e.g. "Dog Spay — under 15kg" vs "Dog Spay — over
25kg"; "Flea + Tick + Worming Package" as opposed to each treatment alone).

Fields: id, service_id, variant_label, weight_range (optional), age_group
(optional), pet_type (optional), notes.

**Constraint:** variants exist to prevent false equivalence — a variant is
never silently collapsed into its base service for comparison purposes.

### Price
A single priced offering: one branch, one service (or variant), one price
point, with full sourcing.

Fields:
- clinic_branch_id
- service_id
- service_variant_id (nullable)
- pet_type (Dog / Cat / Other / Any)
- age_group (Puppy/Kitten / Adult / Senior / Not specified)
- weight_range (one of the defined kg bands, or "Other / Not applicable")
- amount (nullable — see Constraints)
- price_type (Fixed / From / Variable / Package / Plan Included / Discounted /
  Network-wide / Branch-specific)
- consultation_included (boolean / not specified)
- notes (free text — e.g. conditions, exclusions)
- source_type (Website / Supplied Directly by Clinic / User Submission /
  Clinic Submission / Other — identified)
- source_url (required when source_type = Website and a specific pricing page
  exists; forbidden/empty when source_type = Supplied Directly by Clinic,
  which instead uses source_reference)
- source_reference (free text, e.g. "Email from clinic manager, 12 March
  2026" — used when there is no URL)
- verification_date
- effective_date
- active (boolean — inactive prices are retained, not deleted, for history)

**Constraints (hard, non-negotiable):**
1. `amount` is NEVER `0` unless `price_type` reflects a genuinely, explicitly
   free service with documented evidence in `notes`. A plan-included service
   uses `price_type = "Plan Included"` and a display layer that renders
   "Included with Pet Health Plan" — never `amount = 0` with `price_type =
   "Fixed"`.
2. If `amount` is unknown/unclear, `amount` is `null` and `price_type =
   "Variable"`; the display layer renders "Contact clinic for price" or
   "Price varies — contact clinic". `null` must never be coerced to `0` by a
   default value, form placeholder, or fallback anywhere in the stack.
3. `price_type = "From"` prices always render with the word "From" attached;
   the raw number is never displayed alone.
4. `source_url` is required whenever `source_type = "Website"` and the
   clinic publishes a specific pricing page — the clinic's homepage does not
   satisfy this requirement if a more specific page exists.
5. A `Price` row is never deleted on update; see `PriceHistory` below.
6. `network_wide` prices (price_type includes "Network-wide") are explicitly
   flagged as such in the data and in the UI, and are never copied into a
   branch-specific record without an independent branch-level source.

### Plan
A membership/subscription/health plan offered by a clinic or group, entirely
separate from one-off Price records.

Fields: id, clinic_group_id or clinic_branch_id (plans may be network-wide or
branch-specific — flagged the same way as prices), plan_name, monthly_price
(or other billing cycle), source_type, source_url/source_reference,
verification_date, notes.

### PlanBenefit
One inclusion within a Plan.

Fields: id, plan_id, service_id (nullable — some benefits are discounts
rather than specific services), benefit_description, benefit_type (Included /
Discount / Unlimited / Other), discount_percentage (nullable).

**Constraint:** a PlanBenefit is never expressed as a Price with `amount = 0`.
It only ever exists as a PlanBenefit record, referenced from the branch/plan
display.

### PriceHistory
An immutable log of every change to a Price.

Fields: id, price_id, previous_amount, previous_price_type, new_amount,
new_price_type, changed_at, source_type, source_url/reference,
verification_status (Unverified / Reviewed / Verified / Rejected), notes.

### UserSubmission
A correction or new-price tip submitted by a public user (no account
required to submit).

Fields: id, submitted_at, target (existing price_id, or free-text proposal
for a new branch/service), proposed_value, contact_info (optional),
status (Pending / Approved / Rejected), reviewed_by (admin_user_id, nullable),
reviewed_at, review_notes.

### ClinicSubmission
Information supplied directly by a clinic (e.g. via email, phone, or a future
clinic portal).

Fields: id, clinic_branch_id, submitted_by (name/role if known), channel
(Email / Phone / Form / Other), submitted_at, content (structured or free
text), status (Pending / Approved / Rejected), reviewed_by, reviewed_at,
review_notes.

**Constraint:** once approved, resulting Price/Plan records carry
`source_type = "Supplied Directly by Clinic"` and display "Source: Supplied
directly by clinic" — no URL is invented for these.

### VerificationQueue
The review inbox that anything not yet safe to publish sits in: detected
website changes, UserSubmissions, ClinicSubmissions.

Fields: id, item_type (DetectedChange / UserSubmission / ClinicSubmission),
item_id, priority, status (Pending / In Review / Approved / Rejected),
assigned_to (admin_user_id, nullable), created_at, resolved_at.

### Advertisement
**[LAUNCH-OFF]** See `09-ANALYTICS-DONATIONS-ADS.md`.

Fields: id, title, target_url, image_reference, placement (Sidebar / Top
Banner / Other), active (boolean — false at launch and not exposed publicly
regardless of this flag until a separate launch decision is made), start_date,
end_date, created_by, created_at.

**Constraint:** Advertisement records are never joined into, weighted into, or
otherwise allowed to influence the query/sort logic that produces organic
Price search results. They are rendered from an entirely separate code path.

### AdminUser
Fields: id, name, email, role (e.g. Editor / Reviewer / SuperAdmin — exact
role set is a later-phase decision), created_at, last_login_at.

### Analytics
Aggregated, privacy-conscious usage data. See
`09-ANALYTICS-DONATIONS-ADS.md` for the metric list and privacy constraints.
Fields (conceptual): id, date, metric_name, metric_value, dimension (e.g.
county, service, pet_type — for "most searched X" style metrics).

### SystemIssue
Operational issues raised by monitoring, ingestion, or manual report, for
admin visibility (e.g. "source URL returned 404", "price appears to be a
scraping error", "duplicate branch suspected").

Fields: id, issue_type, severity, related_entity_type, related_entity_id,
description, status (Open / Investigating / Resolved / Ignored), created_at,
resolved_at.

## Relationships (summary)

- ClinicGroup 1—N ClinicBranch
- ClinicBranch 1—N Price
- ClinicBranch 1—N Plan (or ClinicGroup 1—N Plan, when network-wide)
- Plan 1—N PlanBenefit
- Service 1—N ServiceAlias
- Service 1—N ServiceVariant
- Price 1—N PriceHistory
- ClinicBranch / Price 1—N UserSubmission, ClinicSubmission (as targets)
- UserSubmission / ClinicSubmission / DetectedChange → VerificationQueue

## Duplicate Prevention

Clinic duplicate detection considers, in combination (no single field is
sufficient alone): normalized name, parent group, address, Eircode, phone,
website, coordinates. A likely-duplicate flag should be raised for admin
review rather than auto-merged.

**[FUTURE]** Admin-facing merge tool that combines duplicate ClinicBranch or
ClinicGroup records while preserving PriceHistory and VerificationQueue
provenance from both sides.

## Price Types (enumeration)

- **Fixed** — a specific published amount.
- **From** — a starting amount; actual price may be higher depending on
  factors like weight.
- **Variable** — amount not fixed/known; display "Price varies — contact
  clinic".
- **Package** — a bundled multi-item offering priced as one line (e.g. Flea +
  Tick + Worming Package) — never decomposed into an invented per-item price.
- **Plan Included** — the service is included in a paid plan; never has a
  numeric `amount` shown as a price.
- **Discounted** — a reduced price under a stated condition (e.g. plan member
  discount); the discount condition is stored in `notes`.
- **Network-wide** — the price applies across a clinic group's branches per
  the source; always flagged as such in the UI.
- **Branch-specific** — the price is confirmed for one branch only.

## Open Questions

- Should `ServiceVariant` support nested variants (e.g. a package that itself
  has weight-based pricing)? Deferred to implementation; the model above
  supports it via `weight_range` on the variant, but multi-dimensional
  variants (weight + age together) may need a small join table later.
- Exact `AdminUser` role/permission granularity — deferred to
  `06-ADMIN-DASHBOARD.md` and a later security design pass.
- Whether `coordinates` should be mandatory for new branches — currently
  optional to avoid blocking real data entry when only an address is known.
