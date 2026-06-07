# Night Audit and OPERA Controls

## Night audit / end of day

Night audit (end of day) is the daily process that closes the business day, rolls the business date
forward, and produces day-end financials and reports. It is the heartbeat of a property's
accounting.

### What night audit does (typical sequence)

- Validates that the day's activity is ready to close (for example arrivals processed, departures
  settled or handled per policy).
- Posts room and tax charges for in-house guests for the night.
- Processes no-shows and applicable charges per policy.
- Rolls the business date forward by one day.
- Runs end-of-day reports and triggers configured exports (including back-office and fiscal
  exports).
- Settles and rolls cashier/shift balances as configured.

### Business date versus system date

- The business date is the operational date the property is working in; it only moves when night
  audit runs.
- The system date/time is the real clock.
- These can differ: a property running late night audit can still be on the previous business date
  after midnight. Reservation, posting, and reporting logic keys off the business date, not the
  wall clock. This distinction matters for any integration that timestamps or reconciles activity.

### Integration implications

- Many exports and Business Events fire around night audit. A consumer that expects a daily file or
  a burst of events should align to the property's audit timing.
- Do not assume midnight equals the business-date rollover; confirm the property's audit schedule.

## OPERA Controls (configuration flags)

OPERA Controls are the configuration flags and settings that switch features and behaviours on or
off and tune them, grouped by functional area. They are the master switches behind much of OPERA
Cloud's behaviour.

### Concepts

- Controls are organised by functional group (for example reservations, cashiering, blocks, rate
  management, front desk, profiles, and so on).
- A control can be a simple on/off parameter, a setting with a value, or a function that unlocks a
  whole capability.
- Changing a control can change system behaviour broadly; treat control changes like any other
  production change (build/verify in UAT, approve, then apply).
- Controls are set per property (and some at chain/enterprise level), so two properties on the same
  tenant can behave differently.

### Working with controls

- Before troubleshooting "OPERA will not let me do X", check whether a control governs X and how it
  is set for that property.
- When documentation says a feature requires a control, that control must be active for the API or
  UI behaviour to appear.
- Record control changes: they are easy to make and easy to forget, and they explain behaviour that
  otherwise looks like a bug.

### Verifying a control's exact name and effect

OPERA Control names and their grouping change across releases and are extensive. Do not guess a
control's exact name. Confirm it against the OPERA Controls documentation for the running release,
and state when an answer depends on a control being set a particular way.
