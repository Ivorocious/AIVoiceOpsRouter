# Voice Debugging Guide

This guide explains how to debug AI Voice Ops Router payload and routing issues during synthetic local testing or future approved live testing.

No live external voice platform is currently connected.

## General Debugging Flow

Use this inspection order in n8n:

1. `Webhook`
2. `Normalize Call Payload`
3. `Validate Call Payload`
4. `Classify Intent and Urgency`
5. `Route Call`
6. selected route handler
7. `Prepare Call Log`
8. `Respond to Webhook`

Check each node output before changing logic.

## Missing Caller Name

Expected behavior:

- Missing `caller_name` alone should not fail validation if `caller_phone` or `caller_email` exists.
- If all caller identity fields are missing, validation should fail and route to manual review.

Inspect:

- `Webhook.body.caller_name`
- `Normalize Call Payload.call.caller_name`
- `Validate Call Payload.validation.errors`

Likely cause:

- voice agent did not ask for name
- provider field was mapped incorrectly
- caller declined to provide identity

## Missing Phone

Expected behavior:

- Missing `caller_phone` alone should not fail validation if name or email exists.
- Missing all identity fields routes to manual review.

Inspect:

- `Normalize Call Payload.call.caller_phone`
- `Validate Call Payload.validation.status`

Portfolio note:

Do not publish real phone numbers in screenshots or examples.

## Empty Transcript

Expected behavior:

- Empty transcript is acceptable if `call_intent` or `summary` exists.
- Empty transcript plus empty intent and empty summary routes to manual review.

Inspect:

- `Webhook.body.transcript`
- `Normalize Call Payload.call.transcript`
- `Validate Call Payload.validation.errors`

Likely cause:

- provider did not return transcript
- call was too short
- transcript setting disabled
- synthetic payload omitted call content

## Unclear Intent

Expected behavior:

- Ambiguous or missing intent should route to manual review when validation fails.
- Weak but valid general inquiries may route to general queue.

Inspect:

- `Classify Intent and Urgency.classification.intent`
- `Classify Intent and Urgency.classification.intent_reason`
- `Route Call.routing.route_key`

Likely cause:

- transcript is too vague
- summary lacks routing keywords
- voice agent did not ask a clarifying question

## Incorrect Urgency

Expected behavior:

- emergency and high urgency override normal routing
- medium and low urgency follow intent-based routing

Inspect:

- `Normalize Call Payload.call.urgency`
- `Classify Intent and Urgency.classification.urgency`
- `Classify Intent and Urgency.classification.urgency_reason`
- `Route Call.routing.route_key`

Likely cause:

- caller used urgent language in transcript
- keyword list is too broad
- voice agent over-classified urgency
- payload urgency conflicts with transcript

## Webhook Timeout

Expected behavior:

- The local test webhook should return a JSON response after `Respond to Webhook`.

Inspect:

- `Webhook` response mode
- `Respond to Webhook` execution
- n8n execution errors
- local n8n runtime logs

Likely cause:

- workflow was not manually executing
- test webhook URL expired after prior test
- `Respond to Webhook` not connected
- response mode not set to use response node
- future live provider timeout limit is shorter than workflow execution time

## Malformed JSON

Expected behavior:

- n8n may reject malformed JSON before downstream nodes execute.

Inspect:

- request body in the test client
- `Content-Type` header
- `Webhook` node input
- n8n execution error message

Likely cause:

- missing quote
- trailing comma
- invalid Boolean/string format
- request sent as text instead of JSON

## Duplicate call_id

Expected behavior:

- Current portfolio workflow does not enforce uniqueness.
- Duplicate detection should be added before production-style testing.

Inspect:

- `call.call_id`
- future log storage or deduplication layer

Recommended future behavior:

- store processed `call_id` values
- reject or flag duplicates
- keep duplicate calls in manual review if unsure

## Failed Downstream Routing

Expected behavior:

- `Route Call` sends one item to exactly one handler based on `routing.route_key`.

Inspect:

- `Classify Intent and Urgency.routing.route_key`
- `Route Call` output tab
- handler node that receives the item
- `Prepare Call Log.route_handler`

Likely cause:

- route key typo
- Switch condition mismatch
- rule ordering issue
- classifier keyword collision
- handler connection attached to wrong Switch output

## Known Routing Lesson

During synthetic testing, a billing payload containing `billing issue` initially routed to support because `issue` was treated as a support keyword. The classifier was corrected so billing-specific keywords are evaluated before generic support keywords.

## What To Capture For Debug Reports

When reporting a bug, include:

- synthetic payload used
- expected route
- actual route
- validation output
- classification output
- `routing.route_key`
- selected handler node
- response JSON

Do not include real webhook URLs, tunnel URLs, credentials, real phone numbers, or private account details.
