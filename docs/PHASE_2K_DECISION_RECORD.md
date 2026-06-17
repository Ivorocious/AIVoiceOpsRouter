# Phase 2K Decision Record: External Voice Platform Testing

## Decision Status

```text
Pending Mission Control final decision.
```

Phase 2K was not executed against a real AI voice platform during the portfolio build.

## Context

The AI Voice Ops Router workflow is running on locally hosted n8n.

Local n8n test webhooks can be reached from the same machine using a local URL, but external AI voice platforms cannot call `localhost` directly. Platforms such as Vapi, Retell, Bland AI, Synthflow, or similar providers need a publicly reachable HTTPS endpoint.

## What We Did In Phase 2K

We intentionally treated Phase 2K as a planning and documentation phase.

Actions completed:

- documented the local n8n limitation
- documented synthetic-only testing as the safest portfolio proof
- documented public tunnel options for later testing
- documented public n8n deployment as a production-style option
- kept all testing synthetic and local
- did not create or expose a tunnel URL
- did not configure a real AI voice platform
- did not activate production webhook behavior
- did not publish webhook URLs, tunnel URLs, access tokens, or account details

## Option 1: Keep Synthetic-Only Portfolio Proof

Status:

```text
Recommended current option.
```

This keeps the project focused on demonstrating workflow architecture without introducing external platform risk.

What it proves:

- webhook intake
- normalization
- validation
- classification
- route handling
- branch convergence
- structured logging preparation
- webhook response behavior

Benefits:

- no public endpoint required
- no real caller data
- no tunnel exposure
- no external provider dependency
- safest for GitHub and portfolio publishing

Limitations:

- does not prove a live voice platform can reach the workflow
- does not test provider-specific payload differences
- does not test external retry behavior

## Option 2: Temporary Tunnel Test

Potential tools:

- ngrok
- Cloudflare Tunnel
- LocalTunnel
- Tailscale Funnel

Example path:

```text
AI voice platform
  -> temporary public tunnel URL
  -> local n8n webhook
  -> AI Voice Ops Router
```

Requirements before approval:

- webhook authentication or shared secret
- temporary tunnel only
- synthetic caller data only
- no production credentials
- no public screenshots with tunnel URL
- tunnel shut down immediately after test
- provider retry behavior understood

Benefits:

- proves an external service can call local n8n
- avoids full public deployment
- useful for a controlled demo

Risks:

- exposes a local service to the internet temporarily
- tunnel URL can leak in screenshots or logs
- provider retries may repeat test executions
- weak authentication could allow unwanted calls

## Option 3: Public n8n Deployment

Potential targets:

- VPS
- cloud VM
- private Docker host
- managed n8n
- reverse proxy with HTTPS

Requirements before approval:

- HTTPS
- authentication
- credential isolation
- firewall/rate-limit plan
- backup plan
- environment variable and secrets management
- test-only data policy

Benefits:

- closer to production architecture
- avoids temporary tunnel dependency
- supports more realistic provider testing

Risks:

- increases infrastructure complexity
- requires security hardening
- requires ongoing maintenance
- higher chance of publishing sensitive URLs or configuration

## Recommendation

For the current portfolio version, keep Phase 2K as:

```text
Skipped intentionally for portfolio safety.
Documented future tunnel/public deployment options.
```

Mission Control should decide whether the next iteration requires live external voice-platform testing.

If Mission Control approves live testing, the recommended next step is a temporary tunnel with synthetic payloads only, webhook authentication, and no published tunnel screenshots.

## Final Decision Needed From Mission Control

Mission Control should choose one:

1. Keep synthetic-only proof for this portfolio project.
2. Approve a temporary tunnel test with synthetic data only.
3. Approve a public n8n deployment plan for production-style testing.

No live external voice-platform test should be started until that decision is made.
