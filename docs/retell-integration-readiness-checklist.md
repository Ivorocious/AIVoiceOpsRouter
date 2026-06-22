# Retell Integration Readiness Checklist

This checklist prepares AI Voice Ops Router for a future controlled Retell AI live test.

It does not indicate that Retell is already connected.

## Retell Account Setup Checklist

- [X] Create or access Retell account.
- [X] Confirm workspace access and role permissions.
- [X] Confirm whether test calls are available without production deployment.
- [X] Confirm whether web-call testing is available.
- [X] Confirm billing or phone-number requirements before any phone-call test.
- [ ] Confirm where webhook settings live in the current Retell dashboard.
- [ ] Confirm where post-call analysis fields are configured.
- [ ] Do not add credentials to the repository.
- [ ] Do not capture billing or account-private screenshots.

## Agent Setup Checklist

- [ ] Create a synthetic intake agent.
- [ ] Use the provider-neutral prompt from `voice-agent-prompt.md` as the starting point.
- [ ] Configure caller greeting.
- [ ] Configure data collection for caller identity, company, intent, urgency, callback preference, transcript, and summary.
- [ ] Configure fallback behavior for unclear callers.
- [ ] Configure human handoff language without connecting a real transfer destination.
- [ ] Configure ending behavior.
- [ ] Keep all examples synthetic.
- [ ] Do not configure real client data.

## Synthetic Caller Scenario Checklist

- [ ] Sales inquiry scenario.
- [ ] Urgent support scenario.
- [ ] Normal support scenario.
- [ ] Billing question scenario.
- [ ] Incomplete or unclear caller scenario.
- [ ] General inquiry scenario.
- [ ] Confirm each scenario has expected route.
- [ ] Confirm no real phone numbers are used in scenario docs.

## Webhook Setup Checklist

- [ ] Decide whether to use account-level or agent-level webhook.
- [ ] Prefer `call_analyzed` for the first routing proof.
- [ ] Confirm whether `call_ended` is needed as a fallback.
- [ ] Disable unnecessary events for the first proof where possible.
- [ ] Confirm Retell sends `event` and `call` payload structure.
- [ ] Confirm where transcript appears.
- [ ] Confirm where post-call analysis fields appear.
- [ ] Confirm webhook retry behavior in n8n logs.
- [ ] Do not commit webhook URL.

## n8n Tunnel Or Public Endpoint Checklist

Do not start this until Mission Control approves live testing.

- [ ] Decide between secure temporary tunnel and public n8n deployment.
- [ ] Use synthetic data only.
- [ ] Confirm endpoint is HTTPS.
- [ ] Confirm webhook path is not published.
- [ ] Confirm endpoint is disabled or tunnel is stopped after test.
- [ ] Confirm n8n workflow remains inactive until deliberate test execution.
- [ ] Confirm screenshots hide endpoint and execution URLs.

## Security Checklist

- [ ] No Retell API key in repo.
- [ ] No webhook signing key in repo.
- [ ] No local n8n URL in repo.
- [ ] No tunnel URL in repo.
- [ ] No production webhook URL in repo.
- [ ] No real caller phone numbers.
- [ ] No real caller emails.
- [ ] No real transcripts.
- [ ] No Retell billing/account screenshots.
- [ ] Webhook signature verification plan documented.
- [ ] Duplicate event handling plan documented.
- [ ] Redaction checklist reviewed before screenshots.

## Test Execution Checklist

- [ ] Start from synthetic web-call or dashboard test if available.
- [ ] Trigger one scenario at a time.
- [ ] Confirm Retell call completes.
- [ ] Confirm expected webhook event fires.
- [ ] Confirm n8n receives payload.
- [ ] Confirm adapter maps Retell payload to existing workflow schema.
- [ ] Confirm validation status.
- [ ] Confirm classification output.
- [ ] Confirm route key.
- [ ] Confirm route handler.
- [ ] Confirm `Prepare Call Log` output.
- [ ] Confirm response or acknowledgment behavior.
- [ ] Record only redacted evidence.

## Evidence And Screenshot Checklist

Collect only after live testing is approved:

- [ ] Retell agent screen with no private details visible.
- [ ] Retell post-call analysis field setup with no secrets.
- [ ] Retell webhook event settings with endpoint redacted.
- [ ] Retell call history showing synthetic test call, caller data redacted.
- [ ] n8n execution showing received payload, webhook URL hidden.
- [ ] n8n classifier output.
- [ ] n8n route handler output.
- [ ] n8n response or acknowledgment output.
- [ ] README or portfolio screenshot clearly saying live Retell test status.

## Resume-Claim Checklist

Safe before live Retell testing:

- [ ] "Designed an n8n AI voice call-routing workflow."
- [ ] "Prepared Retell AI integration documentation."
- [ ] "Built provider-neutral webhook payload readiness docs."
- [ ] "Tested routing with synthetic local payloads."

Not safe before live Retell testing:

- [ ] "Integrated Retell AI with n8n."
- [ ] "Deployed a production Retell webhook."
- [ ] "Handled live Retell calls."
- [ ] "Processed real customer calls."
- [ ] "Connected Retell to production automation."

Safe only after successful live test:

- [ ] "Completed a controlled Retell webhook test with synthetic data."
- [ ] "Mapped Retell call analysis output into n8n routing."
- [ ] "Validated Retell call-result event handling in n8n."
