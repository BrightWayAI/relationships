---
channel: dm
scenario: linkedin-job-change-congrats
relationship_class: business
tier: any
trigger: job_change
time_estimate_min: 3
voice_role: primary
needs_variables: [person.first_name, trigger.context]
---

{{person.first_name}} — congrats on {{trigger.context}}. Big move. Curious what drew you in.

(Genuinely curious — not pivoting to an ask.)

{{voice.signoff}}
