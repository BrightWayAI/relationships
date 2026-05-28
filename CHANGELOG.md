# Changelog

## 0.2.0 — intent dimension + framework clarifications

Major schema refinement informed by the inaugural `/network-rebalance` walk on 2026-05-28. Six framework gaps surfaced from tagging 12 real person pages; this release addresses all six.

### New schema field: `intent` (required for ranking; default by tier)

The `intent` field encodes the **dynamic of engagement** as a dimension orthogonal to (but constrained by) `tier`. Tier captures cadence + drive level; intent captures the texture of HOW you engage.

**Active-drive intents** (paired with inner / strategic tiers):
- `client_delivery` — active client engagement (Tom, Charity, Kim patterns)
- `drive_active` — actively driving a relationship goal
- `door_opening` — connector role; staying useful + visible
- `reciprocal` — mutual referral; match the energy (Jennifer Ives pattern)
- `advising` — asymmetric help with explicit offer (Sang Lee pattern)
- `content_share` — pure-give cadence; no quid pro quo (Rob Buelow post-Vector pattern)

**Passive intents** (paired with operational / dormant tiers):
- `keep_warm` — quiet maintenance (Lauren Little, Caitlyn, Catherine pattern)
- `passive_visibility` — network-bucket only; engage with content, never direct outreach (Jonathan Harms pattern)
- `awaiting_reply` — transient state for just-sent outreach (Lauren Bernstein post-DM pattern)

The brief validates (tier × intent) compatibility — invalid combinations (e.g., strategic + passive_visibility) surface in `/network-rebalance` for reconciliation.

### Intent-driven card framing (`/relationships` Step 4)

The daily brief now adapts the card's framing based on intent:
- `advising` → "[Person] — monthly touch. What's your topic?" (waits for user input, then drafts)
- `passive_visibility` → "Comment on [their recent post]" (never drafts a direct DM)
- `content_share` → "Pure-give — share [tool/framework/practice]"
- All other intents → standard channel + template + draft flow

### Cadence override field

New optional `cadence_days_override: <integer>` lets a person use a custom cadence regardless of tier default. Use case: "strategic by intuition, quarterly by cadence." Scoring engine reads override if set; falls back to tier default if null.

### Follow-up window for awaiting_reply intent

For people with `intent: awaiting_reply`, the standard decay formula is replaced by a follow-up window:
- 0-7 days: 0.0 (give them space)
- 7-21 days: linear ramp to 0.8 (gentle bump pressure)
- 21+ days: 1.0 (clear signal to bump or shift to keep_warm)

Prevents fresh outreach from sitting under the operational 90-day cooling period.

### Internal-team exclusion documented

`/relationships` Step 2 now explicitly scopes the candidate pool to `<config-root>/memory/person/*.md` ONLY. Never `team/`, `client/`, `bizdev/`, `workstream/`. Internal team members in `team/` are not subjects of relationship maintenance — this is enforced at scoping time, not via special-case logic.

### Notes-field principle documented

`references/person-page-extensions.md` now codifies: the `notes` field is about THE RELATIONSHIP (communication style, outreach intent context, origin, framing rules, personal context). Project status (deliverables, WAITING items, engagement specifics) belongs in `client/`, `bizdev/`, or `workstream/` nodes. Includes test ("would the note still matter without this person?") + examples.

### Network-rebalance updates

`/network-rebalance` Step 2 now proposes:
- `intent` based on page context (active client → client_delivery, etc.)
- `cadence_days_override` only when user explicitly indicates non-default cadence
- Validates (tier × intent) combinations and surfaces invalid pairs to the user

### today.json schema additions

`option.person.intent` and `option.person.cadence_days_override` now included in the structured JSON output. Schema version stays at `0.1.0` (additive fields only; no breaking changes for consumers).

### Backward compatibility

- Existing pages without `intent` default by tier: inner/strategic → `drive_active`; operational → `keep_warm`; dormant → `passive_visibility`. Run `/network-rebalance` to set explicit values.
- Existing pages without `cadence_days_override` use tier default (current behavior).

### Not included in v0.2.0 (deferred)

- `team/` node prefix as a formalized cortex v4.12 node type — needs separate cortex PR.
- Generosity-ledger auto-population — not in scope.
- Multi-intent per person (array form) — single intent for v0.2.0; consider array form in v0.3 if patterns warrant.

---

## 0.1.2 — preferred_channels as array

Schema refinement during the first real-world network-rebalance walk. Real people have multi-channel preferences (text + phone for inner-core, email + LinkedIn DM for warm BD); a single `preferred_channel` field forced a false choice.

### Changes

- **`preferred_channel` (single) → `preferred_channels` (array)** on cortex person-page frontmatter under the `relationships:` namespace. Ordered by preference; first entry is the default.
- **Channel-selection logic** (in `/relationships` Step 4 and `references/scoring.md`) updated:
  1. Compute rule-table channel for the trigger.
  2. If rule-table channel ∈ `preferred_channels` → use that match.
  3. Else → use `preferred_channels[0]` (primary preference).
  4. Exception: triggers `cold_icp` and `warm_intro` override preferences (they require specific formats).
  5. Absent/empty → rule-table default.
