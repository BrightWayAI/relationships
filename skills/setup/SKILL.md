---
name: setup-relationships
description: Natural-language entrypoint for configuring the relationships plugin. Fires when the user says "set up relationships," "configure my network," "let's set up my relationships plugin," "I want to track my relationships," or similar. Confirms, then routes to /setup-relationships.
---

# Skill — relationships setup

This skill activates on phrases like:

- "set up relationships"
- "configure my network"
- "configure my relationships"
- "let's set up the relationships plugin"
- "I want to start tracking my outreach daily"
- "build my daily relationship brief"
- "set up my outreach cockpit"

## Behavior

1. Confirm with the user: "Want me to walk you through setting up the relationships plugin — your ICP, tiers, voices, and integrations? Takes about 5-10 minutes."
2. If yes → run `/setup-relationships`.
3. If they want a preview first → describe what `/setup-relationships` captures and what `/relationships` produces.

Respect the autonomy slider — in `auto` mode, skip the confirmation and just run.
