# Rate Plans, Restrictions, and Hurdle Logic

## Rate plan structure

A rate plan (rate code) defines what a guest pays and under what conditions. Key attributes:

- Rate code: the identifier.
- Rate detail: amounts by date range, day of week, and occupancy (single, double, extra adult,
  extra child).
- Room types: which room types the rate applies to.
- Market segment and source: classification used for reporting and statistics.
- Packages: bundled elements (for example breakfast) attached to the rate, each with its own
  posting and allocation rules.
- Booking rules: advance booking, minimum/maximum stay, eligibility.
- Derivation: whether the rate stands alone or derives from another rate.

## Derived rates

A derived rate calculates its amount from a base rate rather than holding its own amounts.

- The derivation can be a fixed amount, a percentage, or another supported adjustment applied to
  the base rate.
- Change the base rate and the derived rate follows automatically. This is powerful and dangerous:
  a base-rate change silently moves every derived rate.
- When auditing "why did this rate change", check whether it is derived before looking for a direct
  edit.

## Restrictions

Restrictions control whether and how rooms can be sold for given dates. Common restriction types:

- Open / Close: whether the rate or room type is sellable at all for the date.
- Closed to Arrival (CTA): cannot start a stay on that date.
- Closed to Departure (CTD): cannot end a stay on that date.
- Minimum Length of Stay (MinLOS): a stay touching the date must be at least N nights.
- Maximum Length of Stay (MaxLOS): a stay touching the date must be at most N nights.
- Minimum/Maximum advance booking: how far ahead a booking can or must be made.

Restrictions can be set at different levels (for example by rate plan, by room type, or house-wide)
and the effective restriction is the combination. A booking is refused if any applicable
restriction blocks it.

## Distribution and channels

Rates and restrictions set in OPERA are pushed to connected channels through the Distribution
APIs / ARI flow (see `distribution-channels.md`). A restriction set in OPERA should propagate to
the channel; a mismatch usually points to a mapping or sync problem rather than a pricing error.

## Hurdle rates (revenue management integration)

A hurdle rate is a revenue-management concept: the minimum rate the system will accept for a date,
below which a booking is not offered, used to protect inventory for higher-value demand. When OPERA
is integrated with a revenue management system:

- The revenue system computes hurdle/optimal pricing and feeds decisions to OPERA / distribution.
- OPERA and the distribution layer apply the hurdle so that availability and rate offered respect
  it.
- Exact behaviour depends on the revenue management integration and the OPERA/distribution release;
  confirm against the documentation for the versions in use.

## Practical rate troubleshooting

1. Is the rate derived? If so, inspect the base rate.
2. Are restrictions blocking the date (open/close, CTA/CTD, MinLOS/MaxLOS)?
3. Does the rate apply to the room type and occupancy being booked?
4. For a channel discrepancy, is the rate/room mapping correct and has the latest change synced?
5. For a revenue-managed property, is a hurdle suppressing the rate?
