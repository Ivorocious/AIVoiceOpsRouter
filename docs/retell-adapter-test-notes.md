# Retell Adapter Test Notes

These notes document the expected behavior for Phase 2L-1 Full synthetic Retell-style adapter testing.

The tests use local synthetic JSON payloads only. No Retell account, credentials, tunnel, public endpoint, or live webhook is used.

## Payloads Tested

| Payload | Format | Expected validation | Expected route |
| --- | --- | --- | --- |
| `test-payloads/sales.json` | Native synthetic | valid | `sales_follow_up` |
| `test-payloads/urgent.json` | Native synthetic | valid | `urgent_human_review` |
| `test-payloads/support.json` | Native synthetic | valid | `support_queue` |
| `test-payloads/billing.json` | Native synthetic | valid | `billing_queue` |
| `test-payloads/manual-review.json` | Native synthetic | invalid | `manual_review` |
| `test-payloads/general.json` | Native synthetic | valid | `general_queue` |
| `test-payloads/retell-style-billing-event.json` | Retell-style synthetic | valid | `billing_queue` |
| `test-payloads/retell-style-urgent-support-event.json` | Retell-style synthetic | valid | `urgent_human_review` |
| `test-payloads/retell-style-invalid-event.json` | Retell-style synthetic | invalid | `manual_review` |

## Expected Normalization Behavior

Native synthetic payloads should keep the existing flat schema and receive these adapter metadata fields:

```json
{
  "source_provider": "native_synthetic",
  "adapter_status": "native_passthrough",
  "adapter_warnings": [],
  "original_event_type": ""
}
```

Retell-style synthetic payloads should be unwrapped from `call` and mapped into the same internal `call` object used by the native payloads.

Expected adapter metadata for complete Retell-style payloads:

```json
{
  "source_provider": "retell_style_synthetic",
  "adapter_status": "normalized",
  "adapter_warnings": [],
  "original_event_type": "call_analyzed"
}
```

Expected adapter metadata for incomplete Retell-style payloads:

```json
{
  "source_provider": "retell_style_synthetic",
  "adapter_status": "normalized_with_warnings",
  "original_event_type": "call_analyzed"
}
```

## Expected Route Outcomes

### Retell-Style Billing Event

Expected normalized highlights:

- `call_id`: `synthetic_retell_billing_001`
- `voice_platform`: `synthetic_retell_style`
- `call_intent`: `billing`
- `urgency`: `low`
- `callback_requested`: `true`

Expected workflow behavior:

- validation: `valid`
- classification intent: `billing`
- classification urgency: `medium`
- route: `billing_queue`

The normalized urgency remains `low`, but the classifier raises the final urgency to `medium` because the transcript contains a follow-up signal.

### Retell-Style Urgent Support Event

Expected normalized highlights:

- `call_id`: `synthetic_retell_urgent_support_001`
- `voice_platform`: `synthetic_retell_style`
- `call_intent`: `support`
- `urgency`: `emergency`
- `call_duration_seconds`: computed from start/end timestamps if direct duration is absent

Expected workflow behavior:

- validation: `valid`
- classification intent: `emergency` or support with emergency urgency signal
- classification urgency: `emergency`
- route: `urgent_human_review`

### Retell-Style Invalid Event

Expected normalized highlights:

- missing `call_id`
- missing caller identity
- missing transcript
- missing summary
- missing intent
- urgency defaults to `low`
- callback requested defaults to `false`

Expected workflow behavior:

- validation: `invalid`
- validation errors include missing `call_id`, caller identity, and call content
- route: `manual_review`
- adapter status: `normalized_with_warnings`

## Known Limitations

- The adapter is based on synthetic Retell-style payloads, not a captured live Retell webhook payload.
- Webhook authentication and signature verification are not implemented in this phase.
- No retry or idempotency handling has been added for live provider webhooks.
- Transcript object parsing is not implemented; the adapter expects a plain transcript string or compatible text field.
- Real Retell field names may differ and must be verified during Phase 2L-2.

## Safe Interpretation

Phase 2L-1 Full proves the workflow can normalize provider-style wrapped synthetic payloads into the existing AI Voice Ops Router schema.

It does not prove live Retell integration.
