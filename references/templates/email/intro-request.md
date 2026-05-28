---
channel: email
scenario: intro-request
relationship_class: business
tier: strategic | inner
trigger: warm_intro
time_estimate_min: 7
voice_role: primary
needs_variables: [person.first_name, target.full_name, target.company, target.why, user.one_liner]
subject_template: "intro ask — {{target.company}}"
---

{{person.first_name}},

A direct ask: would you be open to introducing me to {{target.full_name}} at {{target.company}}?

The why: {{target.why}}. I'd want a 20-minute conversation, no agenda beyond {{specific_topic}}. {{user.one_liner}}, so I think the value flow would be genuine both ways.

I'd send a forwardable blurb if it's a yes. If it's a no or "not a fit" — totally understood, no need to explain.

{{voice.signoff}}
{{user.first_name}}
