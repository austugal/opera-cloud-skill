# Back-Office Financial (Ledger) Exports and the Formula Engine

OPERA Cloud produces financial exports that feed back-office / general-ledger systems (for example
SunSystems and other finance platforms). This file covers the generic structure and the formula
engine constraints. It contains no client-specific mappings or credentials.

## What a back-office export is

A back-office export takes OPERA financial data (ledger postings, transactions) and writes a file
in the structure the finance system expects, usually CSV. It is configured under the
Administration interfaces / back-office export area:
`Administration > Interfaces > Back Office Exports > [Export Name]`

The export defines columns, each populated by a value or a formula, and produces a file per run
(commonly daily, aligned with night audit).

## Typical ledger export column structure

A city-ledger style export usually carries columns along these lines (exact layout is per
configuration and per finance system):

| Column | Typical content | Notes |
|--------|-----------------|-------|
| Journal type | Fixed code identifying the journal | e.g. a city-ledger marker |
| Transaction date | Posting / transaction date | |
| Transaction amount | Signed amount | Credit lines may need special sign handling |
| Indicator field | Document/indicator flag | Distinguishes normal versus credit-note lines |
| Account code | General-ledger account mapping | Maps OPERA transaction code to GL account |
| Cost centre | Property / department code | |
| Description | Transaction description | |
| Amount decimals | Decimal portion of the amount | Formula must keep the sign correct |

## Credit note handling

Credit notes post internally as negative amounts in OPERA. A finance system often requires a
positive amount on the credit line, flagged by a specific indicator field, rather than a negative
number. So:
- The amount column formula must return a positive value for credit lines.
- The decimal column formula must return the correct decimal portion, also positive.
- The indicator field must mark the line as a credit document.

## Formula engine constraints (important)

OPERA Cloud's export formula engine is deliberately limited. The following is documented and
field-observed behaviour and is the source of most export bugs.

### Supported operators

- Arithmetic: `+`  `-`  `*`  `/`  `^` (power), and unary minus (`-x`).
- String literal: `''` (two single quotes) for an empty string.
- Conditional: `IF(condition, true_value, false_value)` — verify support and exact syntax for the
  running release.
- `SIGN(x)` returns -1, 0, or 1.

### Not supported (will cause problems)

- `ABS()` is not available. Attempting to use it can cause the formula to fail to save, sometimes
  with no clear error message.
- `GREATEST()` and similar functions are likewise unavailable and can cause silent save failure.
- Do not assume a function exists because it exists in SQL or a spreadsheet; the export formula
  engine is its own restricted dialect.

### Absolute-value workaround

Because `ABS()` is unavailable, produce an absolute value with supported operators:
- `SIGN(TRX_AMOUNT) * TRX_AMOUNT` is equivalent to `ABS(TRX_AMOUNT)` when the sign is known.
- Alternatively `((TRX_AMOUNT * TRX_AMOUNT) ^ 0.5)` returns the non-negative root.

### Encoding pitfall

A common silent bug: an accent character (for example `´`) appearing where an empty string literal
`''` was intended, caused by clipboard or editor encoding when the formula was pasted in. Fix by
retyping the `''` literal directly in the OPERA formula field rather than pasting from an external
editor.

### Validation reality

- The formula engine validates on save, but a successful save does not prove correct execution.
- Always run a sample export in UAT and inspect the output file against the finance system's
  expected format before promoting.

## UAT to production workflow for exports

1. Export the configuration shell from production (read-only, no live impact).
2. Import into UAT.
3. Run a property refresh in UAT after import.
4. Rebuild or amend the export formulas in UAT.
5. Validate the output file against the finance system's expected format.
6. Obtain documented sign-off from the responsible back-office / finance owner.
7. Apply to production.

Never edit production export formulas directly without prior UAT validation and approval.
