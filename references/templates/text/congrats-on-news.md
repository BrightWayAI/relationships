---
channel: text
scenario: congrats-on-news
relationship_class: any
tier: any
trigger: news | job_change
time_estimate_min: 2
voice_role: primary
needs_variables: [person.first_name, trigger.context]
---

{{person.first_name}} — saw {{trigger.context}}. Huge. Genuinely happy for you.
