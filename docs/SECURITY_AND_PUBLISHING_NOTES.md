# Security And Publishing Notes

## Publishing Standard Used

The repository was prepared for public portfolio use with synthetic data only.

The published package should not contain:

- real webhook URLs
- n8n test webhook paths
- tunnel URLs
- production webhook URLs
- access tokens
- API keys
- production credentials
- private n8n instance IDs
- real caller names
- real phone numbers
- real email addresses
- real transcripts

## Sanitized Artifacts

The full workflow export is stored at:

```text
workflow/AI Voice Ops Router.sanitized.json
```

The Phase 2B reference export is stored at:

```text
workflow/AI Voice Ops Router.phase-2b.sanitized.json
```

Both are sanitized before publication.

## Screenshots

Published screenshots:

- `screenshots/workflow-overview-redacted.png`
- `screenshots/sample-response-redacted.png`
- `screenshots/structured-log-redacted.png`

The sample response screenshot was redacted before publishing because it originally showed a local n8n test webhook URL.

## Test Data Policy

All payloads in `test-payloads/` are synthetic.

The phone numbers, names, companies, emails, transcripts, and summaries are fake test data intended for workflow demonstration only.

## Local n8n Policy

The workflow was tested through local n8n test webhook execution.

The workflow should remain inactive unless intentionally moved into a controlled test or production-style environment.

## Future Live Testing Requirements

Before any future tunnel or public n8n test:

- Mission Control must approve the test path.
- Use synthetic data only unless explicit approval is given.
- Add webhook authentication or a shared secret.
- Do not expose tunnel URLs in screenshots.
- Do not commit provider dashboard screenshots with private account details.
- Shut down temporary tunnels after testing.
- Do not publish raw n8n exports.
