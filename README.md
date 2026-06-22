# AI Voice Ops Router

AI Voice Ops Router is a local n8n portfolio workflow that receives synthetic AI voice call payloads, validates and normalizes the data, classifies call intent and urgency, routes the call to the correct operational queue, prepares a structured call log, and returns a clean JSON response.

This project is built with synthetic data only. It does not use real caller data, production credentials, or a live AI voice platform integration.

## Business Scenario

A business receives calls through an AI voice agent. The voice agent collects caller details, reason for call, urgency, callback preferences, transcript, and summary. The voice platform sends the completed call result to n8n through a webhook.

In this portfolio version, the AI voice platform is simulated with local synthetic POST requests.

## Voice Platform Readiness

This workflow is designed around common AI voice platform webhook patterns: a voice agent completes a call, extracts caller details, transcript, summary, urgency, and intent, then sends a call-result payload to n8n.

The current portfolio version uses synthetic/local payloads for safe testing. It has not yet been connected to live Vapi, Retell, Bland AI, Synthflow, or other external AI voice platforms.

Live external testing is planned as a future integration step, not a completed feature. Because n8n is locally hosted, external platforms cannot call the local test webhook directly without either a secure temporary tunnel or a public n8n deployment.

Readiness documentation:

- `docs/voice-agent-prompt.md`
- `docs/voice-webhook-contract.md`
- `docs/voice-debugging-guide.md`
- `docs/live-integration-plan.md`

## Retell AI Integration Plan

Retell AI is the planned first live voice-platform test target because its webhook and post-call analysis concepts map well to this project's call-result routing workflow.

The project is not yet connected to Retell AI. Current Retell work is familiarization and payload-readiness only. No live Retell credentials, webhook URL, tunnel URL, production deployment, real phone number, or real caller data is included.

Future live testing will require either a secure temporary tunnel or a public n8n deployment, plus webhook authentication and strict redaction. Only synthetic call data should be used unless explicitly approved otherwise.

Retell planning documentation:

- `docs/retell-familiarization-notes.md`
- `docs/retell-integration-readiness-checklist.md`
- `docs/retell-style-payload.md`
- `docs/retell-payload-adapter-map.md`
- `docs/retell-adapter-test-notes.md`
- `docs/phase-2l-live-test-plan.md`

## Retell-Style Adapter Support

The workflow can now process synthetic Retell-style wrapped payloads as well as the original native synthetic payload format. The adapter logic lives in the existing `Normalize Call Payload` node and maps Retell-style event data into the same internal schema used by the rest of the workflow.

This is not a live Retell integration. No real Retell webhook has been received, no Retell credentials are included, and no tunnel or public n8n endpoint has been created. Live Retell testing remains a future Phase 2L-2 step.

## Architecture

```text
Webhook
  -> Normalize Call Payload
  -> Validate Call Payload
  -> Classify Intent and Urgency
  -> Route Call
      -> Urgent Human Review Handler
      -> Sales Follow-Up Handler
      -> Support Queue Handler
      -> Billing Queue Handler
      -> Manual Review Handler
      -> General Queue Handler
  -> Prepare Call Log
  -> Respond to Webhook
```

## Routes

| Route key | Handler | Purpose |
| --- | --- | --- |
| `urgent_human_review` | Urgent Human Review Handler | Escalates emergency or high-urgency calls for immediate human review. |
| `sales_follow_up` | Sales Follow-Up Handler | Routes pricing, demo, quote, and consultation requests to sales. |
| `support_queue` | Support Queue Handler | Routes technical or account support requests to support. |
| `billing_queue` | Billing Queue Handler | Routes invoice, charge, payment, refund, and subscription issues to billing. |
| `manual_review` | Manual Review Handler | Routes incomplete or unclear payloads to operations for review. |
| `general_queue` | General Queue Handler | Routes low-priority general inquiries to a general follow-up queue. |

## Workflow Behavior

The workflow performs these steps:

1. Accepts a POST payload through an n8n test webhook.
2. Normalizes strings, booleans, email casing, timestamps, and numeric duration values.
3. Validates required fields and records validation errors or warnings.
4. Classifies call intent using keyword-based logic.
5. Applies urgency override rules so emergency and high-urgency calls take priority.
6. Routes the call through an n8n Switch node.
7. Adds a placeholder route handler object for the selected branch.
8. Prepares a structured call log object for future storage.
9. Returns a compact JSON response to the caller/test client.

## Local Testing

This workflow was tested locally with n8n using synthetic payloads against the n8n test webhook.

External AI voice platforms cannot call `localhost` directly. Future external testing would require one of these options:

- synthetic payloads only
- a secure temporary tunnel such as ngrok or Cloudflare Tunnel
- a public n8n deployment with authentication and proper secrets management

The workflow should remain inactive during local test-webhook development unless intentionally activated later.

## Test Payloads

The `test-payloads` folder contains native synthetic payloads and Retell-style synthetic payloads.

Native synthetic payloads:

- `sales.json`
- `urgent.json`
- `support.json`
- `billing.json`
- `manual-review.json`
- `general.json`

Retell-style synthetic payloads:

