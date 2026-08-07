# Product decisions — ChaiShai

This document explains **why** we made important product choices. It is written for hiring teams and interview conversations — not as a technical spec.

For implementation detail, watch the [demo video](https://github.com/puniti1511/chaishai-demo/releases/download/v1.0/chaishai-demo.mp4) or ask in an interview; you should expect the builder to explain outcomes in plain language.

---

## 1. Phone-only sign-in (no email or social login)

**Decision:** Users sign in with a US mobile number and a text-message code. No “Sign in with Google” or email/password.

**Why:**

- In this community, **phone numbers are the real identity layer** — WhatsApp, SMS, and calls are how people coordinate.
- A verified phone number feels more accountable for **in-person gatherings** than a throwaway email.
- Invites are matched to phone numbers when guests accept, so one identity path keeps the funnel simple.

**Tradeoff:** We give up email-based marketing for now. Push notifications and SMS cover the moments that matter (RSVP updates, waitlist offers, reminders).

**Canada:** Country choice was added at registration in 2026 — see Decision 13 for full details.

**What we skipped:** Social login would speed up signup but would require extra work to tie social accounts to phone numbers for invites. Not worth it for MVP.

---

## 2. Language as a structured filter (not free text)

**Decision:** Events and profiles use a **fixed list of 16 language options** covering 14 South Asian languages plus English and Other (events may also use “Not applicable”), not an open text field.

**Why:**

- **Language is the #1 fit signal** for many diaspora users — a Gujarati family and a Tamil family may share “South Asian” labels but want different gatherings.
- Structured languages enable: auto-filtering Discover from profile preferences, clearer analytics (“which communities are active?”), and consistent search.

**Tradeoff:** The list will miss some languages at first. Adding more later is a product update, not a redesign.

---

## 3. Privacy rules enforced on the server (not “hidden in the app only”)

**Decision:** Rules like “private events only for invited guests” and “blocked users can’t see each other’s events” are enforced **in the database**, not only by hiding buttons in the app.

**Why:**

- For **in-person meetups between strangers**, a privacy mistake is serious (e.g. leaking a home address or guest list).
- App-only checks can be bypassed if someone adds a new screen or tool later. Server-side rules apply everywhere.

**How to talk about it in an interview:** “We treated privacy as a **trust requirement**, not a UI detail. I prioritized rules that hold even if the client is wrong or someone queries data directly.”

**Tradeoff:** Harder to debug than “filter in code,” but the right call for this product category.

---

## 4. Waitlist with partial offers (not a blind queue)

**Decision:** When a spot opens on a **full** public event, the next waitlisted guest gets an **offer** — including how many spots are available. If their party is larger than what opened, we offer a **partial** spot and give them **hours to accept or pass** (default: 4 hours).

**Why:**

- Gatherings are often **family or group RSVPs**. Silently skipping a party of 4 because only 1 spot opened feels unfair and confusing.
- Transparency (“1 spot open, your party is 3 — take it or pass to the next person?”) respects guest choice.

**What we fixed in review:** Two guests accepting at the same time could overfill an event. We added server-side locking so only one acceptance wins at a time.

**What we skipped:** Automatically splitting a party (“1 goes now, 2 stay waiting”) — too many social edge cases for MVP.

---

## 5. AI concierge updates Discover filters (not a separate search app)

**Decision:** When someone asks in natural language (“Punjabi potluck this weekend”), the app **turns that into the same filters** Discover already uses — languages, dates, radius, kid-friendly, etc. There is no separate “AI results page.”

**Why:**

- **One mental model:** Users still see the familiar Discover feed, just filtered smarter.
- **Transparency:** They can see and tweak what the AI chose.
- **Cost control:** Simple queries can use rules without calling OpenAI; signed-in users get richer AI; anonymous users are rate-limited.

**Tradeoff:** The concierge can only filter on dimensions we already support. It can’t yet answer vague social questions like “events where newcomers feel welcome” unless we add that data later.

**Relevant for AI PM roles:** This is a deliberate **AI-as-layer** design — structured output, fallback path, abuse limits — not “chatbot bolted on.”

---

## 6. Browse before sign-in (with address protected)

**Decision:** Anyone can scroll **public** events on Discover without an account. They see neighborhood-level location, not the full address, until they sign in (and RSVP where required).

**Why:**

- Requiring signup before showing **any** events creates a dead end for first-time users.
- Gating the **full address** protects hosts and gives a clear reason to create an account.

**Tradeoff:** We learn less about anonymous browsers until they sign up.

---

## 7. One date per event for MVP (recurring later)

**Decision:** Hosts create **one gathering at a time**. The system is prepared for recurring events later, but the “repeat every week” UI is **off** for beta.

**Why:**

- Recurring series raises hard product questions: RSVP per date or whole series? Waitlist per occurrence? One invite link or many?
- Beta feedback will tell us if weekly chai circles matter more than one-off potlucks and festivals.

**Host message today:** “For recurring gatherings, create a new event for each date.”

---

## 8. Trust signals on Discover cards (not only on the detail page)

**Decision:** Each event card shows **compact host trust cues** (e.g. verified phone, hosting history) before you open the event.

**Why:**

- Trust only helps if you see it **before** you commit attention — same idea as ratings visible in search on marketplace apps.
- Reduces taps on events that are a bad fit for risk tolerance or experience level.

---

## 9. Messaging only for confirmed attendees

**Decision:** In-app chat between host and guest is available only when the guest is **Attending** — not for “Maybe,” waitlisted, or browse-only users.

**Why:**

- Open messaging on public events is a **harassment and spam** risk.
- Chat should support **coordination among people who are actually going**, not cold outreach from strangers.

**Product surface:** “Message host” and optional RSVP note appear only after Attending is selected.

---

## 10. Staging build before wide beta

**Decision:** Separate **staging** (internal test app) and **production** (store) builds, with automated checks on every code push.

**Why:**

- Real-world failures hurt: wrong RSVP state, missed cancellation, broken push = people show up at the wrong place or time.
- Push notifications and SMS login **don’t work in the same way** in a developer preview app; testers need a real installable build.

**Tradeoff:** More environments to configure — documented in the private codebase.

---

## 11. Discover anchored to profile home ZIP (local by default)

**Decision:** Every member sets a **home ZIP** at registration. Discover ranks and filters public events from that ZIP within a default **15-mile radius** (adjustable in filters). Preferred languages on the profile sync to Discover filters.

**Why:**

- Community gatherings are **local** — a parent in a new suburb needs “what’s near me,” not a national event list.
- One profile field powers the header location, default search, and distance sort — fewer confusing settings.
- Hosts can still run **private** events that never appear on the open Discover feed.

**Tradeoff:** Members who forget to update ZIP after a move may see the wrong area until they change Profile or Discover filters. We accept that over asking for GPS on every open.

**What we skipped:** Auto-detect city from GPS as the default (device permission friction); GPS can complement later, but **home ZIP is the source of truth** for MVP.

---

## 12. In-app admin KPIs (database) + optional PostHog

**Decision:** Core platform metrics (events created, RSVPs, attendees, invites sent/opened, waitlist, messages, trust reports) live in **Admin → App insights**, backed by Supabase RPCs with date-range filters. **PostHog** is optional (env key only) for product analytics trends — not required for beta.

**Why:**

- **Source of truth** for ops and beta review should match the database, not only client-fired events.
- Admins (trust admins) can check KPIs **inside the app** without a separate analytics login.
- PostHog adds funnels and retention later without blocking launch when dashboards aren’t configured yet.

**Tradeoff:** Two systems can diverge slightly (client events vs SQL counts). Admin insights wins for accuracy; PostHog wins for behavioral funnels over time.

**What we skipped:** Full PostHog setup as a beta gate; some events (e.g. invite link opened) are server-logged in Supabase and may not mirror PostHog until explicitly instrumented.

---

## 13. Canada expansion (profiles, events, and Discover)

**Decision:** Users pick **United States or Canada** at registration. Profile stores `home_country`, postal/ZIP, and +1 phone. **Create event** uses US states or Canadian provinces (and city lists) from the host’s country. **Discover** geocodes the member’s home ZIP/postal (or filter override) with country-aware geocoding and the same radius/date/language filters.

**Why:**

- Diaspora communities exist in **Toronto, Vancouver, and other Canadian metros** — hosts and guests there need signup, hosting, and a **local feed** that respects postal codes.
- **Country at registration** keeps phone (+1), postal validation, and event pickers consistent without mixing US states and Canadian provinces in one form.
- **One Discover model** — same radius and filter UX; only geocoding suffix and postal validation differ by country.

**Tradeoff:** Canadian postal geocoding depends on the device geocoder (less strict than US 5-digit ZIP). Event density in Canada may be lower at launch; hosts can still share invite links.

**What we skipped:** Separate metro launch gates, km-based radius (still miles for both countries), and strict Canadian postal format validation.

Migration **064** adds `profiles.country` and relaxes postal constraints for CA.

---

## Security review (product framing)

Before beta, we ran structured reviews and fixed issues such as:

| Issue | Product risk | What we did |
|-------|----------------|-------------|
| Any website could trigger sensitive APIs | Abuse of login / invites from malicious sites | Restrict which web origins can call our APIs |
| Open AI search endpoint | Runaway API cost; spam | Require app credentials; rate-limit; limit AI to signed-in users where appropriate |
| Test mode leaking login codes in production | Account takeover in live environment | Block test shortcuts on live servers |
| Two waitlist guests accepting at once | Overbooking vs host capacity | Serialize acceptances on the server |
| Host name search exposing extra profile data | Privacy leak while filtering | Return only host IDs needed for search, not full profile rows |

You don’t need to recite implementation details — the point is **identify risk → prioritize → verify fix**.

---

## What I’d discuss in a Lead Product AI interview

1. **Audience insight** — Why generic event apps fail this community.  
2. **AI scope** — Concierge as structured filter layer + fallback + limits.  
3. **Trust & safety** — Privacy and messaging gates for IRL gatherings.  
4. **Tradeoffs** — Phone-only auth, deferred recurrence, staging before beta, local ZIP/postal discovery, Canada expansion with shared Discover UX.  
5. **Metrics** — In-app admin KPIs for beta; PostHog optional for growth analytics.  
6. **How I worked** — Product direction and review; implementation accelerated with Cursor and documented decisions here.

---

*Last updated: July 2026 — aligned with migrations through 064; Discover supports US + Canada.*
