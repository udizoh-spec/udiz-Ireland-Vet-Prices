# udiz — Ireland Vet Prices

Compare veterinary treatment prices from clinics across the Republic of Ireland.

## Project status: PHASE 0 — Documentation Only

This repository currently contains **no application code**. It contains only the
planning and specification documents needed to build the product safely in later
phases.

**Do not** expect a working frontend, backend, database, scraper, or deployment
at this stage. None of that exists yet, on purpose.

### What Phase 0 is

Phase 0 is a deliberate clean restart. Its only output is the `docs/` folder: a
consolidated, internally-consistent set of requirements and specifications that
every later phase must be built against.

### What Phase 0 is not

- Not a frontend build
- Not a backend or database implementation
- Not a scraper or monitoring job
- Not an admin dashboard
- Not a Cloudflare deployment
- Not a payments/donations integration
- Not real or sample clinic/price data of any kind

### Where to start

Read the docs in this order:

| Order | Document | Purpose |
|---|---|---|
| 1 | `docs/00-MASTER-REQUIREMENTS.md` | Consolidated source of truth (MUST / SHOULD / FUTURE / LAUNCH-OFF) |
| 2 | `docs/01-PRODUCT-SPEC.md` | Product goals, users, pages, UX, non-goals |
| 3 | `docs/02-DATA-MODEL.md` | Entities, relationships, fields, constraints |
| 4 | `docs/03-DATA-INGESTION.md` | How real price data is sourced and validated |
| 5 | `docs/04-WEEKLY-MONITORING.md` | Weekly monitoring & change-detection architecture |
| 6 | `docs/05-SEARCH-AND-FILTERS.md` | Search, filters, comparison result rules |
| 7 | `docs/06-ADMIN-DASHBOARD.md` | Admin sections, permissions, moderation |
| 8 | `docs/07-VERIFICATION-AND-HISTORY.md` | Verification workflow, audit trail, price history |
| 9 | `docs/08-SUBMISSIONS-AND-CLINIC-CLAIMS.md` | User/clinic submissions, future clinic claim (off at launch) |
| 10 | `docs/09-ANALYTICS-DONATIONS-ADS.md` | Analytics, donations, future advertising (off at launch) |
| 11 | `docs/10-SECURITY-LEGAL-SEO-CLOUDFLARE.md` | Security, legal, SEO, accessibility, Cloudflare readiness |
| — | `docs/TRACEABILITY.md` | Requirement → document matrix |

### Non-negotiable product rules (see `00-MASTER-REQUIREMENTS.md` for the full list)

- Republic of Ireland only. Northern Ireland is explicitly out of scope.
- Real data only. No fake, demo, placeholder, or invented clinic/price data — ever.
- No "Compare Total Cost" feature. Multiple selected services are always compared
  separately; totals are never calculated or displayed.
- A price of **€0** must never be shown unless a treatment is genuinely, explicitly
  free with no conditions. Plan-included services show "Included with Pet Health
  Plan"; missing prices show "Contact clinic for price" or "Price varies — contact
  clinic".
- Every website-sourced price stores and displays a direct source URL to the exact
  pricing page — not just a homepage link.
- Advertisements and any future clinic-claim feature are architected for, but
  switched **off** at launch, and must never influence organic search ranking.

### Contributing in later phases

Later phases (data ingestion, backend, frontend, admin, deployment) must be built
against the documents in `docs/`, not against assumptions. If a document is silent
or ambiguous on a point, that ambiguity should be raised and resolved in the docs
before code is written — see the "Open Questions" sections inside each document.
