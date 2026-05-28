---
channel: email
scenario: followup-no-response
relationship_class: business
tier: any
trigger: cadence_overdue
time_estimate_min: 4
voice_role: primary
needs_variables: [person.first_name, shared_context]
subject_template: "re: {{previous_subject}}"
---

{{person.first_name}} —

Bumping this in case it slipped. Genuinely fine if the answer is "not a priority right now" — would rather know than wonder.

{{shared_context_one_sentence_reminder_of_the_ask}}

{{voice.signoff}}
{{user.first_name}}
