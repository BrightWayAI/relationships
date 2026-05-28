# Templates library

Bundled defaults for the channels the `/relationships` brief recommends. Each file is a markdown template with a frontmatter block describing when to use it and a body containing the message text with variables.

## Variables

All templates may reference:

| Variable | Meaning |
|---|---|
| `{{person.first_name}}` | The recipient's first name |
| `{{person.full_name}}` | Full name |
| `{{person.title}}` | Job title |
| `{{person.company}}` | Company name |
| `{{trigger.context}}` | Short string describing "why now" (e.g., "$12M Series B last week") |
| `{{trigger.specific}}` | A more specific reference — a post topic, a piece of news, an event |
| `{{user.first_name}}` | The sender's first name |
| `{{user.company}}` | Sender's company |
| `{{user.one_liner}}` | One-sentence description of what the sender does |
| `{{shared_context}}` | Recall string from the cortex person page describing the last meaningful interaction |
| `{{voice.signoff}}` | Sign-off pulled from the voice block being used |

If a template references a variable that can't be filled, `/relationships` either picks a variant that doesn't need it OR surfaces the card as "draft incomplete — needs your touch."

## Frontmatter schema

```yaml
---
channel: comment | dm | email | text | call | conference
scenario: short-kebab-case slug describing when to use
relationship_class: business | personal | any
tier: inner | strategic | operational | new | any
trigger: cadence_overdue | their_post | job_change | news | warm_intro | cold_icp | any
time_estimate_min: 2-15
voice_role: primary | personal | business | any
needs_variables: [list of required variables — card surfaces "incomplete" if any are unfilled]
---
```

## User overrides

Users can override any bundled template by placing a file with the same path at `<config-root>/relationships/templates/<channel>/<scenario>.md`. The override is loaded preferentially.

Users can also add new scenarios by dropping new files in the same location — `/relationships` picks them up automatically based on frontmatter.

## What's bundled

| Channel | Scenarios |
|---|---|
| comment | on-their-post, on-mutual-connection-post |
| dm | linkedin-warm-checkin, linkedin-cold-icp, linkedin-job-change-congrats, linkedin-post-followup, instagram-personal-thinking-of-you |
| email | cold-icp, warm-this-made-me-think-of-you, followup-no-response, intro-request, post-meeting-recap |
| text | quick-checkin, congrats-on-news |
| call | opener-warm-checkin, opener-discovery |
| conference | pre-event-dm, post-event-followup |

17 starters. Add more as patterns emerge.
