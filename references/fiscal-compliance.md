# Fiscal and Regulatory Exports

This file describes OPERA's fiscal/regulatory export concepts at a generic, documented level. It
contains no client-specific configuration, no credentials, and no live endpoints. Fiscal rules
change by country and by year; always confirm the current legal requirement and the matching OPERA
release behaviour for the specific jurisdiction before relying on any detail here.

## What fiscal exports do

In many jurisdictions, hospitality businesses must produce standardised tax/audit files or
real-time fiscal reports from their PMS. OPERA supports this through fiscal export functionality and
country-specific fiscal partners/configurations. The export turns OPERA financial and transactional
data into the format the tax authority requires.

## SAF-T (Standard Audit File for Tax)

SAF-T is an OECD-originated standard XML format for exchanging accounting and transactional data
with tax authorities. Several European countries mandate a national SAF-T variant. Common traits:

- It is an XML file with a defined schema set by the national tax authority.
- It covers a reporting period and includes master data and transactional data as the schema
  requires.
- The national schema version matters: the file must validate against the version in force for the
  reporting period.

### Portugal SAF-T (SAF-T PT)

- Portugal mandates SAF-T PT, an XML file following the schema published by the Portuguese tax
  authority (Autoridade Tributária).
- It includes header, master data, and transactional sections per the official schema.
- Document integrity requirements (such as sequential document numbering and, where required,
  digital signing of documents) apply; the certified billing software must enforce them.
- Validate the produced file against the current official schema version before submission.

### Spain fiscal

- Spain's regime differs from a periodic SAF-T file. It has used near-real-time VAT reporting
  (Suministro Inmediato de Información, SII) for in-scope taxpayers, submitting invoice records to
  the tax authority electronically within set deadlines.
- More recent Spanish requirements continue to move toward standardised, verifiable electronic
  invoicing and reporting. Confirm the current obligation and the matching OPERA configuration for
  the property's taxpayer category and region.

### Other country variants

Other jurisdictions have their own SAF-T or fiscal-reporting variants (for example national SAF-T
files in several EU states, and country-specific electronic invoicing regimes). Each has its own
schema, cadence, and certification rules. Treat each country as its own specification.

## CSV and XML fiscal export formats

OPERA fiscal and financial exports are produced as structured files, commonly XML for SAF-T style
audit files and CSV for some ledger/fiscal feeds.

- XML exports must validate against the target schema; an element out of order or a missing
  mandatory field fails validation at the authority even if OPERA produced the file without error.
- CSV exports depend on exact column order, delimiters, encoding, and sign conventions agreed with
  the downstream system or authority.
- Character encoding matters: a stray accented character where an empty string literal was intended
  will corrupt a field. Re-enter literals directly rather than pasting from an external editor.

## Credit note handling in fiscal/financial output

Credit notes (refunds, reversals) need correct treatment:
- They must be identifiable as credit documents, often by a specific document type or indicator
  field rather than only by a negative amount.
- Sign conventions differ between OPERA's internal representation and what the target format
  expects. Some targets require a positive amount on a credit line flagged as a credit, rather than
  a negative amount.
- Document numbering for credit notes must follow the required sequence and reference the original
  document where the schema demands it.

## General rules for any fiscal export work

1. Build and validate in UAT, never directly in production.
2. Validate the output against the official schema or the downstream system's spec, not just
   against OPERA producing a file without an error.
3. Confirm the schema/version in force for the reporting period.
4. Keep document numbering and integrity controls intact; fiscal regimes treat gaps and
   out-of-sequence numbers as red flags.
5. Get documented sign-off from the responsible finance owner before applying to production.
6. Record which OPERA release and which schema version a given export configuration targets, so a
   later upgrade can be checked against schema changes.
