# Nonprofit Cultural Platform — Ghost Build Spec
**Prepared for:** Nonprofit client + Ghost developer
**Purpose:** 30-day lightweight prototype
**Author:** VP Engineering review of prior ChatGPT stack discussion

---

## 1. Engineering Assessment

**Verdict: directionally correct, but needs to be cut down for a 1-month volunteer-run prototype.**

What the ChatGPT conversation got right:
- Ghost is a reasonable, low-maintenance CMS for this use case — good editorial tools, native Vimeo/SoundCloud embeds, 0% transaction fee, cheap hosting.
- Separating "content" (Ghost) from "money" (a payment processor) is the correct architectural instinct.
- Treating Events, Journal, and Installations as three related content types is a clean model.

Where it drifted, and why that's a risk for a 1-month, no-staff, volunteer-run build:
- **Too many vendors were left "on the table."** The thread cycles through Eventbrite, Stripe, Shopify, and a hypothetical custom ticketing system without ever landing on one answer. Every added vendor (Eventbrite, Shopify) is more monthly cost, more logins, more things a volunteer has to learn and maintain.
- **AR is scope creep for v1.** A geo-triggered AR layer is a genuinely good long-term idea, but it's a separate engineering project (device compatibility, hosting for 3D/AR assets, testing). It should not be in a 30-day prototype.
- **Eventbrite adds cost without adding much value at small scale.** For a volunteer org with modest ticket volume, Eventbrite's ~3.7% + $1.79/ticket + 2.9% is meaningfully worse than Stripe's flat 2.9% + $0.30, and Eventbrite is a second system to maintain. Skip it for v1.
- **Shopify is unnecessary at launch.** A handful of merch items can sell through Stripe Payment Links with no cart/inventory software at all.

**Recommendation: cut the stack to two products for v1 — Ghost + Stripe.** Nothing else. This is the leanest version that still delivers events, ticket sales, donations, simple merch, editorial content, and a placeholder for "Installations" (QR-triggered pages, no AR yet).

---

## 2. Recommended Stack (v1 — 30-day prototype)

| Layer | Tool | Fee |
|---|---|---|
| Website / CMS | Ghost (Starter/Creator plan) | Flat monthly, 0% of sales |
| Theme | One custom Ghost theme (DICE-inspired) | One-time build |
| Tickets, merch, donations | Stripe Payment Links + Checkout | 2.9% + $0.30/transaction |
| Ticket delivery | Stripe email receipt (v1) → QR ticket (v2) | — |
| Video / audio | Vimeo & SoundCloud embeds (native to Ghost) | Free tier |

**Explicitly out of scope for v1:** Eventbrite, Shopify, AR, GPS-based triggers, custom ticket-inventory system, donor CRM. All listed in Section 8 as Phase 2.

---

## 3. System Architecture

See attached `architecture.mermaid` for the diagram. Summary:

```
Ghost (content + admin)
   └─ Custom Ghost Theme
        ├─ EVENTS      → Stripe Payment Link (metadata: type=TICKET)
        ├─ JOURNAL      → Vimeo / SoundCloud embeds
        ├─ INSTALLATIONS → QR sign → permanent Ghost URL (/installations/slug)
        └─ SUPPORT      → Stripe Payment Link (metadata: type=DONATION)
```

Everything is one Ghost site with one theme. The only external system is Stripe. A volunteer manages 100% of content from the Ghost admin panel; no developer is needed for day-to-day updates after launch.

---

## 4. Ghost Content Model

Use Ghost's native **Posts + Tags + Custom Excerpt/Feature Image**, no custom code needed for data — just theme templates that key off tags.

