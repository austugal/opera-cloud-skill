# Error Handling, Status Codes, and Rate Limits

## Read the body, not just the status

OPERA Cloud / OHIP APIs return a structured error body alongside the HTTP status. The status tells
you the category; the body tells you what actually went wrong (which field, which rule, which
entity). Always parse and log the error body. Acting on the status alone leads to wrong fixes.

## HTTP status codes you will see

| Status | Meaning | Typical cause and action |
|--------|---------|--------------------------|
| 200 / 201 | Success | Request succeeded (201 on create). |
| 202 | Accepted | Async job accepted; poll for completion. |
| 400 | Bad request | Malformed payload or invalid field. Fix the request; do not retry unchanged. |
| 401 | Unauthorized | Missing/expired token or bad credentials. Refresh token once and retry; repeated 401 is a credential/scope problem. |
| 403 | Forbidden | Authenticated but not permitted (scope, property entitlement, or a control). Check entitlements; do not retry blindly. |
| 404 | Not found | Wrong path or the entity does not exist. Verify the identifier and the endpoint. |
| 405 | Method not allowed | Wrong HTTP verb for the endpoint. |
| 409 | Conflict | State conflict (for example duplicate or concurrent modification). Re-read state and reconcile. |
| 422 | Unprocessable entity | Validation/business-rule failure. Read the body for the rule violated. |
| 429 | Too many requests | Rate limit hit. Back off and retry per the limit guidance below. |
| 500 | Server error | Server-side failure. Retry with backoff; if persistent, escalate. |
| 503 | Service unavailable | Temporary unavailability/maintenance. Retry with backoff. |

## Distinguishing retryable from non-retryable

- Retryable: 429, 500, 503, and transient network failures. Use backoff.
- Not retryable without changing the request: 400, 401 (beyond one refresh), 403, 404, 405, 409,
  422. Fix the cause first.

## Rate limits

OHIP enforces rate limits. When exceeded, calls return 429. Practical handling:

- Respect any retry-after guidance the response provides.
- Use exponential backoff with jitter rather than a fixed immediate retry.
- Smooth bursty workloads (for example a bulk load) into a steady rate rather than firing all calls
  at once.
- For large operations, prefer the asynchronous APIs over many rapid synchronous calls.

### Exponential backoff pattern (concept)

1. On a retryable error, wait an initial delay.
2. Double the delay on each subsequent retry, up to a cap.
3. Add random jitter so many clients do not retry in lockstep.
4. Stop after a maximum number of attempts and surface the failure.

## Common error scenarios

- 401 immediately after working calls: the token expired; refresh and retry once.
- 403 on a specific property only: the integration is not entitled to that property, or a control
  blocks the action.
- 422 on create/modify: a business rule rejected the payload; the body names the rule.
- 429 under load: you are exceeding the rate limit; throttle and back off.
- Async job stuck: you are polling too soon or too often, or the job genuinely failed; read the job
  status body.

## Logging guidance

- Log the status, the error body, and a correlation/request identifier if the API returns one.
- Never log tokens, secrets, or full authorization headers.
- Keep enough context to reproduce: endpoint, method, property context, and the sanitised payload.
