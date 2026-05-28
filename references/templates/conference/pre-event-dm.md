---
channel: conference
scenario: pre-event-dm
relationship_class: business
tier: any
trigger: any
time_estimate_min: 4
voice_role: primary
needs_variables: [person.first_name, trigger.context]
---

{{person.first_name}} — saw you're going to be at {{trigger.context}}. I'll be there too.

If you're up for a 15-minute coffee or hallway chat at some point, I'd value it — happy to work around your schedule. If you're back-to-back, no worries.

{{voice.signoff}}