| Content type | Ghost object | Required tag | Key fields (custom fields via Ghost's built-in "Post settings" + code injection where needed) |
|---|---|---|---|
| Event | Post | `#event` | Hero image, date/time, venue, address, ticket Stripe link, short description |
| Journal | Post | `#journal` | Hero image, body (text/Vimeo/SoundCloud embed) |
| Installation | Post | `#installation` | Hero image, open date, close date, location name, QR target URL (its own permalink), status (upcoming/active/archived — set manually by volunteer) |
| Donation | Page (single, static) | — | Stripe donation link, short mission copy |
| Merch (optional, only if ready by launch) | Post | `#merch` | Product photo, price, Stripe Payment Link |

**Note on "status" for Installations:** for v1, keep this manual — a volunteer just re-labels the post "Upcoming / Active / Archived" and swaps the hero copy. No date-logic automation needed yet; that's a Phase 2 nicety.

---

## 5. Page Wireframes

### Homepage
```
┌──────────────────────────────────────────────┐
│ LOGO         EVENTS  JOURNAL  INSTALLATIONS ☰│
├──────────────────────────────────────────────┤
│            FEATURED EVENT (hero image)        │
│            Event Name · Date · Venue           │
├──────────────────────────────────────────────┤
│ UPCOMING EVENTS                                │
│  [img] Event Name      Date · Venue            │
│  [img] Event Name      Date · Venue            │
├──────────────────────────────────────────────┤
│ HAPPENING NOW: INSTALLATIONS                   │
│  [img] Installation Name   Active · X days left│
├──────────────────────────────────────────────┤
│ FROM THE JOURNAL                               │
│  [img] Story title                             │
├──────────────────────────────────────────────┤
│ SUPPORT US → Donate button                     │
└──────────────────────────────────────────────┘
```

### Event page
```
┌──────────────────────────────────────────────┐
│              LARGE EVENT IMAGE                 │
├───────────────────────┬──────────────────────┤
│ DESCRIPTION            │ EVENT NAME             │
│ Artist info             │ DATE / TIME            │
│                         │ VENUE                  │
│                         │ [ GET TICKETS ] → Stripe│
└───────────────────────┴──────────────────────┘
```

### Installation page (QR destination)
```
┌──────────────────────────────────────────────┐
│   STATUS BADGE: Upcoming / Active / Archived   │
│              LARGE IMAGE / ARTWORK             │
├──────────────────────────────────────────────┤
│  Installation Title                            │
│  Artist · Location · Dates                     │
│                                                │
│  [Story text / audio embed / video embed]      │
│                                                │
│  Related: Event · Journal story (optional)     │
└──────────────────────────────────────────────┘
```
QR code always points to this same URL (`/installations/slug`) permanently — the page content changes over the installation's lifecycle, the sign never has to be reprinted.

### Journal post
```
┌──────────────────────────────────────────────┐
│              HERO IMAGE                        │
│  Story Title                                   │
│  Body text, Vimeo embed, SoundCloud embed      │
└──────────────────────────────────────────────┘
```

### Support / Donate (static page)
```
┌──────────────────────────────────────────────┐
│  Why your support matters (2–3 sentences)      │
│  [ DONATE ] → Stripe Payment Link              │
│  501(c)(3) / EIN disclosure line               │
└──────────────────────────────────────────────┘
```

---

## 6. Payment & Bookkeeping Flow

- Create **one Stripe account** for the org.
- Create separate **Stripe Payment Links** per event, per merch item, and one recurring "General Support" donation link.
- Tag every link with **metadata** (`type: TICKET / DONATION / MERCH`, plus event name) — this is what lets a volunteer export a clean CSV from Stripe and sort revenue by category for the general ledger.
- Stripe issues the buyer's receipt automatically. For donations, the org should still send a short manual thank-you email (Stripe's receipt is not a substitute for a proper donor acknowledgment).
- Digital/QR tickets are **not** in v1 — Stripe's emailed receipt/confirmation is the ticket for the prototype. Build real QR ticketing only if door check-in becomes a real requirement.

---

## 7. 30-Day Roadmap

| Week | Deliverable |
|---|---|
| 1 | Ghost site provisioned; custom theme skeleton (homepage, event template, journal template, installation template, static donate page) |
| 2 | Theme styling to match agreed visual direction; Stripe account + Payment Links set up and tested end-to-end |
| 3 | Content entry: volunteer populates 2–3 real events, 1 journal post, 1 installation (even if placeholder); QA on mobile |
| 4 | Buffer week: bug fixes, load one real QR code test for an installation, soft launch |

---

## 8. Phase 2 Backlog (explicitly deferred)

- Eventbrite or custom ticket-inventory system (only if ticket volume/complexity grows)
- Shopify (only if merch catalog grows past ~10–15 simple items)
- Digital/QR tickets for door check-in
- GPS-triggered (not just QR-triggered) Installation activation
- AR overlays
- Automated Installation status (auto-switch upcoming → active → archived by date)
- Donor CRM / automated tax-receipt emails

---

## 9. Open Questions for Client

1. Confirm 501(c)(3)/EIN status — needed to set up Stripe correctly and for the donation page disclosure copy.
2. Rough expected ticket volume/year — confirms Eventbrite is safe to skip for now.
3. Who is the one volunteer "content owner" who will get Ghost admin access and Stripe dashboard access?
