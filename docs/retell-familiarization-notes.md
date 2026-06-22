# Retell Familiarization Notes

These notes prepare AI Voice Ops Router for a future Retell AI live test.

This repository is not connected to Retell AI yet. No Retell credentials, phone numbers, webhook URLs, tunnel URLs, account screenshots, or live caller data are included.

## What Retell AI Is Used For In This Project

Retell AI is the planned first live voice-platform test target for AI Voice Ops Router.

In the future, Retell would be used to:

- configure a synthetic voice agent
- run a controlled test call
- generate a call transcript and call summary
- produce post-call analysis fields
- send a webhook event to a public n8n endpoint
- prove that AI Voice Ops Router can receive and route an actual voice-platform call result

For now, Retell is only used as a research and planning target.

## Concepts To Understand Before Live Testing

### Agents

Retell agents are voice agents that handle phone or web-call conversations. For this project, the future agent should behave like an intake assistant that collects caller identity, reason for call, urgency, callback preference, transcript, and summary.

Manual verification needed later:

- whether to use a single-prompt agent or conversation-flow agent
- where to place the intake prompt
- how to configure voice, language, interruption behavior, and end-call behavior
- how to keep the test synthetic and portfolio-safe

### Test Calls

Retell supports testing agents before production use. Web-call testing can be done from the dashboard without buying a phone number, while phone-call testing uses a purchased or configured phone number.

For this portfolio project, the preferred first Retell proof is a web-call or dashboard-based test using synthetic caller details.

Manual verification needed later:

- whether the available Retell account supports web-call testing
- whether test calls trigger the same webhook events needed by n8n
- whether any paid phone number setup is required

### Webhooks

Retell webhooks can notify an external endpoint about call events. The relevant future event for this project is likely `call_analyzed`, because it can include completed call details and analysis output after the call is processed.

Important event concepts to verify:

- `call_started`
- `call_ended`
- `call_analyzed`
- `transcript_updated`
- transfer events if human handoff is tested later

The current n8n workflow is built around a final call-result payload, so the first live test should focus on a post-call event rather than streaming transcript updates.

### Post-Call Analysis

Retell post-call analysis can structure completed conversations into fields. This maps well to the current workflow because AI Voice Ops Router already expects call intent, urgency, summary, transcript, and callback-related fields.

Fields to consider later:

- `intent`
- `urgency`
- `callback_requested`
- `preferred_callback_time`
- `caller_name`
- `company`
- `call_summary`
- `human_review_required`

Manual verification needed later:

- whether custom fields appear inside `call_analysis`
- whether field names can be controlled exactly
- whether analysis is available on the webhook event selected for testing

### Call Transcript

The transcript is the key evidence that the agent captured the caller's words. The current n8n payload contract expects a plain `transcript` string, but Retell may provide transcript data in multiple formats.

Manual verification needed later:

- where the plain transcript appears in the webhook payload
- whether transcript objects include speaker turns
- whether tool calls appear in a separate transcript structure
- whether transcript content should be reduced before logging

### Call Summary

The current workflow expects a concise `summary`. Retell may provide summary-like data through post-call analysis, custom analysis fields, or built-in call analysis.

Manual verification needed later:

- where the summary appears
- whether summary is built-in or must be configured as a custom text analysis field
- whether summary quality is good enough for routing and logging

### Custom Analysis Fields

Retell supports analysis field types such as Boolean, text, number, and selector-style categories. These are useful for converting a raw call into routing-ready fields.

Potential custom fields:

| Field | Type | Purpose |
| --- | --- | --- |
| `detected_intent` | selector | sales, support, billing, emergency, general, unclear |
| `detected_urgency` | selector | low, medium, high, emergency |
| `callback_requested` | boolean | whether caller asked for follow-up |
| `preferred_callback_time` | text | caller's preferred callback window |
| `call_summary` | text | concise call summary |
| `human_review_required` | boolean | whether manual review is needed |

### Webhook Events

Events to understand before testing:

| Event | Relevance |
| --- | --- |
| `call_started` | Useful for lifecycle tracking, but too early for routing by transcript/summary. |
| `call_ended` | Useful for completed call metadata and transcript, depending on payload contents. |
| `call_analyzed` | Best candidate for final routing because analysis output should be complete. |
| `transcript_updated` | Useful for streaming use cases, but not needed for the current workflow. |

## What I Do Not Know Yet

Items to learn manually inside Retell:

- exact dashboard setup flow for the current Retell version
- exact agent type best suited for this demo
- exact webhook event payload received from Retell
- whether `call_analyzed` includes all needed fields
- where transcript, summary, custom analysis, and metadata appear
- how Retell signs webhook requests in practice
- whether n8n can verify Retell signatures without a helper service
- whether an adapter node is needed before existing normalization
- how retries appear in n8n executions
- how Retell web-call tests differ from phone-call tests

## What To Verify Manually Inside Retell Later

Before any live test:

- create or access a Retell account
- confirm test mode or low-risk test call path
- create a synthetic intake agent
- configure post-call analysis fields
- inspect available webhook event settings
- confirm whether account-level or agent-level webhook is preferred
- confirm webhook signature behavior
- confirm whether web-call testing can trigger the needed event
- confirm dashboard screens that can be safely redacted
- confirm no real caller data is used

## Screenshots And Proof To Collect Later

Only collect these after Mission Control approves live Retell testing:

- Retell agent configuration screen with private details redacted
- prompt or conversation setup showing synthetic intake behavior
- post-call analysis field configuration with no secrets
- webhook event configuration with endpoint redacted
- n8n execution showing Retell-style payload received, endpoint hidden
- n8n response showing route and classification
- Retell call history entry with caller data redacted
- final README screenshot or architecture diagram showing Retell as planned/in-progress unless live test is completed

Do not publish:

- Retell API keys
- webhook signing keys
- webhook URLs
- tunnel URLs
- real phone numbers
- account IDs
- billing screens
- unredacted caller transcripts

## Official References Used

- Retell introduction: https://docs.retellai.com/general/introduction
- Retell testing overview: https://docs.retellai.com/test/test-overview
- Retell web call testing: https://docs.retellai.com/test/test-web
- Retell webhook overview: https://docs.retellai.com/features/webhook-overview
- Retell webhook setup guide: https://docs.retellai.com/features/register-webhook
- Retell webhook security: https://docs.retellai.com/features/secure-webhook
- Retell post-call analysis overview: https://docs.retellai.com/features/post-call-analysis-overview
- Retell post-call analysis setup: https://docs.retellai.com/features/post-call-analysis-create
- Retell analysis consumption: https://docs.retellai.com/features/post-call-analysis-consumption