- **`/network-rebalance`** updated to propose arrays based on observed patterns ("All emails → `[email]`; mixed with notes → `[text, call]`; sparse → omit").
- **JSON schema** (`today-json-schema.md`): option's `person.preferred_channels` is now an array.

### Backward compatibility

v0.1.2 readers accept either shape — a singular `preferred_channel` value is silently coerced to a single-item array. No migration required for existing pages (only just-introduced in v0.1.1; no person pages have it yet outside this session's rebalance).

### Not breaking

Schema version stays at `0.1.0` (no UI consumers yet to break).

---

## 0.1.1 — Hybrid setup + UI-ready structure

Architectural refinement based on review feedback (three independent perspectives — architecture, shareability, operator UX — all converged on the same hybrid). Two themes: stop duplicating peer-plugin data, and make the plugin's outputs structurally UI-ready before any UI exists.

### Hybrid setup — `/setup-relationships` shrinks for Nucleus-stack users

- **New Step 0.5 — peer-plugin import.** Detects and pre-fills from `identity.md`, `voice.md`, `lead-engine.user-context.md`, `core-ops.user-context.md`, `referral-engine.user-context.md`, `bizdev-outreach.user-context.md` (legacy), `weekly-outreach.user-context.md` (legacy), `writing-style.user-context.md`, and cortex workstream nodes. One-time import at setup; not live runtime reads.
- **Step 2 collapses to confirmation + 5 unique questions.** For full-stack users: ~60 seconds. For standalone installs (no peers): full interview with fallback fields written into the user-context.
- **Provenance.** Each imported field tagged with source path + import date in the written user-context.

### user-context.template — slimmed

- **Dropped:** identity, voice descriptors, full CRM properties, full Apollo config, full referral cooling — all live at their canonical peer files at runtime.
- **Kept:** minimal identity for template-variable filling, tier configurations, bucket weights, close-personal track toggle, time budget, network-expansion voice routing, scoring overrides, companion-plugin detection results, standalone-install fallback sections.
- Net file size cut by ~60%; signal-to-noise improved substantially.

### UI-ready structure (for the future web app / Operator desktop / mobile PWA)

- **Stable option IDs.** Format `<bucket>_<person-slug>_<YYYY-MM-DD>` instead of `<bucket>_<date>_<rank>`. Same person on the same day → same ID across re-runs. UIs can correlate "did I act on this?" reliably.
- **`brief_id`.** UUID v4 generated at the start of every `/relationships` run. Included in `today.json` and in every event. Lets a UI correlate events to specific brief generations.
- **`events.jsonl`** — append-only event log at `<config-root>/relationships/events.jsonl`. Written when the user marks a card copied/sent/skipped/snoozed. Canonical action stream for analytics.
- **`snoozes.json`** — persistent snooze state at `<config-root>/relationships/snoozes.json`. Read by `/relationships` Step 3 (filter pool); written by `/relationships-action`.
- **New command `/relationships-action`** — decoupled action endpoint. Takes a JSON event payload (inline or file-based from a UI inbox). Appends to events log, updates cortex person page, updates snoozes. UIs invoke this once per tap.
- **`inbox/` pattern.** UI writes JSON events to `<config-root>/relationships/inbox/<uuid>.json`; sync daemon invokes `/relationships-action --file=<path>`; processed files move to `inbox/processed/`. No HTTP, no IPC — just files on a shared volume.

### New references

- **`references/ui-integration.md`** — full contract for UI builders: how to consume `today.json`, post events to `/relationships-action`, watch `snoozes.json`, handle schema versioning, the sync-daemon pattern, what NOT to do.

### Updated references

- **`references/today-json-schema.md`** — adds `brief_id` field + new stable option-ID format + related-files section pointing at events.jsonl and snoozes.json.

### Updated contracts (in `nucleus/docs/contracts.md`)

- Added rows for `events.jsonl`, `snoozes.json`, `inbox/`.
- Documented peer-import behavior (one-time at setup, not runtime).

### Skills

- Added `relationships-action` natural-language entrypoint (e.g., "I sent that to Sarah" → resolves option → invokes `/relationships-action`).

### What this is NOT

- Not a UI. v0.1.1 is structure for a future UI. The web app / Operator desktop is Phase 6.
- Not a re-architecture. Existing `/relationships` Step 0 → Step 8 flow is intact; this adds writes (events, snoozes) and reads (snoozes at Step 3).
- Not breaking. Schema version stays at `0.1.0` (option ID format change is a UI-internal concern; the contract was added in this release so no consumers exist yet to break).

---

## 0.1.0 — Initial release

First release of the `relationships` plugin — the daily orchestrator for new business development, relationship building, and network expansion. Three-bucket brief, configurable tiers, templates library, channel-aware draft surface, on-demand drafting, and quarterly migration tooling.

### Commands

- **`/setup-relationships`** — 10-section interview that writes `<config-root>/plugins/relationships.user-context.md` (identity, ICP, current quarter focus, tiers + sizes, buckets, voices, time-budget, CRM, Apollo, companion plugins, network expansion sources). Idempotent.
- **`/relationships`** — daily brief command. 8-step orchestrator: preflight → time-budget gate → score-and-rank (per bucket, via `relationship-ranker` subagent) → filter → pick channel + template → draft → present → write artifacts (`today.md` + `today.json`). Optional person-page write-back on user "mark sent."
- **`/network-rebalance`** — Phase 3 critical-path migration command. Walks cortex person pages, proposes additive `relationships:` frontmatter (tier + buckets + relationship_class + icp_fit + preferred_channel), batch-interactive (10-15 per batch). Optional HubSpot back-sync. Idempotent; runnable quarterly.
- **`/draft-touchpoint`** — per-contact on-demand drafting. Resolve contact → gather context → determine intent → pick channel + template → fill variables → "should we even send" filter → present. Drafts only.

### Subagents

- **`relationship-ranker`** — scoring and ranking subagent invoked once per bucket by `/relationships`. Reads cortex + CRM + Gmail + hot cache, computes weighted score per `references/scoring.md`, returns ranked candidates with score breakdowns and concrete "why now" reasons. Web-search capped at 3 per invocation. Read-only.

### Skills (natural-language entrypoints)

- `relationships` — fires on "who should I reach out to today" / "run my daily relationships."
- `setup` — fires on "set up relationships" / "configure my network."
- `network-rebalance` — fires on "rebalance my network" / "audit my tiers."
- `draft-touchpoint` — fires on "draft a message to [name]" / "help me follow up with [name]."

### Templates library

- **17 bundled defaults** across 6 channels (comment / dm / email / text / call / conference). Variable-driven: `{{person.first_name}}`, `{{trigger.context}}`, `{{user.one_liner}}`, etc.
- User overrides supported at `<config-root>/relationships/templates/<channel>/<scenario>.md` (preferred over bundled by filename).
- Frontmatter schema documented in `references/templates/README.md`.

### Reference docs

- **`framework.md`** — Ibarra tier spine + Ferrazzi qualitative layer + reality-layer adjustments for the time-constrained operator.
- **`scoring.md`** — weighted-score math, default weights, factor definitions, channel-selection table.
- **`person-page-extensions.md`** — the `relationships:` YAML frontmatter namespace (locked: frontmatter, not a section). Additive, non-breaking; treated as candidate cortex v4.12.0 schema bump. Other plugins writing person-page state should use their own frontmatter namespace.
- **`today-json-schema.md`** — formal contract for the structured daily-brief output. Schema version `0.1.0`. Used by `today.json` + downstream surfaces (future web app, Operator desktop).
- **`user-context.template.md`** — full schema for the per-user config file.

### Cross-plugin contracts (added to `nucleus/docs/contracts.md`)

- Writes: `<config-root>/plugins/relationships.user-context.md`, `<config-root>/relationships/today.md`, `<config-root>/relationships/today.json`, `<config-root>/relationships/<date>.md`, optional user overrides under `<config-root>/relationships/templates/`.
- Reads: cortex `identity.md`, `voice.md`, `memory/person/*.md`, `memory/hot.md`, `memory/DASHBOARD.md`, `memory/workstream/*.md` (additive read).
- Additively writes: cortex `memory/person/<slug>.md` `## Recent interactions` log (on user "mark sent" or via `/network-rebalance` frontmatter writes).

### Delegates to (when installed)

- **`contact-researcher`** (lead-engine) — per-contact deep dives.
- **`pipeline-analyst`** (core-ops) — pipeline ranking for the new-business bucket.
- **`memory-librarian`** (cortex) — cross-cortex queries.
- **`referral-engine`** — warm-network ask drafting + cooling-period rules.

### Supersedes / replaces (planned, separate Phase 4 PR)

- **`weekly-outreach`** — its weekly-prep role folds into `/relationships`'s daily flow plus a planned `--week` mode. Step 9 person-page side-effect ported.
- **`bizdev-outreach`** — three-phase research-analyze-draft methodology becomes the contract for `/draft-touchpoint` + the templates library. The standalone plugin is deprecated.

### Out of scope for v0.1

- Web app / mobile UI — Phase 6 (separate proposal).
- Conference / event discovery integrations (Eventbrite, Luma, Meetup) — Phase 5.
- LinkedIn posting cadence orchestration — Phase 5.
- LinkedIn-export and iCloud-contacts bulk importers — Phase 5.
- Auto-tiering / auto-generosity-ledger from sent-mail — future.
- Cortex schema bump to v4.12.0 documenting the `relationships:` frontmatter namespace — separate cortex PR.
