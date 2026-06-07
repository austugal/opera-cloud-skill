# Quick Reference Cheatsheet

Generic, public patterns. No live endpoints, property codes, or credentials. Replace every
placeholder in angle brackets with values for the specific tenant, and confirm exact paths against
the OHIP Developer Portal spec for the running release.

## Authentication flow (concept)

```
1. POST client_key + client_secret to the OAuth token endpoint
   -> receive a short-lived bearer token
2. On every API call send:
   Authorization: Bearer <token>
   x-app-key: <application_key>
   Content-Type: application/json
   Accept: application/json
   + property/hotel context on property-scoped calls
3. Cache the token; refresh before expiry; never request one per call
```

## Common operation patterns (described, not literal paths)

```
# Reservations
- Search reservations by arrival date / status -> returns summaries
- Get one reservation by id -> full detail
- Create / modify / cancel a reservation

# Cashiering
- Post a charge or payment to a folio window
- Read folio and transactions
- Transfer / route charges between windows

# Rooms / housekeeping
- Read room status; update room status (clean/dirty/inspected/OOO/OOS)

# Rates
- Read rate plan and rate detail by date
- Bulk rate load -> use the asynchronous API

# Inventory / availability
- Read availability by date and room type

# Blocks
- Create a block; read pickup vs allocation; manage cut-off
```

## Async pattern (concept)

```
1. Submit to the async endpoint -> 202 Accepted + tracking handle
2. Poll the status resource at a sensible interval (do not tight-loop)
3. On complete -> retrieve the result from the completion location
4. On failed -> read the status body for the reason
```

## Status-code quick map

```
200/201 ok        202 async accepted, poll
400 bad payload   401 refresh token once, retry
403 not entitled  404 wrong id/path
409 state conflict 422 business-rule failure (read body)
429 rate limited, back off
500/503 server, retry with backoff + jitter
```

## Decision shortcuts

- Bulk or long-running operation -> use the asynchronous API, not many synchronous calls.
- Need near-real-time change notification -> Business Events (streaming or polling subscription).
- Channel availability/rates/restrictions -> Distribution ARI push.
- Inbound channel booking -> reservation notification (ResNotif).
- "OPERA will not let me do X" -> check the relevant OPERA Control for the property first.
- Rate looks wrong -> check if it is derived, then check restrictions, then check channel mapping.
- Export file rejected downstream -> validate against the target schema/format, not just that OPERA
  produced a file.

## Hard rules

- Build and validate in UAT; promote to production only after documented approval.
- Never log or commit tokens, secrets, or authorization headers.
- Treat guest profiles and folios as personal data; minimise extraction and retention.
- State the OPERA release an answer depends on; confirm version-specific behaviour against the spec.
