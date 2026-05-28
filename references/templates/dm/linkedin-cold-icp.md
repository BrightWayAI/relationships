---
channel: dm
scenario: linkedin-cold-icp
relationship_class: business
tier: new
trigger: cold_icp
time_estimate_min: 6
voice_role: primary
needs_variables: [person.first_name, person.company, trigger.context, user.one_liner]
---

{{person.first_name}} — saw {{trigger.context}} at {{person.company}}. Reaching out because {{user.one_liner}} — and the orgs that get the most out of what we do tend to be exactly where {{person.company}} sits right now.

Not pitching. If it's interesting, happy to share a 10-minute teardown of a similar engagement so you can see whether the math works for you. If not, no worries.

{{voice.signoff}}
