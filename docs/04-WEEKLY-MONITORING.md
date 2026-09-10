# 04 — Weekly Monitoring

## Purpose

Keep published prices reasonably current by periodically re-checking known
clinic pricing pages, without ever overloading clinic websites or
auto-publishing an unverified change.

## Two Concepts

### A. Monitoring known pricing pages (launch priority)
A curated list of clinic pricing URLs (seeded from the references in
`00-MASTER-REQUIREMENTS.md` §23, expanded over time via ingestion) is checked
on a schedule.

### B. Future discovery of new pricing pages
**[FUTURE]** Automatically finding pricing pages udiz doesn't already know
about (e.g. crawling a clinic's site to locate a pricing page, or discovering
new clinics entirely). Not required for launch; the architecture should not
preclude adding it later, but it introduces materially more risk (accuracy,
politeness, legal) and is deliberately deferred.

## Schedule

- **[SHOULD]** Known URLs are checked weekly at launch. The exact day/time is
  an implementation detail, but the cadence must not be more aggressive than
  weekly without a specific reason logged (e.g. a clinic known to update
  prices frequently).

## Workflow (mandatory)

```
Source → Detect → Review → Verify → Publish
```

- **Source**: the known, curated pricing URL for a branch/group.
- **Detect**: fetch the page, extract the relevant value(s), compare against
  the currently live Price record(s).
- **Review**: if a difference is detected, create a `VerificationQueue` entry
  (type `DetectedChange`) — the live Price is **not** touched yet.
- **Verify**: an administrator inspects the detected change against the
  source page (and, where useful, against the previous PriceHistory) and
  marks it Approved or Rejected.
- **Publish**: only on Approved does the change become the new live Price,
  with the old value moved into `PriceHistory`.

**"Source → Automatically publish" is explicitly forbidden at every stage.**
No code path may skip Review/Verify, including "high confidence" automated
matches.

## Change Detection

- A detected change records: previous value, new value, the exact URL
  fetched, and a timestamp.
- Detection should distinguish between:
  - A genuine price change (number differs).
  - A structural change (the page layout changed and extraction may be
    unreliable) — this should be flagged as a `SystemIssue`, not presented as
    a confident price change.
  - A page that has gone missing/changed URL (404/redirect) — also a
    `SystemIssue`, since the stored `source_url` may now be stale.

## Politeness Requirements (mandatory)

- **Rate limited**: requests to any single clinic domain are spaced out;
  monitoring must not behave like a burst crawler.
- **Respect `robots.txt`**: a domain's `robots.txt` is checked and honoured
  before fetching monitored pages on a schedule.
- **Respect relevant site terms**: if a clinic's terms of use explicitly
  prohibit automated access, that clinic is monitored manually/via
  clinic-supplied data instead, not scraped against its stated terms.
- **Minimise load**: prefer the lightest request that achieves the check
  (e.g. conditional requests / caching where supported) rather than always
  downloading a full page unnecessarily.

## Failure Handling

- A fetch failure (timeout, error status, robots.txt disallow) is logged as a
  `SystemIssue`, not treated as "price unavailable" or "price is €0". The
  previously verified price remains live and visibly dated with its last
  verification date; it is not blanked out because of a single failed check.
- Repeated failures for the same URL should be surfaced to admins (e.g. via
  the Data Quality section of the admin dashboard) rather than silently
  retried forever without visibility.

## Future Discovery (explicitly out of launch scope)

**[FUTURE]** Possible later capability: given a clinic's homepage or name,
locate its pricing page automatically, or identify new clinics not yet in the
directory. This must, when built, still route through the same
Source → Detect → Review → Verify → Publish workflow and the same politeness
requirements — discovery only ever proposes candidates, it never publishes.

## Open Questions

- Exact retry/backoff policy for transient failures — an implementation
  detail once the monitoring job is actually built.
- Whether monitoring runs as a single scheduled job or per-clinic jobs with
  independent schedules — deferred to `10-SECURITY-LEGAL-SEO-CLOUDFLARE.md`
  and later infrastructure design (this doc assumes Cloudflare Worker cron
  triggers as the likely mechanism, per the project's stated deployment
  target, but does not mandate it as the only option).
