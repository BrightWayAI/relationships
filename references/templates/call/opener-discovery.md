---
channel: call
scenario: opener-discovery
relationship_class: business
tier: new
trigger: any
time_estimate_min: 15
voice_role: primary
needs_variables: [person.first_name, person.company, trigger.context]
---

# Opener — discovery

**Opening (45 sec):**
> "{{person.first_name}}, thanks for the time. To make this useful: I want to spend most of the call understanding {{person.company}}'s current state on {{specific_topic}}, share what I've seen at orgs at your stage, and you tell me whether the math is interesting. Anything you want to put on the agenda first?"

**Discovery (the next 12-15 minutes — listen 80%):**
- Current state: how is {{specific_workflow}} done today? Volume? Cost?
- What's the bottleneck — capacity, quality, time?
- What have they tried? What worked, what didn't?
- Decision-maker: who? Budget owner: who? Timeline pressure: what's driving it?

**Close (3 min):**
- Reflect back the constraints you heard
- Be honest: is this an ICP fit? If not, say so and offer a useful pointer elsewhere.
- If yes: name one concrete next step (teardown, sample, intro to a current client) and a date.
