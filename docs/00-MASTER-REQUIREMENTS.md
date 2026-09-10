# 00 — Master Requirements

This is the single consolidated source of truth for udiz. Every other document in
`docs/` must be consistent with this file. Where a conflict is found, this file
wins, and the conflicting document must be corrected.

Requirements are tagged:

- **MUST** — required for launch. Non-negotiable.
- **SHOULD** — strongly desired for launch, but the product can ship without it
  if genuinely necessary, with the gap documented.
- **FUTURE** — intentionally out of scope for launch; architecture should not
  block adding it later.
- **LAUNCH-OFF** — the capability is built/scaffolded but must be inert
  (disabled/hidden) at launch, and switching it on later must be a deliberate,
  isolated action.

---

## 1. Scope & Identity

- **[MUST]** Brand name: **udiz**. Subtitle: **Ireland Vet Prices**.
- **[MUST]** Geographic scope: Republic of Ireland only, all 26 counties.
- **[MUST]** Northern Ireland must NOT be included, referenced as in-scope, or
  implied to be covered.
- **[MUST]** Website language: English only.
- **[MUST]** Currency: EUR (€) only.
- **[MUST]** Homepage headline: "Compare Vet Prices Across Ireland — Save
  Hundreds of Euros a Year".
- **[MUST]** Homepage supporting text: "Compare veterinary treatment prices from
  clinics across Ireland and discover where you could save on the cost of caring
  for your pet."
- **[MUST]** Trust message: "Free to use. No registration required."

## 2. Core Product Rules

- **[MUST]** The site compares veterinary prices across Irish veterinary clinics.
- **[MUST]** Public price search is free, with no registration or personal
  information required.
- **[MUST]** There is no "Compare Total Cost" feature. Combined
  annual/multi-service totals are never calculated or displayed, anywhere.
- **[MUST]** When a user selects multiple services, each service is compared
  separately, in its own result group/table.
- **[MUST]** Real data only. No fake, demo, placeholder, or invented
  clinic/price data may ever be presented as real — in any environment a user
  can reach, including staging shown to non-developers.
- **[MUST]** No duplicate clinics, branches, or canonical services. See
  `02-DATA-MODEL.md` for duplicate-detection fields.
- **[MUST]** Clinic groups and physical branches are modelled as separate
  entities. A group-level fact is never silently attributed to a branch.
- **[MUST]** Branch-specific prices remain branch-specific; they are never
  propagated to other branches of the same group without independent evidence.
- **[MUST]** Network-wide prices are explicitly labelled as network-wide, and
  are never assumed to apply to a specific branch unless the source explicitly
  says so.
- **[MUST]** Service aliases/synonyms map to canonical services (see Service
  Catalogue, Section 7), but materially different variants/packages remain
  distinct records rather than being merged into one.
- **[MUST]** Plans, memberships, subscriptions, and discounts are stored and
  displayed separately from standard one-off prices.

## 3. The €0 Rule (zero tolerance)

