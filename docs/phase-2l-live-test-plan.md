# Phase 2L Live Test Plan

This plan defines the path from Retell familiarization to a controlled future live Retell webhook test.

The current repository is not connected to Retell AI.

## Phase 2L-0: Retell Familiarization

Goal:

Prepare the repository to understand Retell concepts without connecting to Retell.

Status:

```text
In progress / documentation-only.
```

Entry criteria:

- AI Voice Ops Router synthetic local workflow completed.
- Phase 2K Lite voice platform readiness docs completed.
- No live voice platform connection exists.

Work included:

- Retell familiarization notes.
- Retell integration readiness checklist.
- Retell-style synthetic payload document.
- Live test plan.
- README Retell AI Integration Plan section.

Exit criteria:

- Documentation clearly states Retell is planned, not connected.
- No secrets, webhook URLs, tunnel URLs, or real caller data are included.
- Retell concepts are mapped to current n8n workflow needs.
- Mission Control can decide whether to proceed to Phase 2L-1.

## Phase 2L-1: Retell-Style Payload Adapter

Goal:

Design and optionally build a safe adapter layer that maps a Retell-style webhook wrapper into the existing AI Voice Ops Router payload schema.

Entry criteria:

- Phase 2L-0 documentation reviewed.
- Mission Control approves adapter planning.
- Existing workflow behavior remains stable.
- Adapter uses synthetic payloads only.

Potential work:

- Create a `retell-style` synthetic test payload.
- Document mapping from `event.call.*` and `event.call.analysis.*` into the existing flat schema.
- Optionally add a separate adapter workflow or Code node only after approval.
- Test adapter with synthetic payloads.

Exit criteria:

- Adapter mapping documented.
- Synthetic Retell-style payload maps to existing workflow fields.
- Billing, support, sales, urgent, manual review, and general routing still pass.
- No claim of live Retell integration.

## Phase 2L-2: Controlled Live Retell Webhook Test

Goal:

Prove a live Retell synthetic test call can trigger n8n and route through AI Voice Ops Router.

Entry criteria:

- Mission Control explicitly approves live testing.
- Retell account is available.
- Synthetic Retell agent is configured.
- Post-call analysis fields are configured.
- Secure tunnel or public n8n endpoint is approved.
- Webhook authentication plan is approved.
- Screenshot redaction plan is approved.

Potential work:

- Configure Retell webhook for the selected event, likely `call_analyzed`.
- Expose n8n through approved temporary tunnel or public deployment.
- Trigger one synthetic web-call or test call.
- Inspect actual Retell webhook payload.
- Adjust adapter mapping if needed.
- Confirm route and response behavior.

Exit criteria:

- Retell sends webhook to approved n8n endpoint.
- n8n receives the payload.
- webhook authenticity is verified or authentication decision is documented.
- payload maps into the expected schema.
- workflow validates, classifies, routes, logs, and responds.
- evidence is captured with endpoint and account details redacted.

## Security Rules

Always:

- use synthetic caller data only
- redact webhook URLs
- redact tunnel URLs
- redact Retell account details
- redact phone numbers
- keep API keys out of n8n exports
- keep webhook signing secrets out of GitHub
- sanitize workflow exports before publishing
- keep raw Retell payloads private until reviewed

Never:

- publish a tunnel URL
- publish a webhook URL
- commit Retell credentials
- claim production deployment
- use real client data
- use real caller transcripts without explicit approval

## Successful Proof Criteria

For Phase 2L-2, successful proof requires:

- synthetic Retell test call completed
- expected Retell event delivered
- n8n received the event
- payload was mapped correctly
- route matched expected scenario
- `Prepare Call Log` produced structured output
- response or acknowledgment was returned within provider expectations
- no secrets or private data appeared in screenshots or commits

## Claims Safe Only After Live Testing Succeeds

Do not use these claims before Phase 2L-2 passes:

- "Integrated Retell AI with n8n."
- "Connected Retell webhook to AI Voice Ops Router."
- "Processed a live Retell test call."
- "Mapped real Retell webhook payloads into n8n."

Claims safe before live testing:

- "Prepared Retell AI integration plan."
- "Designed a Retell-style payload contract."
- "Built synthetic n8n routing workflow for AI voice call results."
- "Documented future Retell webhook testing path."

## Current Recommendation

Proceed next to Phase 2L-1 only after Mission Control approves adapter planning.

Phase 2L-1 should remain synthetic until real Retell webhook payloads are inspected under an approved live test.
