# 05 — Search & Filters

## Purpose

Let a user find and compare real, sourced prices for one or more services,
without ever producing a combined "total cost" figure.

## Filters

| Filter | Values |
|---|---|
| County | All 26 Republic of Ireland counties |
| Pet | Dog, Cat, Other |
| Age | Puppy/Kitten, Adult, Senior, Not specified |
| Weight | 0–5, 5–10, 10–15, 15–20, 20–25, 25–30, 30–35, 35–40, 40–45, 45–50, 50+ kg, Other / Not applicable |
| Services | Single or multiple selection, from the canonical Service Catalogue |

- **"No filters" is a supported, first-class state** — it browses everything,
  not an error or empty state.
- Filters combine with AND logic across filter types (e.g. County=Cork AND
  Pet=Dog), and OR logic within a multi-select filter (e.g. Service=Dog Spay
  OR Nail Clipping).
- A valid price is **never hidden** solely because optional metadata (weight,
  age) is missing on that record — missing metadata is treated as "applies
  broadly", not as a reason to exclude the row. Filters only exclude a row
  when there's a genuine, stated mismatch (e.g. record explicitly says
  "Cat" and the user filtered for "Dog").

## Multiple Services: Strict Separation

- When more than one service is selected, results are grouped into **one
  section per service**, each with its own heading and its own result
  rows/table.
- **There is no combined total, no "estimated annual cost", and no summed
  figure anywhere in the UI**, regardless of how many services are selected.
  This is enforced at the display layer (no code path sums `Price.amount`
  across services) and should be covered by an automated test once the
  feature is built, to prevent regression.
- A short, visible note should accompany multi-service results explaining
  that prices are shown separately because real costs depend on the
  individual pet and clinic.

## Result Fields

Each result row/card must be able to display:

- Clinic / group name
- Physical branch (when relevant — network-wide plan/price rows should say so
  instead of naming one branch)
- Town
- County
- Service (canonical name + the clinic's own wording, if different)
- Pet type
- Price (formatted per `price_type` — see below)
- Price type
- Weight conditions (if any)
- Whether consultation is included
- Notes
- Verification date
- Source (Website / Supplied directly by clinic / User submission, etc.)
- Direct source link (when `source_type = Website` and a URL exists)

## Price Display Rules

- `Fixed` → the amount, formatted as €X.XX.
- `From` → "From €X.XX" — the word "From" is never dropped.
- `Variable` → "Price varies — contact clinic".
- Missing/unknown amount → "Contact clinic for price".
- `Plan Included` → "Included with Pet Health Plan" (or the plan's actual
  name) — never a numeric amount, never €0.
- `Network-wide` → the price is shown with an explicit label (e.g.
  "Network-wide price — regional variations may apply") rather than being
  presented as branch-confirmed.
- **A price is never, under any formatting path, rendered as "€0" or "Free"
  unless the underlying record has been explicitly marked genuinely free**
  per the constraints in `02-DATA-MODEL.md`.

## Result Layout

- Results should work as either cards or a table depending on breakpoint;
  the same field set applies either way.
- On mobile, the most decision-relevant fields (service, price, price type,
  clinic/branch, county) should be visible without needing to expand a row;
  secondary fields (notes, verification date, source link) can be tucked
  behind a "details" expansion, but must remain reachable, not removed.

## Sorting

- Default sort should be deterministic and based only on organic factors
  (e.g. price ascending within a service group, or alphabetical by
  clinic/branch) — never influenced by advertising, donations, or any
  sponsored flag. See `09-ANALYTICS-DONATIONS-ADS.md` for the explicit
  separation requirement between ads and organic ranking.

## Empty / Partial States

- No results for the current filter combination: explain why in plain
  language and suggest removing the most restrictive filter, rather than
  showing a blank page.
- Some but not all selected services have results: show the services that do
  have results normally, and explicitly note which selected services
  returned nothing (do not silently drop them from view).

## Non-Goals

- No "Compare Total Cost" button, toggle, or hidden feature. This is a hard
  product rule, not a missing feature to be added later.
- No ranking or boosting based on advertising spend, donation history, or
  clinic prominence.

## Open Questions

- Whether county/service should be reflected in the URL for shareable/SEO
  links (e.g. `/compare?county=cork&service=dog-spay`) — recommended for SEO
  (`10-SECURITY-LEGAL-SEO-CLOUDFLARE.md`) but not yet finalised.
- Exact wording for the "prices shown separately" note above multi-service
  results — to be finalised alongside legal review of overall copy.
