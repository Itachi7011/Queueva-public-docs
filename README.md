<div align="center">

# Queueva
### Multi-Tenant Booking & Client Engagement Platform

**Booking, reminders, payments, memberships, and an AI-powered SMS/WhatsApp booking assistant — for salons, gyms, clinics, and independent service shops, built as a single self-hostable multi-tenant SaaS.**

[![Status](https://img.shields.io/badge/status-in%20development-orange)]()
[![Live Demo](https://img.shields.io/badge/demo-coming%20soon-lightgrey)]()
[![License](https://img.shields.io/badge/license-proprietary-blue)]()

**[Live Demo](#) &nbsp;·&nbsp; [Demo Video](#) &nbsp;·&nbsp; [Screenshots](#screenshots) &nbsp;·&nbsp; [Contact for Licensing / Investment](#contact)**

</div>

---

> ⚠️ **This is a documentation-only repository.** It describes the product,
> architecture, and feature set of Queueva for evaluators, partners, and
> investors. Source code is maintained in a private repository and is not
> included here. If you're interested in a technical deep-dive, a live
> walkthrough, or licensing/investment discussion, see [Contact](#contact).

---

## What is Queueva?

Local service businesses — salons, gyms, clinics, independent repair shops
— need appointment scheduling, reminders, and (often) online payments.
Most off-the-shelf booking SaaS products are either expensive per-seat
subscriptions or lock a business into a single vendor's ecosystem.

**Queueva is a single, multi-tenant application that serves many
independent shops from one deployment** — each shop gets its own branded
booking page, staff accounts, service catalog, and business hours, fully
isolated from every other shop, without needing its own server or
database.

Beyond the booking core, Queueva includes recurring memberships, gift
cards & packages, a waitlist and gap-fill engine, no-show risk scoring,
automated win-back and rebooking nudges, and an AI agent that lets a
client book, cancel, or check availability entirely over SMS/WhatsApp.

---

## Core capabilities

- **Multi-tenant booking core** — real-time availability computation
  (business hours minus existing bookings, timezone-aware), recurring
  appointments with whole-series conflict validation, and full
  owner/staff/client role separation per shop.
- **Automated reminders & lifecycle nudges** — appointment reminders,
  rebooking nudges for lapsed clients, gap-fill notifications when a slot
  opens up, and win-back campaigns — all running on a scheduled background
  job system, not manual follow-up.
- **AI-powered SMS/WhatsApp booking assistant** — a client can text a
  shop's number and have a full conversation that checks availability,
  books, cancels, or joins the waitlist. The assistant uses the exact same
  booking engine and business rules as the web app — no special-cased
  shortcuts, so it can never violate business hours, capacity, or
  lead-time rules the UI also enforces.
- **No-show risk scoring** — appointments are scored for no-show risk based
  on client history, informing deposit requirements and staff prioritization.
- **Client payments** — real Stripe-backed checkout for appointments, with
  server-side verification and a webhook-based confirmation path (never
  trusted from the browser alone).
- **Recurring memberships** — unlimited or credit-allowance subscription
  plans per shop, built on Stripe Subscriptions + hosted Checkout for
  correct handling of renewals, retries, and proration.
- **Gift cards & packages** — prepaid credit and bundled-session products,
  purchasable by guests without an account.
- **Waitlist & capacity management** — clients can join a waitlist when a
  slot is full and are notified automatically if one opens up.
- **Coupons & reviews** — discount codes and a post-appointment review
  flow to support client retention and reputation.
- **Deposits & guest checkout** — configurable deposit requirements and a
  full guest flow so a client never needs to create an account just to pay.
- **Automation engine** — a growing set of lifecycle automations (reminders,
  rebooking nudges, win-back campaigns, review requests) that run on a
  dedicated background-worker system, decoupled from the request path.
- **Every paid integration degrades gracefully** — email, image storage,
  payments, and SMS all have a genuine non-erroring fallback when not
  configured, so the full signup → booking → payment flow works end to
  end even with zero paid services set up (useful for demos, evaluation,
  and low-cost pilots).
- **Platform admin console** — a super-admin view across every shop
  (tenant), with the ability to suspend or reactivate a shop.

---

## Why it's different

| | Queueva | Typical booking SaaS |
|---|---|---|
| **Deployment model** | Single, self-hostable multi-tenant application | SaaS-only, per-seat pricing |
| **AI booking channel** | Native SMS/WhatsApp AI agent using the real booking engine | Rarely offered, or a bolt-on chatbot with its own logic |
| **Graceful degradation** | Every paid integration (email, payments, images, SMS) has a real fallback | Usually hard-fails without configured API keys |
| **Client engagement** | Built-in no-show scoring, gap-fill, win-back, and rebooking automations | Often reminders only |
| **Payment model breadth** | One-off checkout, memberships/subscriptions, gift cards, and packages, all in one system | Usually just one-off payments |

---

## Architecture (high level)

Queueva runs as a single web application backed by one shared database,
with a separate background-worker system for scheduled and asynchronous
work:

```
                     ┌─────────────────────────┐
                     │        Web application         │
                     │  (public shop pages, booking,     │
                     │   client + owner dashboards,        │
                     │   platform admin console)             │
                     └────────────┬───────────┘
                                    │
                     ┌──────────────▼──────────────┐
                     │   Application-layer tenant       │
                     │   isolation & access guards        │
                     └──────────────┬──────────────┘
                                    │
              ┌──────────────────────┴──────────────────────┐
              │                                                │
┌─────────────▼─────────────┐                  ┌───────────────▼───────────────┐
│      Shared database           │                  │      Background job queue          │
│  (every shop's data, scoped      │                  │  (low-latency pool +                │
│   per-tenant)                     │                  │   bulk/throughput pool)              │
└───────────────────────────┘                  └───────────────┬───────────────┘
                                                                  │
                                                  ┌────────────────▼────────────────┐
                                                  │  Reminders · rebooking nudges ·    │
                                                  │  gap-fill · win-back · AI SMS        │
                                                  │  conversation handling                 │
                                                  └────────────────────────────────┘

     +  Optional integrations, every one with a working fallback when unconfigured:
        email delivery · image storage · payment processing · SMS/WhatsApp · AI assistant
```

**Key architectural decisions:**

- **Shared-database, shared-schema multi-tenancy** — one database serves
  every shop, with tenant isolation enforced at the application layer on
  every request. This is the standard, cost-efficient approach at this
  stage of a multi-tenant product.
- **Background jobs run on a dedicated worker system, not inline in
  request handlers** — split into a low-latency pool (for
  time-sensitive work like AI conversation replies) and a bulk pool (for
  large scheduled sweeps), so a big daily job can never delay a live
  interaction. Falls back to safe inline execution in a minimal local
  setup, so the app is easy to evaluate with no extra infrastructure.
- **AI as one channel into a shared, rule-enforcing booking engine** — the
  SMS/WhatsApp assistant calls the same validated booking functions the
  web UI does, rather than having its own separate logic path.
- **Every paid third-party dependency is optional at runtime** — the app
  is fully exercisable end-to-end with zero external services configured.

*(This section is intentionally high level. Detailed data models, service
internals, and infrastructure manifests are part of the private
codebase.)*

---

## Tech stack

| Layer | Technology |
|---|---|
| Application | Next.js (App Router), TypeScript |
| Database | PostgreSQL (Prisma ORM) |
| Background jobs | Redis-backed job queue, dedicated worker processes |
| AI booking assistant | Claude API (agentic tool-use loop) |
| Payments | Stripe (one-off checkout, subscriptions, hosted Checkout) |
| Email | Transactional email provider, with fallback |
| SMS / WhatsApp | Twilio, with fallback |
| Image storage | Cloudinary, with fallback |
| Styling | Tailwind CSS |
| Infrastructure | Docker, Docker Compose, Kubernetes, autoscaling |
| CI/CD | GitHub Actions |
| Testing | Playwright (end-to-end) |

---

## Security posture

A full internal security review has been performed covering authentication
(password hashing, session/token handling, OTP verification), rate
limiting, tenant data isolation, and payment verification (all payment
results are re-verified server-side, never trusted from the client).
Findings and deliberate hardening trade-offs are tracked internally, and
the review process is repeated on an ongoing basis as the product evolves.

Detailed implementation write-ups are intentionally not published in this
public repository. A summary is available on request as part of technical
due diligence.

---

## Project status

This is an actively developed, feature-complete product undergoing final
hardening ahead of public launch.

- [x] Multi-tenant booking core (availability, recurrence, roles)
- [x] Automated reminders, rebooking nudges, gap-fill, and win-back automations
- [x] AI-powered SMS/WhatsApp booking assistant
- [x] No-show risk scoring
- [x] Client payments, recurring memberships, gift cards & packages
- [x] Waitlist, coupons, reviews, deposits, guest checkout
- [x] Platform admin console
- [x] Automated end-to-end test suite & CI
- [ ] Public production deployment — **coming soon**
- [ ] Public demo video / walkthrough — **coming soon**

---

## Screenshots

> 📸 Screenshots coming soon. See [`screenshots/`](./screenshots) for the
> planned layout and the list of pages to capture.

<!--
Once available, embed them like:
![Booking flow](./screenshots/booking-flow.png)
-->

## Demo video

> 🎥 A walkthrough demo video will be linked here once recorded.
>
> [Watch the demo](#) *(link coming soon)*

## Live demo

> 🔗 A hosted live demo will be linked here once deployed.
>
> [Try Queueva](#) *(link coming soon)*

---

## Contact

Queueva is available for **licensing, acquisition, or investment**.
If you'd like a private code walkthrough, a live demo, or access to the
private repository under NDA, please reach out.

- **Email:** your-email@example.com
- **LinkedIn:** [your LinkedIn profile](#)
- **Website:** [your site](#)

---

<div align="center">
<sub>© 2026 Queueva. All rights reserved. This repository contains product documentation only.</sub>
</div>
