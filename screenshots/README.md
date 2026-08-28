# Screenshots

This folder is where product screenshots go once captured. Add images here
(PNG or JPG, 1400px+ wide recommended) and reference them from the main
[`README.md`](../README.md) using:

```md
![Booking flow](./screenshots/booking-flow.png)
```

Suggested filenames match the page names below — keep them consistent so
the README embeds stay tidy.

## Pages worth capturing

Pick the strongest 6–10 for the README itself; keep the rest here for a
future "full gallery" section or for investor/demo decks. Capture each
using one of the seeded demo shops (salon / gym / repair shop) so the
content looks real without exposing any actual customer data.

**Marketing / public**
- `landing-page.png` — the public marketing homepage
- `shop-page.png` — a public shop page (e.g. the seeded salon)
- `booking-flow.png` — the booking page showing real, live availability slots
- `gift-cards-public.png` — the public gift-card purchase page
- `memberships-public.png` — the public membership plans/subscribe page

**Client experience**
- `client-signup-otp.gif` — a short GIF of signup → OTP → account flow
- `client-account.png` — a client's upcoming/past bookings view
- `payment-checkout.png` — the appointment payment page
- `review-flow.png` — the post-appointment review page

**Owner dashboard (most important — lead with these)**
- `dashboard-overview.png` — the main owner dashboard
- `dashboard-appointments.png` — appointments list with status actions
- `dashboard-services.png` — service catalog management
- `dashboard-staff.png` — staff management
- `dashboard-waitlist.png` — waitlist view
- `dashboard-coupons.png` — coupon management
- `dashboard-memberships.png` — membership plan management
- `dashboard-gift-cards.png` — gift card management
- `dashboard-packages.png` — package/bundle management
- `dashboard-reviews.png` — reviews management
- `dashboard-analytics.png` — analytics view
- `dashboard-settings.png` — shop settings (hours, branding, AI assistant toggle)

**AI booking assistant**
- `ai-sms-conversation.png` — a screenshot (or mocked transcript) of an SMS/WhatsApp conversation booking an appointment through the AI assistant

**Platform admin**
- `admin-tenants.png` — the super-admin view listing every shop, with suspend/reactivate controls

**Nice-to-have**
- `mobile-booking.png` — the booking flow on a mobile viewport
- `architecture-diagram.png` — an exported image of the architecture diagram from the README

## Before you capture

- Use **seeded/demo data only** — no real client names, emails, phone
  numbers, or payment details.
- Blur or replace anything that looks like a real secret (API keys,
  webhook URLs) even if it's fake — reviewers shouldn't have to guess
  whether it's real.
- Keep browser chrome out of the shot (or use a consistent, clean frame) so
  screenshots feel like product shots, not raw dev-environment captures.
