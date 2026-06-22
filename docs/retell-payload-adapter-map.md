# Retell Payload Adapter Map

Phase 2L-1 Full adds synthetic-only Retell-style adapter support to the existing AI Voice Ops Router workflow.

The adapter is implemented inside the `Normalize Call Payload` Code node in `workflow/AI Voice Ops Router.sanitized.json`. The workflow is still not connected to Retell AI, no webhook endpoint is exposed, and no live Retell payload has been received.

## Scope

Included:

- native synthetic payload passthrough support
- Retell-style synthetic wrapper detection
- Retell-style field normalization into the existing flat workflow schema
- adapter metadata fields
- fallback behavior for incomplete wrapped payloads
- synthetic test payloads for billing, urgent support, and invalid input

Not included:

- live Retell integration
- tunnel setup
- public n8n deployment
- Retell credentials
- webhook signature implementation
- production deployment

## Source Payloads

Synthetic Retell-style payloads:

```text
test-payloads/retell-style-billing-event.json
test-payloads/retell-style-urgent-support-event.json
test-payloads/retell-style-invalid-event.json
```

These payloads are provider-neutral and Retell-inspired. They are not copied from a live Retell account.

## Adapter Position

The adapter now lives in the existing normalization step:

```text
Webhook
  -> Normalize Call Payload
       - native synthetic payload passthrough
       - Retell-style synthetic wrapper normalization
  -> Validate Call Payload
  -> Classify Intent and Urgency
  -> Route Call
```

This keeps downstream validation, classification, routing, logging, and response behavior unchanged.

## Wrapper Detection

`Normalize Call Payload` treats a payload as Retell-style when the incoming body has a nested `call` object plus at least one Retell-style signal:

- `event`
- `provider` containing `retell`
- `call.analysis`
- `call.call_analysis`
- `call.caller`

If the payload does not match those signals, the workflow treats it as the original native AI Voice Ops Router schema.

## Adapter Metadata

The normalization step now emits these top-level metadata fields:

| Field | Native synthetic payload | Retell-style synthetic payload |
| --- | --- | --- |
| `source_provider` | `native_synthetic` | `retell_style_synthetic` |
| `adapter_status` | `native_passthrough` | `normalized` or `normalized_with_warnings` |
| `adapter_warnings` | `[]` | Array of adapter fallback warnings |
| `original_event_type` | `""` | Event name such as `call_analyzed` |

These fields are preserved through the workflow because downstream nodes spread the incoming JSON.

## Field Mapping

| Retell-style source | Existing workflow field | Implemented rule |
| --- | --- | --- |
| `call.call_id`, `call.id`, `call_id` | `call_id` | First non-empty value. Missing value creates adapter warning and validation error. |
| `call.caller.name`, `call.caller_name`, `call.analysis.caller_name` | `caller_name` | First non-empty value. Missing value creates adapter warning. |
| `call.caller.phone`, `call.caller_phone` | `caller_phone` | First non-empty value. Missing value becomes empty string and creates adapter warning. |
| `call.caller.email`, `call.caller_email`, `call.analysis.caller_email` | `caller_email` | First non-empty value, lowercased. |
| `call.caller.company`, `call.company`, `call.analysis.company` | `company` | First non-empty value. |
| `call.analysis.detected_intent`, `call.analysis.intent`, `call.call_intent` | `call_intent` | First non-empty value, lowercased. Missing value creates adapter warning; classifier infers from summary/transcript. |
| `call.analysis.detected_urgency`, `call.analysis.urgency`, `call.urgency` | `urgency` | First non-empty value, lowercased. Missing value defaults to `low` and creates adapter warning. |
| `call.analysis.callback_requested`, `call.callback_requested` | `callback_requested` | Converted to Boolean. Missing value defaults to `false` and creates adapter warning. |
| `call.analysis.preferred_callback_time`, `call.preferred_callback_time` | `preferred_callback_time` | First non-empty value. Missing value stays empty. |
| `call.transcript`, `call.transcript_text`, `call.analysis.transcript` | `transcript` | First available transcript string. Missing value creates adapter warning. |
| `call.summary`, `call.analysis.call_summary`, `call.analysis.summary` | `summary` | First available summary. If missing and transcript exists, uses a transcript excerpt and creates adapter warning. |
| `call.call_started_at`, `call.started_at`, `call.start_timestamp` | `call_started_at` | First non-empty timestamp string. |
| `call.call_duration_seconds`, `call.duration_seconds`, `call.duration_ms`, start/end timestamps | `call_duration_seconds` | Uses seconds if present, converts milliseconds if present, otherwise computes from start/end timestamps when safe. |
| adapter constant | `voice_platform` | `synthetic_retell_style` for wrapped synthetic payloads. |
| `call.call_status`, `call.status`, fallback | `call_status` | First non-empty status, lowercased. Defaults to `unknown`. |

## Expected Normalized Outputs

### Billing Event

`test-payloads/retell-style-billing-event.json` should normalize to:

```json
{
  "call_id": "synthetic_retell_billing_001",
  "caller_name": "Synthetic Billing Caller",
  "caller_phone": "<synthetic_phone_placeholder>",
  "caller_email": "billing.caller@example.test",
  "company": "Example Finance Co",
  "call_intent": "billing",
  "urgency": "low",
  "callback_requested": true,
  "preferred_callback_time": "tomorrow",
  "voice_platform": "synthetic_retell_style",
  "call_status": "completed"
}
```

Expected route: `billing_queue`.

### Urgent Support Event

`test-payloads/retell-style-urgent-support-event.json` should normalize to a support call with emergency urgency.

Expected route: `urgent_human_review`.

### Invalid Event

`test-payloads/retell-style-invalid-event.json` intentionally omits required caller and content fields.

Expected route: `manual_review`.

## Fallback Rules

| Missing or unclear value | Implemented adapter behavior |
| --- | --- |
| Missing `call_id` | Empty normalized `call_id`, adapter warning, validation error. |
| Missing caller name | Empty normalized `caller_name`, adapter warning. |
| Missing caller phone | Empty normalized `caller_phone`, adapter warning. |
| Empty transcript | Empty normalized `transcript`, adapter warning. |
| Missing summary with transcript available | Uses transcript excerpt as `summary`, adapter warning. |
| Missing summary with no transcript | Empty normalized `summary`, adapter warning, validation may fail if intent is also missing. |
| Missing intent | Empty normalized `call_intent`, adapter warning, classifier infers from summary/transcript. |
| Missing urgency | Normalized `urgency` defaults to `low`, adapter warning. |
| Missing callback fields | `callback_requested` defaults to `false`, `preferred_callback_time` defaults to empty string, adapter warning. |
| Emergency or high-urgency wording | Existing classifier still overrides normal intent routing with urgent review. |

## Safe Claims After Phase 2L-1 Full

Safe:

- "Implemented synthetic Retell-style payload normalization in n8n."
- "Preserved native synthetic payload support while adding wrapped payload support."
- "Tested synthetic Retell-style billing, urgent support, and invalid payloads through the workflow logic."

Not safe:

- "Integrated Retell AI."
- "Received live Retell webhooks."
- "Connected Retell to n8n."
- "Processed real Retell calls."
- "Deployed a production Retell webhook."

## Phase 2L-2 Gate

Before any live Retell test, Mission Control must approve:

- tunnel or public endpoint approach
- webhook authentication approach
- exact Retell event to test
- screenshot redaction rules
- synthetic-only caller scenario
- whether additional workflow changes are allowed
