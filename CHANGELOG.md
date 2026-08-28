# Changelog

High-level release notes. Implementation-level detail (specific files,
schema changes, and internal configuration) is tracked in the private
repository.

## Latest — Revenue features, retention automation, and scaling

- Added recurring **memberships** (subscription plans with unlimited or
  refreshing per-cycle credits), built on hosted checkout for correct
  handling of renewals, retries, and proration.
- Added **gift cards** and **packages** (bundled-session products),
  purchasable by guests without an account.
- Added a **waitlist and gap-fill engine** — clients are notified
  automatically when a slot opens up.
- Added **no-show risk scoring** and **rebooking / win-back nudges** as
  part of a growing automation engine.
- Added **coupons** and a post-appointment **review** flow.
- Introduced **deposits** and a full **guest checkout** path.
- Moved background/scheduled work (reminders, nudges, gap-fill, AI SMS
  handling) onto a dedicated worker system split into a low-latency pool
  and a bulk pool, so large scheduled sweeps can never delay a live
  client interaction.
- Made rate limiting shared across instances via a distributed store when
  running multiple app instances.

## Earlier milestones

- **Foundation** — multi-tenant architecture with per-shop isolation.
- **Authentication** — owner and client signup, OTP verification, secure
  session handling.
- **Tenant onboarding** — services, staff invitations, shop branding and
  business hours.
- **Booking engine** — real-time availability, recurring appointments with
  whole-series conflict validation.
- **Automated reminders** for upcoming appointments.
- **Client payments** — Stripe-backed checkout with server-side
  verification.
- **AI-powered SMS/WhatsApp booking assistant**, using the same validated
  booking engine as the web app.
- **Platform admin console** for managing every shop from one place.
