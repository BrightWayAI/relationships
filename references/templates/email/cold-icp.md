---
channel: email
scenario: cold-icp
relationship_class: business
tier: new
trigger: cold_icp
time_estimate_min: 8
voice_role: primary
needs_variables: [person.first_name, person.company, trigger.context, user.one_liner, user.first_name]
subject_template: "{{person.company}} + {{user.company}} — quick math"
---

Hi {{person.first_name}},

{{trigger.context}} — caught my attention because {{user.one_liner}}, and orgs at {{person.company}}'s stage usually face exactly the bottleneck we solve.

The short version of the math: a similar engagement cut the same workflow from {{old_state_short}} to {{new_state_short}}.

If it's worth a 10-minute look, I'd send a one-page teardown of a comparable engagement and let you decide whether the math works for you. If not, no worries.

{{voice.signoff}}
{{user.first_name}}
