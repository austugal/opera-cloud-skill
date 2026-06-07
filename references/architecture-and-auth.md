# Architecture, Environments, and Authentication

## OPERA Cloud architecture

OPERA Cloud is Oracle's multi-tenant, browser-delivered Property Management System running on
Oracle Cloud Infrastructure. Conceptually it has four layers.

- Presentation layer: the responsive browser UI and OPERA mobile clients.
- Application layer: PMS business logic — reservations, cashiering, profiles, night audit,
  housekeeping, blocks, events.
- Integration layer: the Oracle Hospitality Integration Platform (OHIP), an API gateway that
  exposes REST APIs, asynchronous APIs, Business Events, and streaming.
- Data layer: per-tenant property data isolated within Oracle Cloud.

External systems (channel managers, revenue management, payment, door locks, point of sale,
back-office finance) integrate through OHIP, not by direct database access.

## Environment types

- Non-production / UAT (user acceptance testing): used for build, configuration, and validation.
- Production (PRD): the live property environment.

Operating discipline: configuration and formula work is built and tested in UAT, then promoted to
PRD only after sign-off. Changes to live PRD configuration without prior UAT validation and
approval are the most common source of avoidable production incidents.

A typical promotion workflow:
1. Export the configuration shell from PRD (read-only export, no live impact).
2. Import the shell into UAT.
3. Run a property refresh in UAT so data aligns.
4. Build or amend the configuration / formulas in UAT.
5. Validate output against the expected format or downstream system.
6. Obtain documented approval from the responsible back-office or finance owner.
7. Apply to PRD.

## OHIP: the integration platform

OHIP is the gateway through which all programmatic access flows. It provides:

- REST APIs grouped into functional modules (see `api-modules.md`).
- Asynchronous APIs for long-running operations (see `async-streaming-events.md`).
- Business Events and streaming for near-real-time change notification.
- Distribution APIs for channel connectivity (see `distribution-channels.md`).

There are two related but distinct portals:
- The OHIP Developer Portal, where integrations are registered, API documentation is browsed,
  and credentials are managed.
- The OHIP administration surface for affiliated customers, analytics, and streaming subscription
  management.

## Authentication: OAuth 2.0 client credentials

OHIP authenticates with the OAuth 2.0 client credentials grant. The flow:

1. The integration holds three pieces of registered identity: a client key (client ID), a client
   secret, and an application key (the OHIP app key issued for the integration).
2. It posts the client key and secret to the token endpoint to obtain a short-lived bearer token.
3. Every subsequent API call carries the bearer token plus the application key and context headers.

This skill never contains live values for any of these. They are issued per integration and per
tenant and must be stored as secrets, never committed to source control or shared in plain text.

### Standard required headers

Calls to property APIs generally require, at minimum:

- `Authorization: Bearer <token>` — the OAuth token.
- `x-app-key: <application key>` — the OHIP application key for the integration.
- `Content-Type: application/json` and `Accept: application/json` for JSON payloads.
- A hotel / property context identifier on property-scoped calls (the property code the request
  applies to).

The exact header names and any additional context headers are defined per release in the OHIP
Developer Portal API documentation; confirm against the spec for the version in use rather than
assuming.

### Token strategy (production patterns)

- Cache the token and reuse it until shortly before expiry. Do not request a new token per call.
- Refresh proactively a margin before the stated expiry to avoid mid-request expiry.
- Handle a 401 by refreshing once and retrying; treat repeated 401s as a credential or scope
  problem, not a transient error.
- Keep the client secret in a secrets manager or environment variable, never in code or config
  committed to a repository.

### OCIM client management

OCIM is the client management surface used to administer integration client identities and their
access. Practical notes that are publicly documented behaviour:

- Client IDs can be long; tooling that stores or transmits them must accommodate identifiers
  longer than 50 characters.
- Access for affiliated customers is granted and scoped through client creation and access
  control in the portal, so an integration only reaches the properties it is entitled to.

## Security ground rules for any OPERA integration

- Never log tokens, secrets, or full authorization headers.
- Scope credentials to the minimum set of APIs and properties needed.
- Rotate secrets on a schedule and immediately if exposure is suspected.
- Treat property data (guest profiles, folios) as personal data subject to the applicable data
  protection regime; minimise what you extract and retain.
