# Mission Control Report: AI Voice Ops Router

## Project Status

Portfolio Project #2, AI Voice Ops Router, has been completed through portfolio packaging and GitHub publishing.

GitHub repository:

https://github.com/Ivorocious/AIVoiceOpsRouter

The workflow remains a local n8n portfolio build using synthetic data only. No real voice platform, production credentials, real caller data, public tunnel, or production webhook was configured.

## Business Scenario

The project models a business that receives calls through an AI voice agent. The AI voice agent collects caller details, call reason, urgency, callback preference, transcript, and call summary. The voice platform would send that completed call result to n8n through a webhook.

For this portfolio version, the voice platform was simulated with synthetic POST payloads sent to a local n8n test webhook.

## Final Workflow

Workflow name:

```text
AI Voice Ops Router
```

Final workflow architecture:

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

The workflow is documented inside n8n with sticky notes describing:

- overall workflow purpose
- local n8n testing limitations
- intake and validation behavior
- classification and routing behavior
- placeholder route handlers
- structured logging and webhook response behavior

## What Was Built

The following n8n workflow capabilities were built manually:

- POST webhook intake
- local test webhook execution
- synthetic payload testing
- payload normalization
- required-field validation
- warning/error collection
- keyword-based intent classification
- urgency classification
- emergency/high-urgency override routing
- Switch-based branch routing
- six placeholder route handlers
- branch convergence into a shared logging node
- structured `call_log` preparation
- clean JSON response through `Respond to Webhook`
- sticky-note workflow documentation

## Validation And Normalization

The normalization step prepares incoming call data by:

- trimming string values
- lowercasing email, intent, urgency, voice platform, and call status fields
- converting callback values to Boolean
- converting call duration to a number
- preserving the raw synthetic payload for build-time inspection

The validation step checks:

- `call_id`
- at least one caller identity field
- at least one call content field
- `voice_platform`
- optional `call_status`
- email format when present
- non-negative call duration
- timestamp parseability when present

Invalid payloads are routed to manual review.

## Classification And Routing

The workflow classifies intent into:

- emergency
- sales
- billing
- support
- general

Routing outcomes:

| Route key | Handler |
| --- | --- |
| `urgent_human_review` | Urgent Human Review Handler |
| `sales_follow_up` | Sales Follow-Up Handler |
| `support_queue` | Support Queue Handler |
| `billing_queue` | Billing Queue Handler |
| `manual_review` | Manual Review Handler |
| `general_queue` | General Queue Handler |

Emergency and high-urgency signals override normal intent routing.

A bug was found during synthetic testing: the billing payload originally routed to support because the phrase `billing issue` matched the support keyword `issue` before billing was checked. The classifier was corrected so billing-specific keywords are evaluated before generic support keywords.

## Synthetic Testing Completed

Six synthetic payloads were created and tested:

- `sales.json` -> `sales_follow_up`
- `urgent.json` -> `urgent_human_review`
- `support.json` -> `support_queue`
- `billing.json` -> `billing_queue`
- `manual-review.json` -> `manual_review`
- `general.json` -> `general_queue`

All six route outcomes passed after correcting the billing/support keyword priority.

## Webhook Response

The workflow returns a compact JSON response containing:

- `success`
- `call_id`
- `route`
- `route_name`
- `classification.intent`
- `classification.urgency`
- `validation_status`
- `errors`
- `warnings`
- `message`

Example:

```json
{
  "success": true,
  "call_id": "test_general_001",
  "route": "general_queue",
  "route_name": "General Queue",
  "classification": {
    "intent": "general",
    "urgency": "low"
  },
  "validation_status": "valid",
  "errors": [],
  "warnings": [],
  "message": "Call received and routed."
}
```

## Structured Logging

The workflow prepares a `call_log` object suitable for future storage in a spreadsheet, database, incident log, CRM, or ticketing system.

The log includes:

- event type
- workflow name
- call ID
- voice platform
- call status
- caller presence metadata
- classification result
- routing decision
- validation result
- callback metadata
- summary
- processed timestamp

The portfolio version does not write this record to an external system.

## Phase 2K: External Voice Platform Testing

Phase 2K was intentionally not executed against a real AI voice platform.

Reason:

The workflow is running on locally hosted n8n. External AI voice platforms cannot call `localhost` directly. Connecting a real voice platform would require either a public tunnel or a public n8n deployment.

What was done instead:

- We documented the limitation.
- We documented future testing options.
- We kept the project synthetic-only for portfolio safety.
- We did not create a public tunnel.
- We did not configure Vapi, Retell, Bland AI, Synthflow, or any other external AI voice platform.
- We did not expose a local webhook, tunnel URL, production webhook, credential, or token.

Phase 2K decision status:

```text
Pending Mission Control final decision.
```

Recommendation:

Keep this project synthetic-only for the portfolio version unless Mission Control specifically wants live external voice-platform testing. If live testing is approved, require webhook authentication, a temporary secure tunnel or public deployment, and strict redaction rules before any screenshots or commits.

See `docs/PHASE_2K_DECISION_RECORD.md` for the full decision record.

## Repository Package

The repository package includes:

- `README.md`
- `SyntheticData.txt`
- `workflow/AI Voice Ops Router.sanitized.json`
- `workflow/AI Voice Ops Router.phase-2b.sanitized.json`
- `test-payloads/*.json`
- redacted screenshots
- documentation notes

Screenshots included:

- `workflow-overview-redacted.png`
- `sample-response-redacted.png`
- `structured-log-redacted.png`

The optional route-test screenshot was not included.

## Security And Privacy Work Completed

Before publishing:

- final workflow JSON was sanitized
- local webhook path was redacted
- webhook ID was redacted
- workflow ID was redacted
- version ID was redacted
- n8n instance ID was redacted
- sample response screenshot was redacted
- no tunnel URL was included
- no real caller data was included
- no production credential was included
- privacy scan found no local webhook URL or private n8n identifiers

## GitHub Publishing

The project was initialized as a standalone git repository and pushed to:

https://github.com/Ivorocious/AIVoiceOpsRouter

Initial publish commit:

```text
184621a Add AI Voice Ops Router portfolio package
```

## Final Assessment

AI Voice Ops Router demonstrates:

- n8n webhook architecture
- local webhook testing
- JavaScript-based normalization and validation
- keyword-based classification
- routing with Switch
- branch convergence
- structured logging preparation
- clean JSON response handling
- synthetic test coverage
- local n8n limitation awareness
- privacy-conscious portfolio packaging

The project is ready for Mission Control review.
