# Traceability Matrix

Maps each major requirement area from `00-MASTER-REQUIREMENTS.md` to the
document(s) where it is specified in detail. Use this to confirm nothing was
silently dropped when documents are edited in later phases.

| # | Requirement area | Master Req. section | Detailed in |
|---|---|---|---|
| 1 | Brand, geography (RoI only), language, currency | §1 | 01-PRODUCT-SPEC.md |
| 2 | No "Compare Total Cost" / no combined totals | §2, §3 | 05-SEARCH-AND-FILTERS.md |
| 3 | Real data only, no fabrication | §2, §4 | 03-DATA-INGESTION.md |
| 4 | Clinic group vs. branch separation | §2 | 02-DATA-MODEL.md |
| 5 | Branch-specific vs. network-wide pricing | §2 | 02-DATA-MODEL.md, 05-SEARCH-AND-FILTERS.md |
| 6 | Service aliases / variants | §2, §7 | 02-DATA-MODEL.md, 03-DATA-INGESTION.md |
| 7 | Plans/memberships separate from prices | §2, §14 | 02-DATA-MODEL.md, 06-ADMIN-DASHBOARD.md |
| 8 | The €0 rule (never €0 unless genuinely free) | §3 | 02-DATA-MODEL.md (Constraints), 03-DATA-INGESTION.md, 05-SEARCH-AND-FILTERS.md, 06-ADMIN-DASHBOARD.md |
| 9 | Missing price wording ("Contact clinic...") | §3 | 02-DATA-MODEL.md, 05-SEARCH-AND-FILTERS.md |
| 10 | "From €X" preserved, variable pricing preserved | §3 | 02-DATA-MODEL.md, 05-SEARCH-AND-FILTERS.md |
| 11 | Allowed data source types | §4 | 03-DATA-INGESTION.md |
| 12 | Submission review before publication | §4 | 08-SUBMISSIONS-AND-CLINIC-CLAIMS.md, 07-VERIFICATION-AND-HISTORY.md |
| 13 | Direct source URL requirement | §4 | 02-DATA-MODEL.md (Constraints), 03-DATA-INGESTION.md |
| 14 | "Supplied directly by clinic" labelling | §4 | 02-DATA-MODEL.md, 03-DATA-INGESTION.md, 08-SUBMISSIONS-AND-CLINIC-CLAIMS.md |
| 15 | Known-URL monitoring, weekly cadence | §5 | 04-WEEKLY-MONITORING.md |
| 16 | Source → Detect → Review → Verify → Publish workflow | §5 | 04-WEEKLY-MONITORING.md, 07-VERIFICATION-AND-HISTORY.md |
| 17 | Polite scraping (rate limits, robots.txt, terms) | §5 | 04-WEEKLY-MONITORING.md |
| 18 | Future discovery of new pricing pages | §5 | 04-WEEKLY-MONITORING.md (Future Discovery) |
| 19 | Price history preservation | §6 | 02-DATA-MODEL.md, 07-VERIFICATION-AND-HISTORY.md |
| 20 | Service catalogue (30 services) | §7 | 00-MASTER-REQUIREMENTS.md §7, 02-DATA-MODEL.md |
| 21 | Worming/parasite pricing complexity (no universal price) | §7 | 03-DATA-INGESTION.md |
| 22 | Filters: county, pet, age, weight, services, no-filter state | §8 | 05-SEARCH-AND-FILTERS.md |
| 23 | Valid prices not hidden for missing optional metadata | §8 | 05-SEARCH-AND-FILTERS.md |
| 24 | Search result required fields | §9 | 05-SEARCH-AND-FILTERS.md |
| 25 | Data model entities & relationships | §10 | 02-DATA-MODEL.md |
| 26 | Price type enumeration | §10 | 02-DATA-MODEL.md |
| 27 | Duplicate clinic detection & future merge | §11 | 02-DATA-MODEL.md, 06-ADMIN-DASHBOARD.md |
| 28 | Admin dashboard sections | §12 | 06-ADMIN-DASHBOARD.md |
| 29 | Clinic claim feature (future, off at launch) | §13 | 08-SUBMISSIONS-AND-CLINIC-CLAIMS.md |
| 30 | Pet Health Plans structure | §14 | 02-DATA-MODEL.md, 06-ADMIN-DASHBOARD.md |
| 31 | Donations (PayPal, non-ranking) | §15 | 09-ANALYTICS-DONATIONS-ADS.md |
| 32 | Advertisements (off at launch, no ranking influence) | §16 | 09-ANALYTICS-DONATIONS-ADS.md, 06-ADMIN-DASHBOARD.md, 02-DATA-MODEL.md |
| 33 | No fake reviews/ratings/badges/sponsored ranking | §17 | 09-ANALYTICS-DONATIONS-ADS.md, 01-PRODUCT-SPEC.md (Non-Goals) |
| 34 | Analytics metrics & daily email | §18 | 09-ANALYTICS-DONATIONS-ADS.md |
| 35 | Navigation (desktop/mobile/footer) | §19 | 01-PRODUCT-SPEC.md |
| 36 | Design system (palette, tone) | §20 | 01-PRODUCT-SPEC.md, 10-SECURITY-LEGAL-SEO-CLOUDFLARE.md (Accessibility) |
| 37 | Legal / disclaimer stance | §21 | 10-SECURITY-LEGAL-SEO-CLOUDFLARE.md |
| 38 | Initial data target (~40 branches, quality over quantity) | §22 | 01-PRODUCT-SPEC.md, 03-DATA-INGESTION.md |
| 39 | Known official source URLs | §23 | 00-MASTER-REQUIREMENTS.md §23, 03-DATA-INGESTION.md |
| 40 | Phase 0 scope boundary (docs only) | §24 | README.md, this file |
| 41 | Security: secrets, access control, abuse protection | (implied throughout) | 10-SECURITY-LEGAL-SEO-CLOUDFLARE.md |
| 42 | SEO & Cloudflare deployment readiness | (implied throughout) | 10-SECURITY-LEGAL-SEO-CLOUDFLARE.md |

## Coverage Check

Every numbered requirement in the original project brief maps to at least one
row above. If a future edit to any `docs/` file removes something covered
here without updating this matrix, that is a signal the change needs a second
look before merging.

## How to Use This File

When adding or changing a requirement:
1. Update `00-MASTER-REQUIREMENTS.md` first (it is the source of truth).
2. Update the detailed document(s) that implement it.
3. Update this matrix so the requirement remains traceable.