- `retell-style-billing-event.json`
- `retell-style-urgent-support-event.json`
- `retell-style-invalid-event.json`

Example local test command:

```powershell
curl.exe -X POST "<N8N_TEST_WEBHOOK_URL>" `
  -H "Content-Type: application/json" `
  --data-binary "@test-payloads/sales.json"
```

Replace `<N8N_TEST_WEBHOOK_URL>` locally. Do not commit real webhook URLs.

## Test Results

Native and Retell-style synthetic route outcomes were tested successfully:

| Payload | Format | Expected route |
| --- | --- | --- |
| `sales.json` | Native synthetic | `sales_follow_up` |
| `urgent.json` | Native synthetic | `urgent_human_review` |
| `support.json` | Native synthetic | `support_queue` |
| `billing.json` | Native synthetic | `billing_queue` |
| `manual-review.json` | Native synthetic | `manual_review` |
| `general.json` | Native synthetic | `general_queue` |
| `retell-style-billing-event.json` | Retell-style synthetic | `billing_queue` |
| `retell-style-urgent-support-event.json` | Retell-style synthetic | `urgent_human_review` |
| `retell-style-invalid-event.json` | Retell-style synthetic | `manual_review` |

## Response Shape

Successful route response example:

```json
{
  "success": true,
  "call_id": "test_sales_001",
  "route": "sales_follow_up",
  "route_name": "Sales Follow-Up",
  "classification": {
    "intent": "sales",
    "urgency": "medium"
  },
  "validation_status": "valid",
  "errors": [],
  "warnings": [],
  "message": "Call received and routed."
}
```

Invalid or incomplete payloads route to manual review:

```json
{
  "success": false,
  "call_id": "test_incomplete_001",
  "route": "manual_review",
  "route_name": "Manual Review",
  "classification": {
    "intent": "general",
    "urgency": "low"
  },
  "validation_status": "invalid",
  "errors": [
    "Missing caller identity",
    "Missing call intent, summary, or transcript"
  ],
  "warnings": [],
  "message": "Call payload requires manual review."
}
```

## Repository Contents

```text
AIVoiceOpsRouter/
  README.md
  SyntheticData.txt
  workflow/
    AI Voice Ops Router.sanitized.json
    AI Voice Ops Router.phase-2b.sanitized.json
  test-payloads/
    sales.json
    urgent.json
    support.json
    billing.json
    manual-review.json
    general.json
    retell-style-billing-event.json
    retell-style-urgent-support-event.json
    retell-style-invalid-event.json
  screenshots/
    README.md
  docs/
    MISSION_CONTROL_REPORT.md
    PHASE_2K_DECISION_RECORD.md
    PHASE_2K_LITE_REPORT.md
    SECURITY_AND_PUBLISHING_NOTES.md
    live-integration-plan.md
    phase-2l-live-test-plan.md
    retell-adapter-test-notes.md
    retell-familiarization-notes.md
    retell-integration-readiness-checklist.md
    retell-payload-adapter-map.md
    retell-style-payload.md
    voice-agent-prompt.md
    voice-debugging-guide.md
    voice-webhook-contract.md
```

## Additional Documentation

- `docs/MISSION_CONTROL_REPORT.md` summarizes the completed build for review.
- `docs/PHASE_2K_DECISION_RECORD.md` documents the skipped external voice-platform test and the pending Mission Control decision.
- `docs/PHASE_2K_LITE_REPORT.md` summarizes the voice platform readiness upgrade.
- `docs/SECURITY_AND_PUBLISHING_NOTES.md` records the privacy and publication safeguards used for the repo.
- `docs/voice-agent-prompt.md` defines the provider-neutral voice agent behavior.
- `docs/voice-webhook-contract.md` defines the expected call-result webhook contract.
- `docs/voice-debugging-guide.md` documents common payload and routing troubleshooting steps.
- `docs/live-integration-plan.md` explains future tunnel or public deployment options.
- `docs/retell-familiarization-notes.md` documents Retell concepts to learn before live testing.
- `docs/retell-integration-readiness-checklist.md` provides pre-live Retell setup and evidence checklists.
- `docs/retell-style-payload.md` defines a synthetic Retell-inspired payload shape.
- `docs/retell-payload-adapter-map.md` documents the implemented synthetic Retell-style adapter mapping.
- `docs/retell-adapter-test-notes.md` records expected normalization, validation, and route outcomes.
- `docs/phase-2l-live-test-plan.md` lays out the staged Retell live-test path.

## Security And Privacy

This repository intentionally excludes:

- real webhook URLs
- tunnel URLs
- access tokens
- production credentials
- real caller names
- real phone numbers
- real email addresses
- real transcripts
- private n8n instance details

Screenshots should be redacted before publishing if they show webhook paths, tunnel URLs, account details, or execution data that should not be public.

## Future Improvements

- Add webhook authentication before any public or tunnel-based testing.
- Store `call_log` records in a database, spreadsheet, or ticketing system.
- Replace placeholder route handlers with real CRM, helpdesk, or notification actions.
- Add provider-specific payload adapters for platforms such as Vapi, Retell, Bland AI, or Synthflow.
- Deploy n8n publicly only with HTTPS, authentication, backups, and secrets management.
