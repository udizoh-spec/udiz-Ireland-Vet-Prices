# 01 — Product Spec

## Goal

Give pet owners in the Republic of Ireland a trustworthy, free way to see how
veterinary prices vary between clinics for the same treatment, so they can make
an informed choice before booking — without registering, without being upsold,
and without seeing invented numbers.

## Users

- **Pet owners** researching or comparing prices before booking a treatment.
  Primary audience; no account required.
- **Clinics** (future) — may eventually supply data directly or claim a branch
  listing. At launch, clinics interact with udiz only as a data source, not as
  logged-in users.
- **Administrators** — review and publish submissions, manage the data model,
  monitor data quality. Internal only; not a public-facing role.

## Pages (public)

1. **Home** — headline, supporting text, trust message, primary CTAs
   ("Compare Prices", "Browse Clinics").
2. **Compare Prices (Search)** — filter panel + results, grouped by service
   when multiple services are selected.
3. **Browse Clinics** — directory of clinic groups and their branches, by
   county/town/group.
4. **Branch detail** — one physical branch: address/contact if known, its
   priced services, its plan (if any), source & verification info.
5. **How It Works** — plain-language explanation of where prices come from and
   why users should still confirm before booking.
6. **About** — independence statement, what udiz is and isn't.
7. **Legal** — Disclaimer, Terms, Privacy (English only).
8. **Contact** — a way to report a wrong price or ask a question.

## Pages (admin — see `06-ADMIN-DASHBOARD.md`)

Not public. Reachable only by an authenticated administrator.

## Core UX Principles

- **No dead ends without an explanation.** If a filter combination returns
  nothing, say so and suggest widening it — never show a blank page.
- **Every price is traceable.** A user should always be able to see, in one
  click, where a number came from and when it was last checked.
- **Separation over aggregation.** When several services are selected, they
  are shown as separate, clearly-labelled groups. udiz never adds prices
  together into a total, and never implies a "total cost of ownership" figure.
- **Missing data is stated, not hidden or guessed.** "Contact clinic for
  price" is a valid, expected result state, not an error.
- **Plans are not prices.** A Pet Health Plan or membership is presented as its
  own section, never blended into the one-off price list.

## Non-Goals (explicitly out of scope)

- No online booking or payments.
- No "Compare Total Cost" or any combined/annual cost calculator.
- No user accounts required for searching or browsing.
- No reviews, ratings, testimonials, or "best vet" badges — fake or real —
  at launch (real reviews are not ruled out for a much later phase, but are
  not part of this spec).
- No advertising visible at launch (architecture only — see
  `09-ANALYTICS-DONATIONS-ADS.md`).
- No clinic login/claim flow active at launch (architecture only — see
  `08-SUBMISSIONS-AND-CLINIC-CLAIMS.md`).
- No coverage of Northern Ireland.
- No languages other than English.
- No currency other than EUR.

## Success Criteria (qualitative, for launch)

- A user can find a real, sourced price for at least the most common
  services (consultation, vaccination, neutering) in the counties where udiz
  has data, within two or three interactions.
- Every visible price can be traced to a source or is explicitly marked as
  clinic-supplied.
- No zero-euro price appears anywhere on the public site unless it is
  genuinely, explicitly free.
- No combined/total price appears anywhere on the public site.

## Open Questions

- Should "Browse Clinics" support a map view at launch, or is a list/dropdown
  sufficient for v1? Deferred — not required by the master requirements, so
  default to list/dropdown for launch and treat a map as a later enhancement.
- Should branch pages be indexable/SEO-targeted individually at launch, or
  behind the search tool only? See `10-SECURITY-LEGAL-SEO-CLOUDFLARE.md`.
