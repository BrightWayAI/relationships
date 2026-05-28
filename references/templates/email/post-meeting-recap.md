---
channel: email
scenario: post-meeting-recap
relationship_class: business
tier: any
trigger: any
time_estimate_min: 6
voice_role: primary
needs_variables: [person.first_name, shared_context]
subject_template: "follow-ups from today"
---

{{person.first_name}},

Quick recap and the things I owe you:

**What we agreed:**
- {{decision_1}}
- {{decision_2}}

**On my side:**
- {{commitment_1}} — by {{date_1}}
- {{commitment_2}} — by {{date_2}}

**On your side (just so we're aligned):**
- {{their_commitment}} — by {{their_date}}

If I missed or misremembered anything, let me know.

{{voice.signoff}}
{{user.first_name}}
