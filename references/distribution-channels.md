# Distribution APIs and Channel Manager Migration

## Distribution APIs

OPERA Cloud Distribution APIs connect the PMS to distribution partners and channel managers:
online travel agencies, central reservation systems, and channel management platforms. They carry
two main flows.

### ARI push (Availability, Rates, Inventory)

OPERA pushes availability, rate, and restriction changes outward so connected channels stay in
sync with the property. When availability, a rate amount, or a restriction changes in OPERA, the
change is distributed to the channel.

- Availability: how many rooms are sellable by room type and date.
- Rates: rate amounts by rate plan and date.
- Restrictions: open/close, minimum/maximum length of stay, closed to arrival/departure, and
  similar controls (see `rates-restrictions.md`).

### Reservation notification (ResNotif)

Inbound reservations from a channel are delivered to OPERA as reservation notifications: a booking
made on a channel becomes a reservation in OPERA. The flow handles create, modify, and cancel for
channel-originated bookings.

### Design notes

- Distribution flows are near real time; design consumers and mappings for change volume at peak.
- Code mapping between OPERA and the channel is the heart of a reliable integration: room types,
  rate plans, and restrictions must map cleanly in both directions.
- Confirm the exact module set and behaviour against the Distribution API documentation for the
  running release; distribution capability changes across versions.

## Channel manager migration

This section is generic, documented and field-observed migration behaviour. It applies when a
property moves channel connectivity onto OPERA Cloud or swaps channel manager while staying on
OPERA. Translate to the specific platforms in scope.

### Core principle

During an OPERA channel manager migration, external codes generally do not change. What is
reconfigured is the OPERA Exchange (OXI) translation layer that maps external codes to OPERA codes.

### What changes versus what stays the same

| Component | Changes? | Notes |
|-----------|----------|-------|
| Channel external rate codes | No | Typically remain identical pre and post migration |
| OPERA Exchange (OXI) translation | Yes | New mappings configured for OPERA Cloud |
| Channel extranet rate plan links | Verify | Exception — requires a separate check |
| OPERA internal rate codes | Depends | May change if a rate restructure is in scope |

### Extranet rate plan links

Channel extranet rate plan links are not always covered by the external-code preservation rule.
Treat them as an exception:
1. Document them pre-migration (export or screenshot all active extranet links).
2. Verify post-migration that they still resolve correctly.
3. Reconfigure if the underlying OPERA rate code changed.

### Reporting structural break

A migration creates a permanent reporting break at the migration date.
- Pre-migration reservations carry the legacy code set.
- Post-migration reservations carry the new code set.
- Year-over-year and period comparison reports will be structurally inconsistent across the date.

Mitigation: produce a legacy-to-new mapping table before go-live covering rate codes, market
segments, source codes, and room types where applicable.

### Guest profile and reservation impact

- Profiles holding legacy codes keep those codes on the record.
- Reservations created pre-migration with legacy codes keep them until check-out.
- After check-out, legacy codes remain in history; there is no retroactive conversion.

Account for this in any segmentation or revenue analysis spanning the migration date.

### Interface sequencing at go-live

Production cutover is not lighter than the UAT build. Plan for:
1. OXI reconfigured before the channel goes live.
2. The legacy channel retired cleanly, not merely disabled.
3. Parameter updates in both OPERA and the channel manager admin.
4. A cutover window that accounts for in-flight reservations.
