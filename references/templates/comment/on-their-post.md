---
channel: comment
scenario: on-their-post
relationship_class: any
tier: any
trigger: their_post
time_estimate_min: 3
voice_role: primary
needs_variables: [person.first_name, trigger.specific]
---

{{person.first_name}} — the part about {{trigger.specific}} is the part most people skip. We saw the same pattern when {{shared_context_or_short_relevant_observation}}. Curious whether you're seeing it hold in the larger orgs you work with.
