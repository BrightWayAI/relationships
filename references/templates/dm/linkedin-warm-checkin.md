---
channel: dm
scenario: linkedin-warm-checkin
relationship_class: business
tier: inner | strategic
trigger: cadence_overdue
time_estimate_min: 4
voice_role: primary
needs_variables: [person.first_name, shared_context]
---

{{person.first_name}} — overdue for a real catch-up. Last we talked, {{shared_context}}. Where did that land?

No pressure to volley back immediately — also happy with a "still cooking, ping me in a month."

{{voice.signoff}}
