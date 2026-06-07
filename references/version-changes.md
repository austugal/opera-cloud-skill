# Version Changes and Release Tracking

## Release cadence

OPERA Cloud ships on a roughly quarterly cadence with version numbers in the form `YY.x`
(for example 24.1, 24.2, 25.1, 25.2 and onward). Each release can add API modules, add or change
fields, introduce or rename OPERA Controls, and occasionally deprecate behaviour.

OPERA 5 is the previous-generation product, spanning 5.0.04 through 5.6 across its life, with its
own patch levels.

Because releases move quarterly, do not hard-state "the current version is X" from memory. Confirm
the running release for the property in question and read that release's change log.

## How to track changes reliably

1. Identify the exact OPERA Cloud release the property/tenant is on.
2. Read Oracle's release notes / what's new for that release in the Oracle Help Center.
3. For API behaviour, compare against the OHIP Developer Portal API documentation and the published
   hospitality API specifications for that version.
4. Check OPERA Controls documentation for the release; new features frequently arrive gated behind a
   new control.

## Durable categories of change to watch

These categories recur release to release; check each when upgrading.

- New API modules or endpoints: added capability, usually additive and safe to ignore until needed.
- New or changed fields on existing payloads: can break strict consumers that reject unknown
  fields; build consumers tolerant of additive fields.
- Enum / code value additions: a new status or type value can appear; handle unknown values
  gracefully rather than failing.
- OPERA Control additions/renames: a new feature may require enabling a control; behaviour you
  expect may not appear until it is set.
- Distribution changes: channel/ARI behaviour evolves; re-verify mappings after a distribution
  release.
- Deprecations: occasionally an old behaviour is retired with notice; track the deprecation list and
  migrate before the removal release.
- Report and merge-code changes: report catalogues and folio merge codes can change.

## Upgrade checklist for an integration

1. Read the target release notes end to end.
2. List every API your integration calls and check each against the target version's spec.
3. Re-test in UAT on the target release before it reaches production.
4. Confirm any OPERA Controls your integration relies on still exist and are set correctly.
5. Re-validate fiscal and back-office export output against the target release (schemas and formula
   behaviour can shift).
6. Re-verify distribution mappings if a distribution change is in the release.

## Stating version context in answers

When behaviour differs by release, name the release the answer applies to and flag that the user
should confirm against their running version. Do not present release-specific behaviour as
universal.