- **[MUST]** A service included in a paid plan is never displayed as €0. Use
  "Included with Pet Health Plan" (or the plan's actual name).
- **[MUST]** A treatment is never shown as free or €0 unless there is explicit,
  documented evidence that it is genuinely free with no conditions attached.
- **[MUST]** If a price is unavailable, unclear, or not published, display
  "Contact clinic for price" or "Price varies — contact clinic". €0 must never
  be used to represent missing data, under any code path, including defaults,
  fallbacks, or admin form defaults.
- **[MUST]** "From €X" is preserved as "From €X" — never silently converted to
  a fixed price or to €X flat.
- **[MUST]** Variable pricing remains labelled as variable; it is never
  collapsed into a single number.

## 4. Data Sourcing

- **[MUST]** Allowed source types: official clinic websites/price lists,
  information supplied directly by a clinic, user submissions, clinic
  submissions, and other explicitly identified sources.
- **[MUST]** User submissions and clinic submissions require administrator
  review before publication. Nothing from an unverified submission reaches the
  public site automatically.
- **[MUST]** Every website-derived price stores a direct URL to the exact
  clinic page where that price appears — not the clinic's homepage, when a
  specific pricing page exists.
- **[MUST]** For website-sourced prices: store and show the exact source URL,
  preserve the source, preserve the verification date, preserve price history.
- **[MUST]** For clinic-supplied information: label as "Source: Supplied
  directly by clinic", do not invent a URL, and preserve whatever submission
  details exist (e.g. contact name, date, channel).
- **[MUST]** No fabricated, invented, or copied-without-verification data, at
  any phase, for any reason, including to "fill out" the directory.

## 5. Monitoring & Ingestion

- **[MUST]** Support monitoring of known official clinic pricing URLs.
- **[SHOULD]** Weekly checks of known URLs at launch.
- **[MUST]** Detected changes go to a review/verification queue — never
  auto-published.
- **[FUTURE]** Discovery of new pricing pages beyond the known/monitored list.
- **[MUST]** Monitoring/scraping must be polite: rate-limited, respectful of
  `robots.txt`, respectful of relevant site terms, and designed not to overload
  clinic websites.
- **[MUST]** Required workflow: Source → Detect → Review → Verify → Publish.
  "Source → Automatically publish" is explicitly forbidden.

## 6. Price History

- **[MUST]** Historical prices are preserved, not overwritten. Each change
  stores: previous price, new price, date/time of change, source, verification
  status, and notes.

## 7. Service Catalogue

The canonical service list (see `02-DATA-MODEL.md` for full field definitions):

1. Vet Consultation
2. Follow-up Consultation
3. Nurse Consultation
4. Emergency Consultation
5. Puppy Primary Vaccination
6. Adult Dog Booster
7. Kennel Cough Vaccination
8. Kitten Primary Vaccination
9. Adult Cat Booster
10. Rabies / Travel Vaccination
11. Dog Worming
12. Cat Worming
13. Flea Treatment
14. Tick Treatment
15. Flea + Tick + Worming Package
16. Microchipping
17. Nail Clipping
18. Anal Gland Expression
19. Dog Castration
20. Dog Spay
21. Cat Castration
22. Cat Spay
23. Dental Check
24. Dental Descale & Polish
25. Dental Extraction
26. Blood Test
27. Urine Test
28. X-Ray
29. Ultrasound
30. Pet Passport / Travel Consultation

- **[MUST]** Worming/parasite prices, which depend on product, species, weight,
  and frequency, must never be reduced to a single invented "universal" price.

## 8. Filters

- **[MUST]** County: all 26 Republic of Ireland counties.
- **[MUST]** Pet: Dog, Cat, Other.
- **[MUST]** Age: Puppy/Kitten, Adult, Senior, Not specified.
- **[MUST]** Weight (kg bands): 0–5, 5–10, 10–15, 15–20, 20–25, 25–30, 30–35,
  35–40, 40–45, 45–50, 50+, Other / Not applicable.
- **[MUST]** Services: single or multiple selection.
- **[MUST]** "No filters" (browse everything) must be supported.
- **[MUST]** A valid price is never hidden solely because optional metadata
  (e.g. weight or age) is missing.

## 9. Search Result Fields

Every result must be able to show: clinic/group, physical branch (where
relevant), town, county, service, pet type, price, price type, weight
conditions, whether consultation is included, notes, verification date,
source, and a direct source link.

## 10. Data Model (summary — full detail in `02-DATA-MODEL.md`)

Entities: ClinicGroup, ClinicBranch, Service, ServiceAlias, ServiceVariant,
Price, Plan, PlanBenefit, PriceHistory, UserSubmission, ClinicSubmission,
VerificationQueue, Advertisement, AdminUser, Analytics, SystemIssue.

Price types: Fixed, From, Variable, Package, Plan Included, Discounted,
Network-wide, Branch-specific.

## 11. Duplicate Prevention

- **[MUST]** Clinic duplicate detection considers: normalized name, parent
  group, address, Eircode, phone, website, coordinates.
- **[FUTURE]** Admin-facing merge of duplicate clinics while preserving history.

## 12. Admin Dashboard

- **[MUST]** Sections: Clinics/Groups/Branches; Services/Aliases/Variants;
  Prices; Plans & Memberships; Price History; User Submissions; Clinic
  Submissions; Verification Queue; Analytics; Advertisements; Data Quality.

## 13. Clinic Claims

- **[FUTURE / LAUNCH-OFF]** Business-email verification → magic link →
  administrator approval. Architecture should support this later; it must be
  fully inactive at launch (no public entry point that does anything).

## 14. Pet Health Plans

- **[MUST]** Stored separately from ordinary prices. A plan may include
  consultations, vaccinations, parasite prevention, nurse consultations,
  discounts, or other benefits.
- **[MUST]** Plan benefits are never converted into ordinary €0 prices.

## 15. Donations

- **[MUST]** A permanent, optional donation link (PayPal:
  `https://paypal.me/udizoh`) appears on every public page.
- **[MUST]** Donations never affect rankings, search results, price
  comparisons, or clinic visibility.
- **[MUST]** No PayPal credentials or payment secrets are stored by udiz.

## 16. Advertisements

- **[LAUNCH-OFF]** Ad architecture may exist, but ads are off at launch.
- **[MUST, once enabled]** Admin-controlled; clearly labelled "Advertisement"
  or "Sponsored"; never manipulates organic price ranking.

## 17. Reviews / Ratings / Badges

- **[MUST]** No fake reviews, fake ratings, fake testimonials, "best vet"
  badges, or sponsored organic ranking of any kind, ever.

## 18. Analytics

- **[SHOULD]** Daily usage summary email to `udizoh@gmail.com`, covering
  metrics such as unique visitors/sessions, page views, price searches, clinic
  views, most-searched county/pet/service, submission counts, and system
  issues.
- **[MUST]** Analytics are privacy-conscious (see `10-SECURITY-LEGAL-SEO-CLOUDFLARE.md`).

## 19. Navigation

- **[MUST]** Desktop: `udiz | Compare Prices | Browse Clinics | How It Works |
  About | ❤️ DONATE`. Logo links to home.
- **[MUST]** Mobile: hamburger navigation with the same items.
- **[MUST]** Footer: legal links, disclaimer, donate.

## 20. Design

- **[MUST]** Calm, trustworthy, friendly, modern. Palette: white, soft green,
  muted teal, light neutral, dark grey. No neon, no clutter, no excessive
  animation.
- **[MUST]** Mobile responsive, accessible, SEO-ready, secure, suitable for
  GitHub source control and Cloudflare deployment.

## 21. Legal

- **[MUST]** The site is informational and comparative only. Users are
  responsible for verifying current prices, availability, terms, suitability,
  and treatment details before booking.
- **[MUST]** The site must not promise accuracy, completeness, availability, or
  quality.
- **[MUST]** Legal wording is written cautiously and should be reviewed by an
  Irish solicitor before launch — it is not a substitute for legal advice.
- **[MUST NOT]** An exaggerated "we accept no responsibility for anything"
  style disclaimer.

## 22. Initial Data

- **[SHOULD]** Approximately 40 real physical clinic branches at launch — a
  target, not a quota.
- **[MUST]** Quality over quantity. Never invent branches or prices to reach a
  number.
- **[MUST]** Known research sources (see Section 23) may be documented for
  future ingestion; they must not be turned into fabricated current data.

## 23. Known Official Sources Identified During Research

These are reference pointers only, to be used by a future, properly reviewed
ingestion process — not data to copy in now.

| Clinic group | URL(s) |
|---|---|
| Village Vets | https://www.villagevets.ie/pricelist |
| MyVet | https://www.myvet.ie/about-us/procedures-pricing, https://www.myvet.ie/referrals/fixed-prices |
| Urban Vets | https://urbanvets.ie/price-list/ |
| Dublin Bay Vets | https://dublinbayvets.ie/services/prices/ |
| Donnybrook Vets | https://donnybrookvets.ie/pricing/ |
| Marina Vets | https://www.marinavets.ie/pricelist/ |
| Value Vets | https://www.valuevets.ie/about-us/pricing |
| Abbeyville | https://abbeyvillevet.ie/, https://abbeyvillevet.ie/pet-health-plan/ |

## 24. Phase Rule

- **[MUST]** Phase 0 is documentation only. No frontend, backend, database
  migrations, scraping code, scheduled jobs, authentication, admin UI,
  Cloudflare deployment, or payment systems are built in Phase 0.

## 25. Quality Rules Governing This Document Set

- Do not invent requirements that conflict with this file.
- Do not silently remove requirements from this file in later edits — mark
  superseded items instead of deleting history of the decision.
- If something is ambiguous, document the ambiguity (see each document's "Open
  Questions" section) rather than inventing an answer.
- Launch requirements are clearly distinguished from future requirements
  (MUST/SHOULD vs FUTURE/LAUNCH-OFF tags above).
- Website-sourced prices are clearly distinguished from clinic-supplied prices
  at every layer: data model, ingestion, and display.
- Branch-specific prices are clearly distinguished from network-wide prices.
- Ordinary prices are clearly distinguished from plans/memberships.
- The "never €0 unless explicitly free" rule is enforced as a hard constraint,
  not a style guideline — see `02-DATA-MODEL.md` §Constraints.
- Direct source URL requirements are enforced as a hard constraint.
- Weekly monitoring is enforced as a documented, scheduled process.
- "No total cost comparison" is enforced as a hard constraint on the search/UI
  layer — see `05-SEARCH-AND-FILTERS.md`.
- No fake data, no placeholder data presented as real, anywhere, at any phase.

## Open Questions

- Exact legal entity/ownership behind udiz, for the Terms/Privacy documents —
  needs solicitor input, not assumed here.
- Whether "Other" pet type needs its own sub-catalogue of services, or reuses
  the Dog/Cat catalogue with a flag — deferred to `02-DATA-MODEL.md`.
- Exact cadence and content of the daily analytics email beyond "should exist"
  — deferred to `09-ANALYTICS-DONATIONS-ADS.md`.
