# Live Integration Plan

This document describes how AI Voice Ops Router could be connected to a live external AI voice platform later.

No live external voice platform is currently connected. This plan is readiness documentation only.

## Why External Voice Platforms Cannot Call Localhost

The current workflow runs on locally hosted n8n.

External platforms such as Vapi, Retell, Bland AI, Synthflow, or similar services run outside the local machine. They cannot call a local-only n8n webhook because `localhost` refers to the platform's own runtime environment, not the developer's computer.

To receive live platform webhooks, n8n needs a publicly reachable HTTPS endpoint.

## Option A: Secure Temporary Tunnel

Use a temporary tunnel only for controlled testing with synthetic data.

Potential tunnel categories:

- developer tunnel
- zero-trust tunnel
- temporary HTTPS forwarding tool

Requirements:

- temporary URL only
- synthetic call data only
- webhook authentication enabled
- no tunnel URL in screenshots
- no tunnel URL committed to GitHub
- tunnel stopped after testing
- logs reviewed for unexpected calls

Recommended use:

- one short live-platform proof test
- provider webhook reachability validation
- response timing validation

Not recommended for:

- production
- real caller data
- unattended long-running testing

## Option B: Public n8n Deployment On VPS

Use a public deployment only when production-style testing is required.

Minimum requirements:

- HTTPS
- reverse proxy or managed TLS
- n8n authentication
- firewall rules
- secrets stored outside workflow JSON
- webhook authentication
- backups
- monitoring
- log retention plan

Recommended use:

- production-style provider testing
- longer-running demos
- controlled staging environment

Not recommended until:

- credential handling is reviewed
- webhook authentication is defined
- retry behavior is understood
- redaction and publishing policy is approved

## Webhook Authentication Plan

Future live testing should add authentication before exposing the webhook.

Possible approaches:

- shared secret header
- signed webhook payload
- bearer token stored as an environment variable
- IP allowlisting when supported by the provider

Recommended minimum:

```text
Require a shared secret header and reject requests when the header is missing or invalid.
```

Implementation note:

The repository should document the header name and validation behavior, but never commit the actual secret value.

## Retry And Error Handling Plan

External providers may retry webhook delivery when they receive timeouts or non-success responses.

Future workflow behavior should define:

- timeout target
- idempotency using `call_id`
- duplicate handling
- retry-safe logging
- clear failure response body
- manual review route for ambiguous failures

Recommended response policy:

- return `200` for accepted and routed payloads
- return a controlled error response for malformed requests
- route unclear payloads to manual review instead of failing silently

## Privacy And Redaction Checklist

Before any live test:

- use synthetic caller data unless explicitly approved otherwise
- do not publish webhook URLs
- do not publish tunnel URLs
- do not publish provider dashboard screenshots with account details
- do not commit access tokens
- do not commit real phone numbers
- do not commit real transcripts
- do not commit raw unsanitized workflow exports
- redact logs before screenshots

## Successful Live Test Proof

A live test would count as successful only if all of the following are true:

- external platform sends a synthetic call-result webhook
- n8n receives the payload through a public HTTPS endpoint
- authentication succeeds
- payload maps into the documented webhook contract
- workflow validates the payload
- workflow classifies the call
- workflow routes to the expected handler
- `Prepare Call Log` creates the expected structured log
- `Respond to Webhook` returns the expected compact JSON response
- no secrets or private data are exposed in screenshots, logs, or commits

## Current Recommendation

Keep the published portfolio version synthetic-only.

Use this plan to show readiness for live provider integration without claiming that live integration has already happened.
