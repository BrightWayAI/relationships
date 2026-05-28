---
channel: text
scenario: quick-checkin
relationship_class: personal
tier: any
trigger: cadence_overdue
time_estimate_min: 1
voice_role: personal
needs_variables: [person.first_name]
---

Hey {{person.first_name}} — thinking about you. Update me whenever.
