# 08 — Submissions & Clinic Claims

## Purpose

Define how the public and clinics can contribute information, and describe
(without building) a future clinic-claim feature that must remain off at
launch.

## User Submissions

- Any visitor can submit a correction or a new-price tip without creating an
  account.
- A submission references either an existing Price (as a proposed
  correction) or describes a new branch/service in free text.
- Submissions never publish automatically. Every submission enters the
  `VerificationQueue` and requires administrator Approve/Reject (see
  `06-ADMIN-DASHBOARD.md` §User Submissions and `07-VERIFICATION-AND-HISTORY.md`).
- Contact information from the submitter is optional; if given, it is used
  only to follow up on that submission (see `10-SECURITY-LEGAL-SEO-CLOUDFLARE.md`
  for privacy handling).

## Clinic Submissions

- A clinic can supply information directly (email, phone call, form, or a
  future clinic portal) without needing the clinic-claim/login feature below.
- Recorded as a `ClinicSubmission` with channel, submitter, and date.
- On admin approval, results in Price/Plan records labelled `Source: Supplied
  directly by clinic` — never assigned an invented URL.
- This path is independent of, and does not require, the future clinic-claim
  feature — a clinic can supply data today (post-launch) even before any
  self-service claim flow exists.

## Moderation (applies to both submission types)

- Every submission is reviewed by an administrator before it can change
  anything visible on the public site.
- Rejections should record a reason for internal traceability.
- Repeated bad-faith or clearly inaccurate submissions from the same source
  should be visible to admins as a pattern (see Data Quality in
  `06-ADMIN-DASHBOARD.md`) rather than only handled one at a time.

## Future Clinic Claim Feature — LAUNCH-OFF

**Status at launch: fully disabled. No public entry point performs any
action.**

Intended future flow (architecture only, not built):

1. A representative of a clinic requests to claim their branch listing,
   using a business email address associated with that clinic.
2. A verification/magic link is sent to that business email.
3. Following the link starts a claim request, which still requires
   **administrator approval** before the claim is granted — email
   verification alone is not sufficient to grant control of a listing.
4. Once approved, a claimed clinic may (in a future phase) submit updates
   more directly, but any such update still passes through the same
   verification principles as a `ClinicSubmission` unless a separate,
   deliberate decision is made later to trust claimed clinics with more
   direct publishing rights — that decision is explicitly out of scope now.

Requirements for when this is eventually built:

- The claim flow must not allow a clinic to self-verify without admin
  approval.
- The claim flow must not be able to alter historical `PriceHistory`.
- The claim flow's existence must not change how non-claimed branches are
  treated (i.e. unclaimed clinics remain fully functional in the directory).
- Until explicitly turned on by a deliberate launch decision, no UI element
  should suggest to a clinic or a user that claiming is currently available.

## Open Questions

- Whether a clinic-claim request itself (before admin approval) should be
  visible anywhere in the admin dashboard as its own status, or treated as a
  type of `ClinicSubmission` — recommend treating it as its own lightweight
  entity when it's actually built, since "claiming a listing" is a different
  action from "submitting a price", but this is not decided here.
- Whether user submissions should have any rate-limiting/anti-abuse
  mechanism at launch — see `10-SECURITY-LEGAL-SEO-CLOUDFLARE.md` §Abuse
  Protection.
