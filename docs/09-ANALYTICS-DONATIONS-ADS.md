# 09 — Analytics, Donations & Advertising

## Analytics

### Purpose
Understand usage well enough to prioritise data-quality work (e.g. which
counties/services get searched most with the least data) — without profiling
individual users.

### Suggested Metrics
- Unique visitors / users
- Sessions
- Page views
- Price searches performed
- Clinic (branch) page views
- Most searched county
- Most searched pet type
- Most searched service
- User submission count
- Clinic submission count
- System issues (from monitoring/ingestion) raised in the period

### Daily Summary Email
- **[SHOULD]** A daily usage summary email is sent to `udizoh@gmail.com`,
  covering the metrics above for the prior day.
- Exact template/format is an implementation detail; content should stay
  aggregate (counts and top-N lists), not individual-level.

### Privacy Constraints
- Analytics must be privacy-conscious: aggregate counts and non-identifying
  dimensions (county searched, service searched) rather than tracking
  individuals across sessions in a way that isn't necessary for the stated
  metrics.
- No analytics data is sold or shared with third parties beyond what is
  strictly needed to operate the metric pipeline itself (e.g. a hosting
  provider's built-in request logs).
- See `10-SECURITY-LEGAL-SEO-CLOUDFLARE.md` for the broader privacy/legal
  framing (this doc defines *what* is measured; that one covers *how* it's
  handled responsibly and lawfully).

## Donations

- A permanent, optional donation option (PayPal: `https://paypal.me/udizoh`)
  appears on every public page (header and/or footer, per
  `01-PRODUCT-SPEC.md` navigation spec).
- Donating is entirely optional and never gates any feature.
- **Hard constraint: donations never affect rankings, search results, price
  comparisons, or clinic visibility.** There is no code path where a
  donation — from a user or, hypothetically, a clinic — changes what is
  displayed or how it's ordered.
- udiz does not store PayPal credentials or any payment secrets. Payment
  processing is entirely PayPal's responsibility; udiz only links out to it.

## Advertising — LAUNCH-OFF

### Status at launch
Ads are architecturally supported but **off**. No advertisement is visible
to the public at launch, regardless of whether any Advertisement records
exist in the admin dashboard.

### Requirements for when advertising is switched on (future)
- Fully admin-controlled: creating, editing, activating, and scheduling ads
  happens only through the Advertisements section of the admin dashboard
  (see `06-ADMIN-DASHBOARD.md`).
- Every ad is clearly labelled **"Advertisement"** or **"Sponsored"** —
  never blended into organic result styling without a label.
- Ads render from placement-specific slots (e.g. sidebar, top banner) that
  are structurally and visually distinct from search result rows.

### Hard Separation From Organic Ranking
- **Organic price search/comparison results are computed only from Price,
  Service, ClinicBranch, and related non-advertising data.** The
  query/sort/filter logic described in `05-SEARCH-AND-FILTERS.md` has no
  input from `Advertisement` records, `active` status, spend, or placement.
- Advertisement data is rendered by a separate, independent code path/view
  that does not read from or write to the organic result set.
- This separation is a permanent architectural constraint, not a launch-time
  precaution to be relaxed later — even once ads are switched on, they must
  never be able to reorder, filter, hide, or otherwise influence organic
  price comparisons.
- No "sponsored organic ranking" (i.e. a paid clinic quietly ranked higher in
  normal results) is permitted at any point — see also
  `00-MASTER-REQUIREMENTS.md` §17 (Reviews/Ratings/Badges) for the adjacent
  "no fake prominence" rule.

## Open Questions

- Exact analytics tooling/provider (self-hosted vs. third-party) — deferred;
  whichever is chosen must satisfy the privacy constraints above.
- Whether the daily email should also flag Data Quality issues (e.g.
  monitoring failures) or stay purely usage-focused — leaning toward
  including a short "system issues" line since it's already a proposed
  metric, but not finalised.
- Ad pricing model / rate card — irrelevant until advertising is actually
  switched on; not a launch concern.
