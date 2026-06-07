# OPERA Cloud REST API Modules and Key Operations

OPERA Cloud REST APIs are organised into functional modules exposed through OHIP. This file maps
the module surface and the most common operations by domain. Endpoint paths, payload schemas, and
field names are defined per release in the OHIP Developer Portal and Oracle's published hospitality
API specifications; treat the spec for the running version as authoritative and do not invent paths.

## Module map

| Module | Domain |
|--------|--------|
| RSV | Reservations |
| CRM | Profiles / customer relationship (guests, companies, travel agents, sources, contacts) |
| CSH | Cashiering / folios / postings |
| FOF | Front office and room status operations |
| HSK | Housekeeping |
| RTP | Rate plans and rate management |
| INV | Inventory and availability |
| BLK | Block (group) management |
| EVM | Event management, sales and catering |
| FOFEXP / exports | Operational exports |
| ENTCFG | Enterprise configuration |
| Property configuration | Property-level configuration |
| INT / INTCFG | Interfaces and interface configuration |
| Distribution (ARI, ResNotif) | Channel availability, rates, restrictions, reservation notification |
| Reporting / analytics | Report and data retrieval |

Modules also exist in synchronous and asynchronous flavours for operations that can be long
running (bulk rate loads, large extracts). See `async-streaming-events.md`.

## Reservations (RSV)

Core capabilities:
- Search reservations by criteria such as arrival date, departure date, confirmation number,
  profile, room, or status.
- Retrieve a single reservation by its identifier.
- Create, modify, and cancel reservations.
- Manage attached items: packages, fixed charges, traces, alerts, accompanying guests, routing.
- Check-in and check-out actions where exposed through the API.

Typical patterns:
- Search returns summary records; fetch the full reservation by ID for detail.
- Reservation state is driven by status (for example reserved, in house, checked out, cancelled,
  no show); always read status before acting.
- Routing instructions move charges between folio windows or to another reservation/account.

## Profiles / CRM

Capabilities:
- Profile types: guest, company, travel agent, source, contact, group.
- Create and update profiles; search by name, membership, contact details.
- Manage memberships, preferences, addresses, communication channels, and negotiated rates
  attached to company/agent profiles.
- Profile lookup and de-duplication considerations: matching reduces duplicate guest records.

Data protection: profiles are personal data. Extract and retain only what an integration needs.

## Cashiering (CSH)

Capabilities:
- Post charges and payments to a folio window.
- Retrieve folio and transaction detail.
- Adjust, transfer, and void postings subject to cashiering privileges and OPERA Controls.
- Settlement and folio generation.

Concepts:
- A reservation folio has multiple windows; routing and transfers move charges between them.
- Transaction codes classify each posting (room, food and beverage, tax, payment, and so on) and
  drive reporting and back-office mapping.
- Credit and debit behaviour and sign conventions matter for downstream exports (see
  `backoffice-export.md`).

## Rooms, room status, housekeeping (FOF / HSK)

Capabilities:
- Read and update room status (for example clean, dirty, inspected, out of order, out of service).
- Manage housekeeping task assignment and room conditions.
- Front office room operations such as room moves and assignment.

Concepts:
- Out of Order versus Out of Service differ: Out of Order removes a room from sellable inventory;
  Out of Service typically does not reduce availability in the same way. Confirm exact inventory
  impact against the release documentation.

## Rate management (RTP)

Capabilities:
- Read rate plans, rate codes, rate details, and rate amounts by date.
- Create and update rate plans and rate detail where exposed.
- Manage rate plan attributes: market segment, room types, packages, derivation.

Concepts:
- Derived rates calculate from a base rate by a formula or adjustment rather than holding their own
  amounts. Changing the base flows to the derived rate.
- Bulk rate operations are good candidates for the asynchronous APIs.
- See `rates-restrictions.md` for rate plan structure and restrictions.

## Inventory and availability (INV)

Capabilities:
- Read availability and inventory counts by date and room type.
- Manage sell limits and inventory controls where exposed.

## Block management (BLK)

Capabilities:
- Create and manage blocks (group allotments): room block grid, pickup, cut-off, rooming list.
- Read block availability and pickup against allocation.

Concepts:
- Block status governs whether inventory is deducted from general availability.
- Cut-off date releases unsold block rooms back to general inventory.

## Front office finance (FOF)

Capabilities:
- Operational finance actions tied to the front desk: balances, deposits, and related postings,
  depending on what the release exposes.

## Event management / sales and catering (EVM)

Capabilities:
- Manage events, function space bookings, and catering where the property is licensed for sales
  and catering.

## Enterprise and property configuration (ENTCFG / property config)

Capabilities:
- Read and manage configuration entities: room types, rate codes, market segments, source codes,
  transaction codes, packages, and similar reference data.
- Configuration APIs underpin any integration that must keep an external system's code set aligned
  with OPERA.

## Interfaces (INT / INTCFG)

Capabilities:
- Configure and query interface definitions used by OPERA to talk to external systems (payment,
  point of sale, door locks, and others).
- See `interfaces-ifc8.md` for IFC8 and OXI concepts.

## General API conventions

- Pagination: list operations page results; follow the documented paging parameters rather than
  assuming a fixed page size.
- Idempotency: creating resources should account for retries; check whether the operation supports
  an idempotency mechanism before blindly retrying a create.
- Versioning: APIs are versioned; pin to a known version and review the change log before upgrading
  (see `version-changes.md`).
- Errors: read the Oracle error response body, not just the HTTP status (see `error-handling.md`).
