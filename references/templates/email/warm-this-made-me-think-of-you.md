---
channel: email
scenario: warm-this-made-me-think-of-you
relationship_class: business
tier: strategic | inner
trigger: cadence_overdue
time_estimate_min: 5
voice_role: primary
needs_variables: [person.first_name, trigger.specific, shared_context]
subject_template: "this made me think of you"
---

{{person.first_name}},

Came across {{trigger.specific}} and immediately thought of {{shared_context}}.

No ask, no pivot — just sharing because the part about {{specific_relevant_detail}} maps directly onto what you were working through.

{{voice.signoff}}
{{user.first_name}}
