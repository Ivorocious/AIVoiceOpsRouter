# Voice Webhook Contract

This document defines the expected call-result webhook contract for AI Voice Ops Router.

The current workflow is tested with synthetic local payloads only. This contract is provider-neutral and is intended to make the repository ready for a future AI voice platform integration without claiming that a live platform has already been connected.

## Expected Payload Schema

```json
{
  "call_id": "string",
  "caller_name": "string",
  "caller_phone": "string",
  "caller_email": "string",
  "company": "string",
  "call_intent": "string",
  "urgency": "low | medium | high | emergency | unknown",
  "callback_requested": true,
  "preferred_callback_time": "string",
  "transcript": "string",
  "summary": "string",
  "call_started_at": "ISO-8601 timestamp string",
  "call_duration_seconds": 120,
  "voice_platform": "synthetic | vapi | retell | bland_ai | synthflow | other",
  "call_status": "completed | failed | abandoned | voicemail | unknown"
}
```

## Required Fields

The n8n validation logic expects:

- `call_id`
- at least one caller identity field:
  - `caller_name`
  - `caller_phone`
  - `caller_email`
- at least one call content field:
  - `call_intent`
  - `summary`
  - `transcript`
- `voice_platform`

Missing caller identity or missing call content routes the call to manual review.

## Optional Fields

Optional but recommended:

- `company`
- `urgency`
- `callback_requested`
- `preferred_callback_time`
- `call_started_at`
- `call_duration_seconds`
- `call_status`

Optional fields may create warnings instead of hard failures depending on the validation rule.

## Sample Synthetic Payload

This example uses placeholders and synthetic values only.

```json
{
  "call_id": "test_voice_ready_001",
  "caller_name": "Synthetic Caller",
  "caller_phone": "<synthetic_phone_placeholder>",
  "caller_email": "caller@example.test",
  "company": "Example Operations Co",
  "call_intent": "billing question",
  "urgency": "low",
  "callback_requested": true,
  "preferred_callback_time": "next business day",
  "transcript": "I have a question about an invoice and would like someone from billing to follow up.",
  "summary": "Caller asked for billing follow-up about an invoice.",
  "call_started_at": "2026-06-18T09:00:00Z",
  "call_duration_seconds": 96,
  "voice_platform": "synthetic",
  "call_status": "completed"
}
```

## Example Transcript

```text
Caller said they had a question about an invoice and wanted someone from billing to follow up during the next business day.
```

## Example Call Summary

```text
Synthetic caller requested billing follow-up about an invoice. Urgency is low and callback is requested.
```

## Expected n8n Response Schema

```json
{
  "success": true,
  "call_id": "test_voice_ready_001",
  "route": "billing_queue",
  "route_name": "Billing Queue",
  "classification": {
    "intent": "billing",
    "urgency": "low"
  },
  "validation_status": "valid",
  "errors": [],
  "warnings": [],
  "message": "Call received and routed."
}
```

## Failure Or Manual Review Response

```json
{
  "success": false,
  "call_id": "test_unclear_001",
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

## Provider Mapping Notes

Future provider integrations should map their native fields into this contract before entering the existing workflow.

Examples:

- platform call ID -> `call_id`
- transcript text -> `transcript`
- platform summary -> `summary`
- assistant intent output -> `call_intent`
- platform call completion state -> `call_status`
- provider name -> `voice_platform`

Provider-specific adapters should be documented separately if they are added later.
