---
channel: conference
scenario: post-event-followup
relationship_class: business
tier: any
trigger: any
time_estimate_min: 5
voice_role: primary
needs_variables: [person.first_name, shared_context, trigger.context]
---

{{person.first_name}} —

Good to actually shake your hand at {{trigger.context}}. The {{specific_part_of_the_conversation}} stuck with me — particularly {{specific_detail}}.

Following up because I owe you {{specific_thing_you_promised}}. {{deliverable_or_pointer}}.

{{voice.signoff}}
