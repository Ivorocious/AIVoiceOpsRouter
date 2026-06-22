# Retell-Style Payload

This document defines a provider-neutral, Retell-inspired synthetic payload for AI Voice Ops Router.

It is not copied from a live Retell account. It is a safe planning artifact based on the expected shape of a voice-platform call-result event.

## Purpose

The workflow accepts the original flat synthetic payload and synthetic Retell-style event wrappers. Real Retell webhook events may arrive in an event wrapper with a nested `call` object and optional post-call analysis fields.

This document bridges those ideas without claiming live Retell integration.

## Required Fields For AI Voice Ops Router

The existing workflow needs these mapped fields:

| Existing field | Required | Source idea |
| --- | --- | --- |
| `call_id` | yes | Retell call identifier |
| `caller_name` | one identity field required | dynamic variable, metadata, or analysis field |
| `caller_phone` | one identity field required | phone call metadata, redacted in docs |
| `caller_email` | one identity field required | dynamic variable, metadata, or analysis field |
| `company` | no | dynamic variable or analysis field |
| `call_intent` | one content field required | custom analysis selector or summary |
| `urgency` | no but useful | custom analysis selector |
| `callback_requested` | no but useful | custom Boolean analysis |
| `preferred_callback_time` | no | custom text analysis |
| `transcript` | one content field required | call transcript |
| `summary` | one content field required | custom text analysis or call summary |
| `call_started_at` | no | call timestamp |
| `call_duration_seconds` | no | computed from timestamps or call metadata |
| `voice_platform` | yes | `retell` for future live tests, `synthetic_retell_style` for this artifact |
| `call_status` | no but useful | Retell call status or outcome |

## Optional Retell-Inspired Fields

These may be useful later:

- `event`
- `agent_id`
- `direction`
- `disconnection_reason`
- `metadata`
- `analysis`
- `transcript_object`
- `webhook_received_at`

Do not rely on exact names until a real Retell webhook payload is inspected.

## Example Provider-Neutral Retell-Style Payload

```json
{
  "event": "call_analyzed",
  "provider": "retell_planned",
  "call": {
    "call_id": "synthetic_retell_call_001",
    "agent_id": "synthetic_agent_placeholder",
    "direction": "inbound",
    "call_status": "completed",
    "call_started_at": "2026-06-22T09:00:00Z",
    "call_ended_at": "2026-06-22T09:03:20Z",
    "call_duration_seconds": 200,
    "disconnection_reason": "caller_ended",
    "caller": {
      "name": "Synthetic Caller",
      "phone": "<synthetic_phone_placeholder>",
      "email": "caller@example.test",
      "company": "Example Operations Co"
    },
    "transcript": "Caller asked about invoice details and requested a callback from billing during the next business day.",
    "summary": "Synthetic caller requested billing follow-up about an invoice.",
    "analysis": {
      "detected_intent": "billing",
      "detected_urgency": "low",
      "callback_requested": true,
      "preferred_callback_time": "next business day",
      "human_review_required": false,
      "call_outcome": "routed"
    },
    "metadata": {
      "test_mode": true,
      "source": "phase_2l_0_planning"
    }
  }
}
```

## Equivalent Existing Workflow Payload

The implemented synthetic adapter maps the example above into the current workflow shape:

```json
{
  "call_id": "synthetic_retell_call_001",
  "caller_name": "Synthetic Caller",
  "caller_phone": "<synthetic_phone_placeholder>",
  "caller_email": "caller@example.test",
  "company": "Example Operations Co",
  "call_intent": "billing",
  "urgency": "low",
  "callback_requested": true,
  "preferred_callback_time": "next business day",
  "transcript": "Caller asked about invoice details and requested a callback from billing during the next business day.",
  "summary": "Synthetic caller requested billing follow-up about an invoice.",
  "call_started_at": "2026-06-22T09:00:00Z",
  "call_duration_seconds": 200,
  "voice_platform": "synthetic_retell_style",
  "call_status": "completed"
}
```

## Expected n8n Response

```json
{
  "success": true,
  "call_id": "synthetic_retell_call_001",
  "route": "billing_queue",
  "route_name": "Billing Queue",
  "classification": {
    "intent": "billing",
    "urgency": "medium"
  },
  "validation_status": "valid",
  "errors": [],
  "warnings": [],
  "message": "Call received and routed."
}
```

## Fields That May Differ After Inspecting Real Retell Webhooks

Expect possible differences in:

- event names selected for delivery
- timestamp format
- transcript field names
- transcript object structure
- post-call analysis object names
- call status values
- disconnection reason values
- dynamic variable location
- metadata location
- phone number formatting
- whether empty analysis fields are omitted

## Adapter Notes For Phase 2L-1

Phase 2L-1 Full implements this mapping inside the existing `Normalize Call Payload` node:

```text
Retell-style event wrapper
  -> existing Normalize Call Payload node
  -> provider-neutral AI Voice Ops Router payload
  -> existing validation, classification, routing, logging, and response nodes
```

This remains synthetic-only support. It does not mean a live Retell webhook has been received.
