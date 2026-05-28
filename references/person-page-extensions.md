# Person-page extensions

The `relationships` plugin extends cortex person pages (`<config-root>/memory/person/<slug>.md`) with additive fields that drive tier-based prioritization and bucket eligibility. **Existing person pages remain valid** — untagged people fall back to sensible defaults.

## The new fields

```yaml
relationships:
  tier: inner | strategic | operational | dormant
  buckets: [new_biz, relationship, network]
  relationship_class: business | personal
  icp_fit: primary | secondary | none
  next_touch_target: YYYY-MM-DD
  preferred_channel: email | linkedin_dm | text | call | none
  generosity_ledger:
    - date: YYYY-MM-DD
      direction: gave | received
      note: "short string"
  notes:
    - "free-form, optional"
```

## Field reference

### `tier` (required for ranking; default `operational`)

Drives cadence. Tier targets are configurable per user in `relationships.user-context.md`. Default targets:

| Tier | Default cadence |
|---|---|
| `inner` | 14 days |
| `strategic` | 30 days |
| `operational` | 90 days |
| `dormant` | trigger-only — never proactively surfaced |

### `buckets` (required for eligibility; default `[relationship]`)

Which daily-brief buckets this person is eligible for. Most people are eligible for one or two; very few belong in all three.

- `new_biz` — appears in the New Business Development bucket. Use for ICP-fit prospects and active-deal contacts.
- `relationship` — appears in the Relationship Building bucket. Use for inner-core, strategic, and operational contacts you maintain.
- `network` — appears in the Network Expansion bucket. Use for people whose content you actively engage with, conference cohort, etc.

### `relationship_class` (default `business`)

Drives channel and voice selection. `personal` people use the personal voice (if defined) and channel defaults skew to text/Instagram DM/phone call. Surfaced in a separate sub-tab if the user enabled the close-personal track in setup.

### `icp_fit` (default `none`)

For new-business weighting. `primary` matches the user-context primary ICP definition; `secondary` matches secondary; `none` is out-of-ICP for this user's current focus. Recompute as ICP evolves.

### `next_touch_target` (auto-computed)

Date the cadence next becomes due. Computed as `last_meaningful_contact + tier_cadence_days`. Used by the scoring engine to compute the decay factor. **Auto-maintained** by `/relationships` and `/network-rebalance`; users rarely set manually.

### `preferred_channel` (optional)

If the user knows the person prefers a specific channel ("Sarah always responds fast on LinkedIn DM; emails go to voicemail"), set it here. The brief's channel-selection rules respect this override.

### `generosity_ledger` (optional)

Log of value exchange. Each entry: `date`, `direction` (gave or received), one-line `note`. Used by the reciprocity scoring factor — if you owe them, it weights them up; if they recently owe you, it weights them down (they should reach out next).

Keep this short — the last 5-10 entries are enough. Don't journal every micro-interaction.

### `notes` (optional)

Free-form additional notes specific to relationships logic. Most context belongs in the cortex page's main Notes section; use this only for relationship-mechanism notes ("don't text after 9pm her time," "always cc her assistant").

---

## Where the fields live — YAML frontmatter

**Decision locked (2026-05-28):** the new fields live in YAML frontmatter at the top of the person-page file, between `---` delimiters.

```markdown
---
relationships:
  tier: strategic
  buckets: [new_biz, relationship]
  relationship_class: business
  icp_fit: primary
  next_touch_target: 2026-06-10
  preferred_channel: linkedin_dm
  generosity_ledger:
    - date: 2026-05-12
      direction: gave
      note: "intro to Sarah at MedBridge"
    - date: 2026-04-28
      direction: gave
      note: "shared their LinkedIn post"
---

# person:sarah-chen
[rest of the page unchanged — narrative sections continue as before]
```

### Why frontmatter

- Machine-readable. The indexer, `memory-librarian`, and a future web-app / Operator desktop can parse the fields without bullet-list ambiguity.
- Additive and non-breaking. Existing pages without frontmatter remain valid; the plugin treats missing frontmatter as `tier: operational`, `relationship_class: business`, `icp_fit: none`, empty buckets default to `[relationship]`.
- Aligns with Obsidian's native frontmatter handling — cortex pages opened in Obsidian show frontmatter fields as properties in the UI.

### Cortex coordination

This is a small cortex schema additive (no breaking change — frontmatter is purely additive on top of existing person pages). Treated as a **minor** cortex version bump (v4.12.0 candidate) that:

1. Documents `relationships:` as a recognized frontmatter namespace under the person-page schema in `cortex/CLAUDE.md`.
2. Updates the cortex `indexer` skill to surface the new fields in `memory/index.md` entries (tier label, ICP-fit chip).
3. Adds a graceful no-op for pages without the frontmatter — same as today's behavior.
4. Does NOT migrate existing pages automatically. Migration is opt-in via `/network-rebalance` (this plugin's Phase 3 command).

If cortex maintainers prefer a separate frontmatter namespace pattern (e.g., one block per plugin) rather than `relationships:` at the root, this plugin will follow that. Until then, `relationships:` is the canonical namespace.

### Future plugins reusing the pattern

Other plugins that need to write structured data to person pages should use their own frontmatter namespace (e.g., `referral_engine:`, `weekly_outreach:`). This avoids collisions and keeps each plugin's writes scoped.

---

## Migration

`/network-rebalance` (Phase 3) walks every person page and proposes `tier`, `buckets`, `relationship_class`, and `icp_fit` based on:

- `temperature` field (Cold/Warm/Active/Dormant from cortex v4.2 person-page schema) → seed for `tier`
- `Linked entities` → projects + workstreams → seed for `buckets`
- Mention count + interaction recency → seed for `tier` adjustment
- User-context ICP definition → match to seed `icp_fit`

User approves in batches (10-15 at a time). Idempotent — re-runnable.

---

## Reads + writes

| Source | Reads which fields | Writes which fields |
|---|---|---|
| `/relationships` (this plugin) | all | `next_touch_target` (auto-maintained), `generosity_ledger` (when user logs giving), `## Recent interactions` append |
| `/network-rebalance` (this plugin, Phase 3) | all | `tier`, `buckets`, `relationship_class`, `icp_fit` (proposed; user-approved) |
| `contact-researcher` (lead-engine) | reads only | does not write these fields |
| `referral-engine` | `tier`, `relationship_class`, `generosity_ledger` | may append to `generosity_ledger` on ask draft |
| cortex `/recall` | all (renders the section) | none |
| cortex `/cleanup` | `tier` (uses for archive triggers) | may demote `tier` to `dormant` on inactivity |

---

## Out of scope for v0.1

- Auto-tiering — for now `tier` is user-set (via `/network-rebalance`). Auto-tiering by mention frequency + interaction recency is a Phase 5+ idea.
- ICP fit auto-detection from CRM custom fields — Phase 3 will read CRM tier/ICP properties and propose, but won't blindly sync.
- Generosity-ledger auto-logging from sent messages — out of scope until we have outbound logging.
