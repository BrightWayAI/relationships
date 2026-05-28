# Changelog

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
