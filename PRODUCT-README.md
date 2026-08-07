# ChaiShai — Product overview

**Community gatherings for South Asian diaspora in the United States and Canada.**

ChaiShai is a mobile app for discovering and hosting in-person events — chai meetups, potlucks, religious observances, family gatherings, and language-based meetups — built for South Asian communities across North America.

> **Product-led build, preparing for beta.** End-to-end ownership from problem definition through feature prioritization, security review, and beta prep. Built with AI-assisted development tools (Cursor) for implementation speed; product decisions and review criteria are documented in [DECISIONS.md](DECISIONS.md).

**Demo:** [Watch the product walkthrough (MP4)](https://github.com/puniti1511/chaishai-demo/releases/download/v1.0/chaishai-demo.mp4)

*Source code is private. This document describes scope and context for reviewers.*

---

## The problem

South Asian diaspora communities in the US are large, geographically scattered, and culturally specific. Generic event platforms (Eventbrite, Partiful, Facebook Groups) don’t distinguish a Navratri gathering from a general mixer. They rarely filter by language. They treat a 10-person chai meetup and a 500-person conference the same way.

**People don’t get an easy way to connect with each other** — especially after a move or when your existing circle is small. There is no simple, trusted place to find “people like me, near me, this weekend” without already knowing someone who knows someone.

For **parents who come to the US** (visiting family, on a spouse visa, or settling in a new city), the gap is even sharper. Community life often depends on **WhatsApp groups you were never added to** — temple circles, school parents, neighborhood chats. Without those invites, days can feel long and isolating; there is little to join beyond waiting for a relative to forward a link. Boredom and disconnection are common, not because the community isn’t there, but because **discovery is locked inside private chats**.

In practice, coordination falls back to those same **WhatsApp group chats** — great for close circles, but weak for **discovery**, **new members**, and **people across a metro area** who don’t already know the host.

ChaiShai is designed for how this community actually plans gatherings: **language**, **cultural event type**, **trust in the host**, and **safety for meeting strangers in person** — so newcomers and parents can **find gatherings to walk into**, not just hear about them after the fact.

---

## What makes it different

| Area | What we built | Why it matters |
|------|----------------|----------------|
| **Language-first discovery** | 16 language options covering 14 South Asian languages plus English and Other (Hindi, Punjabi, Tamil, Gujarati, and more) | Language is often the strongest signal for “will I feel at home here?” |
| **Cultural event types** | Potluck, religious/cultural, birthday, kids & family, general gathering | Matches how hosts describe events, not generic “networking” labels |
| **Trust on the card** | Host signals (verified phone, past gatherings) visible before you tap in | Safety and fit decisions happen early, not only on the detail page |
| **AI discovery (Concierge)** | Natural language search → “Find Punjabi family events this weekend near me” → applied as normal Discover filters | Relevant for **Lead Product AI** roles: intent parsing, guardrails, cost control |
| **Smart waitlist** | When a spot opens, guests with larger parties get a **partial offer** and time to accept — not a silent skip | Families often RSVP together; the product should respect that |
| **Guest browse** | See public events before sign-in; full address after login | Lower friction to understand value before creating an account |
| **Local discovery** | Profile **home ZIP/postal** + default **15 mi** radius (adjustable in Discover) | Events shown near where you live — not a random national feed |
| **US + Canada profiles** | Country chosen at registration; province/state + city on events; +1 phone for both | US and Canadian members get local Discover from home ZIP/postal + radius |

---

## Discover near you (profile location)

When someone completes registration, they set a **home ZIP or postal code** and **preferred languages** on their profile. Discover uses that setup by default:

- **Distance** — Events are ranked and filtered from the member’s **home ZIP or postal code** (geocoded to map coordinates), within a default **15-mile** radius. They can change location or radius in Discover filters (e.g. while visiting family in another city). Works for **US and Canada** based on profile country.
- **Languages** — Profile language preferences sync to Discover filters so the feed prioritizes gatherings that match how they actually socialize.
- **Public listings only** — Discover shows **public** upcoming events. Private home gatherings stay off the open feed unless the guest is invited.

This keeps community **local and relevant** — a parent in a new suburb sees what’s happening **near them**, not every event in the country.

---

## Core user journeys

1. **Discover** — Events near your profile ZIP/postal (or a filter override), within your radius, plus date, language, event type, audience, or host name. Optional AI concierge turns a sentence into filters.
2. **RSVP** — Attending / Maybe / Not interested; join waitlist when an event is full.
3. **Invite → login → event** — Host sends link; guest opens on phone, signs in with SMS, lands on the event.
4. **Host tools** — Create event, manage guests, message attendees, send invites, see basic invite funnel analytics.
5. **Trust & safety** — Report, block, hide; private events stay private to invited guests.

---

## Trust & safety at in-person gatherings

ChaiShai is **community-focused, not ethnicity-gated**. Anyone can register with a verified phone (+1 US/Canada); cultural fit comes from **language and event type**, not blocking signup by background. Safety is **layered** — especially when strangers meet in person.

**Accountability**

- **Phone verification** — Sign-in requires a mobile number and SMS code (account tied to a real phone, not a throwaway username).

**Information control**

- **Staged locations** — Browse shows neighborhood/ZIP area only; **full address** unlocks after sign-in.
- **Private events** — Hosts can run **invite-only** gatherings; access is enforced in the **database** (not just hidden in the app).
- **Guest list privacy** — Hosts choose whether names are visible to all guests or only to the host.

**Before and during an event**

- **Trust on the card** — Verified phone and hosting history visible before RSVP.
- **Host sees who’s coming** — Guest list with names, party size, and optional notes (and phone for accepted guests when coordinating).
- **Messaging gated** — Guests can **message the host only after Attending** — no cold DMs from browsers or “Maybe” RSVPs.

**If something feels wrong**

- **Report** events or users (misleading, unsafe, harassment, spam, inappropriate for families, etc.).
- **Block** — Blocked users disappear from your view; messaging stops (server-enforced).
- **Hide** — Remove an event from your feed without notifying the host.
- **Admin moderation** — Trust admins review reports in-app (Profile → Admin → Moderation queue).

**What we don’t claim (MVP)**

- No background checks or ID verification.
- No automatic “approve every RSVP” on public events — hosts who want tight control should use **private events + invite lists**.

For sensitive gatherings (home chai, kids’ playdates), the product guidance is: **private event, invite list, review the guest list before the day of.**

---

## How I worked on this project

This project is meant to show **product judgment**, not just code volume.

- **Problem and audience** — Defined who the app is for and what generic tools miss.
- **Prioritization** — Shipped MVP paths (Discover, RSVP, invites, waitlist, messaging) and deferred recurrence UI until post-beta feedback.
- **Security review** — Ran structured reviews before beta; identified and fixed issues such as open AI endpoints, overly permissive browser access to sensitive APIs, and waitlist race conditions when two guests accept at once.
- **AI product choices** — Concierge applies filters to the existing Discover feed (one mental model), with rules-based fallback and limits on anonymous usage to control cost.
- **Documentation** — [DECISIONS.md](DECISIONS.md) explains *why* for major choices in language you can discuss in an interview.

**Stack (high level):** Expo React Native mobile app + Supabase (Postgres, auth, edge functions). Technical deep-dives available on request in interview.

---

## Product decisions

See **[DECISIONS.md](DECISIONS.md)** — 13 documented decisions with reasoning, tradeoffs, and what we intentionally did *not* build.

---

## Status

- **Beta:** Live with 5 users actively testing across initial metros
- **CI:** Automated typecheck + lint on every push (private repo)
- **Security:** Pre-beta review completed; fixes deployed
- **Source code:** Private – technical deep-dive available on request in interview

---

## App architecture (high level)

```
Mobile app (Expo)     Discover, Create, Profile, RSVP, invites, messaging
Backend (Supabase)    Postgres + RLS, phone OTP, push, AI concierge
```

*Implementation detail and schema are in the private codebase.*
