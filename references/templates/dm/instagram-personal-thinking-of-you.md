---
channel: dm
scenario: instagram-personal-thinking-of-you
relationship_class: personal
tier: any
trigger: cadence_overdue
time_estimate_min: 2
voice_role: personal
needs_variables: [person.first_name]
---

{{person.first_name}} — saw {{trigger.context_or_a_thing_that_made_you_think_of_them}} and it made me think of you.

How are you?
