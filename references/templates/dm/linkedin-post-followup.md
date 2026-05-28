---
channel: dm
scenario: linkedin-post-followup
relationship_class: business
tier: any
trigger: their_post
time_estimate_min: 4
voice_role: primary
needs_variables: [person.first_name, trigger.specific]
---

{{person.first_name}} — meant to follow up on the comment thread under your {{trigger.specific}} post. The piece that didn't fit in a comment: {{one_specific_genuine_insight}}.

Sharing because it's been useful to me — not pitching. If it's useful too, great.

{{voice.signoff}}
