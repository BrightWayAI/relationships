---
channel: call
scenario: opener-warm-checkin
relationship_class: business
tier: inner | strategic
trigger: cadence_overdue
time_estimate_min: 12
voice_role: primary
needs_variables: [person.first_name, shared_context]
---

# Opener — warm catch-up

**Opening (30 sec):**
> "{{person.first_name}}, good to hear your voice. Last we talked it was {{shared_context}} — wanted to give it some space, but I've been thinking about it. Where did it land?"

**Middle (be ready to do most of the listening):**
- What's changed since last time
- What they're sitting with right now (work and otherwise)
- One specific value-give you can offer in real time

**Close (don't force an ask):**
- Confirm next touch-point
- "Anything I can help unstick before we hang up?"
