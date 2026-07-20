# Founder Inbox

**An AI-native, omnichannel engagement inbox for founders.** Every place people
talk *to or about you* — Twitter replies and DMs, LinkedIn, email, Discord, your
in-app widget, GitHub — collapsed into one triaged stream, researched, and
answered in your voice.

> Working name — placeholder until we settle on a brand.

> **This is a fork of [cossistant](https://github.com/cossistantcom/cossistant)**
> (AGPL-3.0). We build on their inbox, real-time messaging, and AI-agent
> foundation. See [Credits & License](#credits--license).

---

## Why

Founders have no support team — they *are* the support, sales, community, and
recruiting team. The pings arrive everywhere at once, and the job isn't clearing
a queue. It's not missing the five interactions that matter out of two hundred,
and responding like a human, fast.

Existing tools miss this. Zendesk assumes a queue of agents clearing tickets.
Missive and Front are team inboxes without a triage brain. Intercom is
support-shaped. None are built for a founder who needs aggressive noise
reduction, research on who's reaching out, and drafts that sound like them.

## What it is

Cossistant's conversation model and AI agents become the **triage brain**, fed by
a **channel gateway** that normalizes every platform into one stream. The
embedded chat widget stops being the product and becomes one channel among many.

## Architecture

```
[Twitter/X · LinkedIn · email · Discord · IG · web widget · GitHub …]
        │  webhook (push)  or  worker poll (pull)
        ▼
  Inbound gateway  ──►  normalize to ONE event schema (Zod)
        │                { channel, externalThreadId, externalUserId,
        │                  author, content, permalink, timestamp, raw }
        ▼
  Persist as conversation / message   (cossistant's model)
        ▼
  Triage agent:  classify intent · sentiment · priority
                 research the person (identity resolution + history + context)
                 route → auto-draft  or  human review
        ▼
  Outbound dispatch  ──►  reply through the ORIGIN channel's API
```

Webhooks where a platform supports them; worker polling where it doesn't. The
event schema is the same either way, so the agent never cares how a message
arrived.

## Feature suite

The layers double as the build order — each is useful before the next exists.

**Layer 0 — Unified inbox (the spine).** Every channel normalized into one
thread view. Merge by *person*, not by channel: a DM on X and an email next week
are one conversation. Claim/assign among founders so nothing is double-handled.

**Layer 1 — AI triage + research.** Per inbound: classify intent (lead /
support / bug / press / investor / recruit / fan / noise), score priority and
sentiment, and attach a research card — who they are, their company, whether
they're already a customer, past interactions, which post they engaged with.
Bulk "clear the noise" auto-dispositions the low-value 90%.

**Layer 2 — AI drafting in your voice.** Per-founder voice profiles. AI drafts,
a human approves and sends. Never fully automated — see [What it's not](#what-its-not).

**Layer 3 — Relationship CRM-lite.** Interactions are relationships, not
tickets. Lightweight contact + company records, a stage (lead / design partner /
investor / hire / partner), follow-up reminders and "waiting on them" states so
warm threads don't die.

**Layer 4 — Proactive engagement.** Not only reactive. Surface conversations
worth jumping into — relevant posts where a founder showing up creates pipeline
or goodwill. The find-and-engage bridge between the response loop and marketing.

**Layer 5 — Intelligence + briefing.** Recurring themes feed product-feedback
mining and content ideas. A daily digest: "3 leads, 2 bugs, 1 podcast invite,
sentiment up."

## MVP

Layers 0–2 across **email + Twitter DMs/mentions + the web widget**: unified
inbox, AI triage with a research card, voice-drafted replies, and claim/assign
between founders. Everything else is additive.

## What it's not

- **Not a Zendesk** — no ticketing SLAs, CSAT surveys, macros, or help-center CMS.
- **Not full auto-reply.** A founder's authenticity is the moat. AI drafts; a
  human sends. The human-in-the-loop is a feature, not a limitation.
- **Not a social scheduler.** Buffer / Typefully own publishing. This is the
  reply-and-relationship layer, not the posting layer.
- **Not every channel at once.** Depth on the 3–4 that drive real interactions
  beats shallow coverage of ten.

## Channel reality

Coverage is gated by each platform's API, not by our architecture.

| Tier | Channels | Notes |
|------|----------|-------|
| Clean | Email, web widget, WhatsApp (Meta Cloud API), SMS | Webhooks + send APIs both exist |
| Gated / paid | Twitter/X, Instagram + Facebook (Meta) | Paid API tiers; Meta needs app review + business account |
| Hard / closed | LinkedIn organic engagement | No sanctioned API to read/reply to post comments without partner access — likely needs an aggregator (Unipile, Nango) |

## Status

Early. Forked from cossistant on 2026-07-20. Roadmap above; MVP first.

## Tech stack

Inherited from cossistant, and a deliberate match for our other repos:

- Monorepo (Turborepo), Bun
- React & Next.js, TypeScript
- Hono (API), tRPC
- Drizzle ORM, Postgres + pgvector
- Better Auth
- TailwindCSS
- WebSockets
- Docker (Postgres + Redis)

## Credits & License

This is a fork of **[cossistant](https://github.com/cossistantcom/cossistant)**
by Productized Inc. — an open-source chat support widget for the React ecosystem
([cossistant.com](https://cossistant.com)). The inbox, real-time messaging, and
AI-agent foundation are theirs; the founder-inbox direction above is ours.

Licensed under **[AGPL-3.0](https://opensource.org/licenses/AGPL-3.0)**, inherited
from upstream — see [`LICENSE`](./LICENSE) (unchanged). What that means in
practice: if you deploy a **modified** version that users interact with over a
network, you owe *those users* the source of your modifications — not your whole
product. To keep modifications private, upstream sells a commercial license
([anthony@cossistant.com](mailto:anthony@cossistant.com)).
