# Changelog

## 0.1.0 — Initial release (unreleased)

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
