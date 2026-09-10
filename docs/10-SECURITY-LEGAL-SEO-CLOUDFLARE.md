# 10 — Security, Legal, SEO & Cloudflare Readiness

## Security

### Secrets
- No secrets (API keys, admin credentials, PayPal details, database
  connection strings) are ever committed to the repository.
- Environment-specific configuration is kept out of version control via the
  standard mechanism for whatever platform is chosen (e.g. environment
  variables / secrets manager) — the specific tool is a later-phase decision,
  but "never in git" is a Phase 0 requirement on the repository itself.

### Access Control
- The admin dashboard requires authentication; it is not reachable by an
  unauthenticated user in any way that allows viewing or changing
  unpublished/candidate data.
- Every admin action that changes public-facing data is attributable to a
  specific `AdminUser` (see `02-DATA-MODEL.md`, `07-VERIFICATION-AND-HISTORY.md`).

### Abuse Protection
- Public submission endpoints (User Submissions, Contact) should have
  reasonable abuse protection (e.g. rate limiting, basic spam/bot mitigation)
  proportionate to a low-traffic launch — exact mechanism is an
  implementation decision, not fixed here.
- Monitoring/scraping abuse protection is covered in
  `04-WEEKLY-MONITORING.md` §Politeness Requirements (this is about udiz not
  abusing clinic sites, which is equally a security/legal concern).

## Privacy-Conscious Analytics

- Restated from `09-ANALYTICS-DONATIONS-ADS.md`: analytics stay aggregate
  (counts, top-N by dimension) rather than tracking identifiable individuals
  across sessions beyond what's needed for the stated metrics.
- A Privacy Policy (see Legal below) must accurately describe what is
  actually collected — the policy is written to match the real
  implementation, not a generic template that overstates or understates data
  collection.

## Legal

- The site is informational and comparative only; it does not promise
  accuracy, completeness, availability, or quality of any listed price or
  clinic.
- Users are responsible for verifying current prices, availability, terms,
  suitability, and treatment details directly with the clinic before
  booking.
- Legal copy (Disclaimer, Terms, Privacy) should be written cautiously and
  **reviewed by an Irish solicitor before launch** — this document set does
  not substitute for that review.
- Legal copy must avoid an exaggerated "we accept no responsibility for
  anything" tone; it should be honest and proportionate about what udiz is
  (a comparison tool) and isn't (a guarantee of any kind).
- Governing law: Ireland (to be confirmed by solicitor review).

## SEO

- **[SHOULD]** Individual branch pages and search result pages should be
  crafted to be indexable and useful as standalone pages (clear titles,
  meta descriptions, structured data where appropriate) since "compare vet
  prices in [county]" style queries are a plausible discovery path.
- Content must remain accurate to the underlying data — SEO copy must not
  overstate coverage (e.g. must not claim national coverage of a service
  before real data supports it).
- URLs for filtered search states should ideally be shareable/bookmarkable
  (see Open Question in `05-SEARCH-AND-FILTERS.md`), which also benefits SEO.

## Accessibility

- **[SHOULD]** Reasonable baseline accessibility: sufficient colour contrast
  within the calm/muted palette described in `01-PRODUCT-SPEC.md`, keyboard
  navigability, semantic HTML/ARIA labelling for interactive filter controls,
  and alt text for any meaningful images.
- Accessibility is not deferred to "later" as a nice-to-have retrofit; it
  should be considered during frontend build, not bolted on afterward.

## Cloudflare Readiness

- The project's stated deployment target is Cloudflare. This document does
  not mandate specific Cloudflare products, but notes the following as
  relevant once building begins:
  - Scheduled jobs (weekly monitoring) are a natural fit for a Cloudflare
    Worker with a cron trigger, as referenced in `04-WEEKLY-MONITORING.md`.
  - Static/frontend assets and API routes should be structured in a way
    that doesn't assume a specific non-Cloudflare-compatible runtime
    feature.
- No deployment is performed in Phase 0. This section exists so later
  phases don't have to retrofit Cloudflare-compatibility decisions.

## Repository Hygiene (GitHub-suitability)

- `.gitignore` should exclude local env files, dependency directories, and
  any build artefacts once those exist (not applicable yet in Phase 0, but
  noted for the first code-bearing phase).
- Commit history and `.git` metadata are preserved through all phases,
  including this Phase 0 cleanup — nothing in this document set should be
  read as licence to rewrite or delete history.

## Open Questions

- Specific solicitor engagement/timing for legal copy review — outside
  udiz's technical scope, but flagged here as a hard prerequisite before
  public launch of the Disclaimer/Terms/Privacy pages.
- Specific Cloudflare products to use (Workers, Pages, D1, KV, R2, etc.) —
  deferred to the phase that actually builds the backend/frontend.
- Formal accessibility conformance target (e.g. WCAG 2.1 AA) — recommended
  but not formally adopted as a numbered requirement in this document set
  yet.
