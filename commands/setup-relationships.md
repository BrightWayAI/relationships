---
description: Configure the relationships plugin for your ICP, tiers, voices, time-budget, and integrations. Writes results to <config-root>/plugins/relationships.user-context.md so the daily brief adapts to your context. Re-run anytime to update.
---

# /setup-relationships

Short interview that captures what the `relationships` plugin needs to produce a useful daily brief. Idempotent — re-running updates rather than restarts.

---

## Step 0 — Resolve plugin config root

Per-plugin config in this marketplace lives under a user-chosen folder, recorded at `~/Documents/.claude-plugin-config-root` (single-line text file in the user's home).

### A — Try the pointer

Ensure access to `~/Documents`. In Cowork, call `request_cowork_directory(~/Documents)` once if not already granted. In Claude Code (or any environment with direct filesystem access), no mount is needed. Then read `~/Documents/.claude-plugin-config-root`.

- **Exists:** read line 1 → that's the config root path. Ensure access to `<config-root>`. If running in Cowork and the folder isn't already mounted in this session, call `request_cowork_directory(<config-root>)`. If running in Claude Code or another environment with direct filesystem access, no mount call is needed. Skip to section C.
- **Missing:** continue to section B.

### B — First-time bootstrap

Prompt: "First-time plugin setup. Where should I store your plugin config — identity, voice, and per-plugin settings? Pick a folder you control (e.g., `~/Documents/Claude/` or `~/Documents/PluginConfig/`). The folder will hold `identity.md`, `voice.md`, and a `plugins/` subdirectory."

Then:
1. Ensure access to `<path>`. If running in Cowork and the folder isn't already mounted, call `request_cowork_directory(<path>)`. In Claude Code, no mount call needed.
2. Create `<path>/plugins/`. Write absolute path to `~/Documents/.claude-plugin-config-root`.

### C — Read shared identity

Read `<config-root>/identity.md` (cortex's `/setup-identity` output).
- **Populated** → pre-fill Section 1 (Identity).
- **Missing** → offer `/setup-identity` first or proceed inline.

### D — Read shared voice

Read `<config-root>/voice.md` (cortex's `/setup-voice` output).
- **Populated** → pre-fill Section 5 (Voices).
- **Missing** → offer `/setup-voice` first or proceed inline.

For the rest of this document, **`<config-root>`** refers to the resolved path. This plugin's config file lives at **`<config-root>/plugins/relationships.user-context.md`**.

---

## Step 1 — Check for existing config

Read `<config-root>/plugins/relationships.user-context.md`.
- **Populated:** ask whether to update specific sections or start over. If update: name the sections; let the user pick.
- **Missing:** start fresh.

---

## Step 2 — The interview

One section at a time. Confirm before moving on. **Don't ask every question if defaults are good for the user — surface the defaults and confirm.**

### Section 1 — Identity (pre-fill from `identity.md` if available)
- Your name
- Your company (or "solo" if no company)
- One-sentence description of what you do
- Your preferred first-name form for templates (e.g., "Zach" vs "Zachary")

### Section 2 — Business context and current focus
- **Primary ICP** — titles + org types + size range + region. Example: "Heads of Content / VP Production at mid-market EdTech and L&D, 10–200+ videos/year, US/EU"
- **Secondary ICP** — same shape, less hot
- **Out-of-ICP** — anything you explicitly never want surfaced
- **Current quarter focus** — what theme should weight prioritization? E.g., "Studio K-12 anchors" or "consulting pipeline for Q3"
- **Quarterly outcome target** (optional but helpful for prioritization): "2 Phase-1 anchors signed," "$150K new contracted," "5 qualified discovery calls"

### Section 3 — Tiers (configurable)

Defaults:

| Tier | Default size | Default cadence |
|---|---|---|
| Inner Core | 10–15 | every 14 days |
| Strategic | 30–50 | every 30 days |
| Operational | 100–200 | every 90 days |
| Dormant | rest | on trigger only |

Ask:
- "How many people are you actively trying to be close to right now? (your inner core)"
- "About how big is your strategic ring? (people you want monthly contact with)"
- "Any tier sizes or cadences you want to override?"
- "Do you want a separate **Close personal** track? (family + closest friends, surfaced on a separate tab in the daily brief)"
  - If yes: how big, what cadence

### Section 4 — Buckets and emphasis

The daily brief produces three buckets. Ask:
- Default weight per bucket (equal? prospecting-heavy? maintenance-heavy?) — defaults to equal.
- Should the brief always show all three, even if one is empty? Or hide an empty bucket?
- For **Network Expansion**, do you have **one voice** (e.g., one personal-brand LinkedIn) or **multiple** (e.g., personal brand + business brand)? Capture each named voice.

### Section 5 — Voices (pre-fill from `voice.md` if available)

For each voice the user named in Section 4:
- Three words describing the voice
- Additional banned phrases beyond cortex defaults (the default banned list already includes "just checking in," "circling back," "touching base," "I hope this email finds you well," "per my last email," "synergy/leverage/align," "any luck with [X]?")
- Sentence-length preference — short / mixed / longer
- Sign-off and CTA style

If a single voice was named, this becomes one section. If multiple, repeat per voice.

### Section 6 — Time budget

- Typical daily availability: 5 / 15 / 30 / 60+ min — pick the most common
- "Highly variable?" If yes: enable the time-budget prompt at the top of every `/relationships` invocation

### Section 7 — CRM

- Which CRM? (HubSpot / Salesforce / Pipedrive / Attio / other / "none")
- Your CRM owner ID (if applicable)
- **Deal pipeline stages** in order
- **Tier property** in CRM (if you tag tiers in CRM — e.g., `engagement_cadence`)
- **ICP-fit property** (e.g., `icp_fit_score`)
- **Do-not-engage property or status**
- API tier / capabilities — if user knows: any Sequences-API restrictions? (HubSpot Starter doesn't expose Sequences via API)

### Section 8 — Apollo and net-new prospecting

- Is Apollo connected? (Y/N)
- If yes: cap on net-new prospects surfaced per day (default 1) and per week (default 5)
- Any additional sources you want surfaced for net-new (events, LinkedIn engagement targets, intro requests, seasonal hooks)

### Section 9 — Companion plugins

- Is `cortex` installed? (Y/N — person pages, identity, voice; the foundation)
- Is `lead-engine` installed? (Y/N — drives `contact-researcher` delegation + signal taxonomy)
- Is `referral-engine` installed? (Y/N — drives relationship-bucket asks + cooling-period rules)
- Is `core-ops` installed? (Y/N — drives `pipeline-analyst` delegation)
- Is `daily-brief` installed? (Y/N — pairs with `/brief` morning surface)
- Is `writing-style` installed? (Y/N — adaptive voice learning)

### Section 10 — Network expansion sources (Phase 5; collect intent now)

- Conference / event sources: Eventbrite? Luma? Meetup? Manual entry? AI web search?
- LinkedIn cadence: how many posts per week target across each voice? Engagement targets per day (comments)?

(These are forward-looking; the daily brief in Phase 2 will surface them as suggestions even before integrations are wired.)

---

## Step 3 — Write the config

Populate `<config-root>/plugins/relationships.user-context.md`. See `references/user-context.template.md` for the canonical layout.

If the file existed before Step 1 and the user chose "update specific sections," only rewrite the named sections — preserve the rest verbatim.

---

## Step 4 — Confirm and offer next step

Summarize back: tiers + sizes, buckets + emphasis, voices, integrations.

Then offer:
> "Run `/relationships` tomorrow morning (or right now) to see your first daily brief. Run `/network-rebalance` later to bulk-tag your existing cortex person pages with tiers and bucket eligibility (Phase 3 — not yet implemented)."

---

## Behavior rules

- One section at a time.
- Skip sections that don't apply (e.g., no CRM → skip Section 7).
- Idempotent — re-running updates rather than restarts; preserve untouched sections.
- Pre-fill from cortex `identity.md` and `voice.md` whenever they exist.
- Defaults are surfaced and confirmed, not interrogated. Don't make the user answer 60 questions if 50 of them have sensible defaults.
- Respect the autonomy slider (`<config-root>/plugins/cortex.user-context.md` → `autonomy:`). In `auto` mode, accept all defaults and only ask for sections without sensible defaults (ICP, voice descriptors).
