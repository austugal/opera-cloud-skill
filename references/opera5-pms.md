# OPERA 5 PMS Configuration Reference

OPERA 5 (OPERA V5) is Oracle's previous-generation, on-premise or hosted PMS, spanning releases
5.0.04 through 5.6. It shares concepts with OPERA Cloud but uses different menus and configuration
paths. Source of truth: the Oracle Help Center OPERA 5 core help libraries for the matching release.
Confirm any specific path or value against the documentation for the exact version in use.

## Configuration module map (high level)

OPERA 5 configuration lives under `Configuration` with major branches including Setup, Cashiering,
Reservations, Rate Management, Profiles, Rooms Management, Blocks, and others. Operational tools live
under `Miscellaneous`.

## Workstations and print tasks (v5.6)

Source: Oracle Help Center, OPERA 5.6 core help — Workstations.

Setup path: `Configuration > Setup > Workstations`

Key concepts:
- Each workstation is identified by `domain\computer name` (for example `HOTEL\FD01`).
- OPERA auto-creates a workstation record on first login from a new machine.
- Print tasks are workstation-specific, not user-specific.
- Print tasks display for the logged-in property or are global.

Adding a workstation:
1. `Configuration > Setup > Workstations > New`.
2. Station: enter `domain\computer name` (for example `HOTEL\RSV01`).
3. Description: physical location (for example `Front Desk Left`).
4. Swipe Reader Attached: check if a card reader is present; select Reader No. and Software ID.
5. Chip and PIN Device Available: check if the `IFC > CHIP AND PIN` parameter is Y.
6. Fiscal Printer Attached: check if `CASHIERING > FISCAL FOLIO PRINTING` is Y.
7. Attach Print Tasks From: copy print tasks from an existing workstation.

Adding print tasks to a workstation:
1. Highlight the workstation, click Print Tasks.
2. Click New.
3. Select the Printer (must be pre-configured in `Configuration > Setup > Printers`).
4. Select the Print Task from the list of values (multi-select supported per printer).
5. Caution: duplicate print tasks to different printers cause double-printing.

### Print task reference

| Task | Trigger | License / function required |
|------|---------|-----------------------------|
| AR FOLIO | Printing from within an AR account | `CASHIERING > ACCOUNTS RECEIVABLES = Y` (or AR Settlement Print Task param = Y) |
| AR RECEIPT | Printing an AR receipt | `CASHIERING > ACCOUNTS RECEIVABLES = Y` |
| CASHIER CLOSE REP | Closing cashier / cashier reports | — |
| CHECK PRINTER | Printing commission checks | Commission license |
| CHECK XANGE | Check exchange receipt | `CASHIERING > CHECK EXCHANGE = Y` |
| CONF LETTER | Confirmation letter print | — |
| CONF LETTER EMAIL | Confirmation letter email | — |
| CONF LETTER FAX | Confirmation letter fax | — |
| DATA CARD | Data/key card embossing | Specialised datacard machine |
| DEFAULT EMAIL | Default property email | — |
| DEFAULT FAX | Default property fax | — |
| DEP RECEIPTS | Deposit receipt | `CASHIERING > DEPOSIT HANDLING = Y` |
| FOREIGN CURR XANGE | Currency exchange receipt | `CASHIERING > FOREIGN CURRENCY HANDLING = Y` |
| GST FOLIO | Guest folios | Primary folio print task |
| GST MSG | Guest messages | `FRONT DESK > MESSAGES = Y` |
| HOUSEKEEPING | Housekeeping module printing | — |
| INFO FOLIO | Information folio | — |
| LABELS | Labels | — |
| LIST REP | Reports and lists | — |
| MAILINGS | Mailing reports | — |
| NA REPORTS | Night audit reports | — |
| OCIS ECERTIFICATE | E-certificate vouchers | `PROFILES > OCIS E-CERTIFICATES = Y` |
| OWNER STATEMENT | Owner statements | Vacation ownership license |
| PAIDOUTS | Paid-out receipts | — |
| PASSER FOLIO | Passerby folios | `CASHIERING > PASSERBY = Y` |
| POST IT | Post-It folio/receipt | — |
| REG CARD | Registration cards | — |
| REMINDER | AR reminder letters | `CASHIERING > ACCOUNTS RECEIVABLES = Y` |
| REMINDER FAX | AR reminder faxes | `CASHIERING > ACCOUNTS RECEIVABLES = Y` |
| STATEMENT | AR statements | `CASHIERING > ACCOUNTS RECEIVABLES = Y` |
| STATEMENT FAX | AR statement faxes | `CASHIERING > ACCOUNTS RECEIVABLES = Y` |
| VOUCHERS | Voucher printing | `RESERVATIONS > VOUCHERS = Y` |

Troubleshooting missing print tasks:
1. Confirm the required application function/parameter is active (see table).
2. Confirm the printer is configured at `Configuration > Setup > Printers` and tied to the correct
   property.
3. Confirm the print task is not inherited from another workstation (inherited tasks cannot be
   edited at the current workstation).
4. Confirm the workstation is registered for the correct property in a multi-property environment.
5. Check for duplicate print tasks to different printers, which cause double-printing.

## Printers setup (v5.6)

Source: Oracle Help Center, OPERA 5.6 core help — Printers Setup.

Setup path: `Configuration > Setup > Printers`

| Field | Notes |
|-------|-------|
| Physical Device | Network UNC path (for example `\\SERVER\PRINTER`) or an IP-based UNC path |
| Device Mode | Character (dot matrix, uses .PRT control files) or Bitmap (PDF, thin client) |
| Device Driver | Required for Character mode; references a PRT control file |
| Use Print Client | Print to a local/network PC printer rather than directly from the app server |
| Use Alert Printing | Makes the printer available for alert printing (`RESERVATIONS > ALERTS = Y`) |
| Folio Printer | Enables folio print tasks; required where credit bill / fiscal folio printing applies |
| Queue Name | Latin American properties only; ties the printer to a fiscal folio numbering sequence |
| Starting Folio No. | Latin American only; starting number for pre-printed fiscal forms |

## OPI — Oracle Payment Interface for OPERA V5

Source: Oracle Help Center / Oracle Payment Interface documentation (OPI release 20.4 line).

Minimum OPERA versions for OPI integration (verify against current OPI docs):
- OPERA V5 Hosted: 5.5.0.25.8 or higher.
- OPERA V5.6.6 or higher.
- OPERA V5 On Premise: 5.5.0.24.4 or higher.

OPI server requirements:
- 64-bit OS only.
- Minimum free disk space per the installer (around 6 GB).
- Static IP address.
- Machine name without special characters.
- Supported Windows desktop/server OS per the OPI release notes.
- Database: MySQL or Oracle DB (OPI does not install the database itself).

Clean install (outline):
1. Run the installer as Administrator.
2. Select language.
3. Choose Complete or Custom (Custom needs all three features: Database Schema, OPI Services,
   Configuration Tool).
4. Select database type and enter connection details.
5. Provide database admin credentials; OPI creates its own DB user.
6. Create the super-user system admin account.
7. Set host/port for the OPI configuration service.
8. Set and confirm the passphrase.
9. Select the OPI instance mode (OPERA/Suite8).
10. Configure token exchange settings.

Merchant configuration in OPI:
- Chain Code and Property Code must be upper case.
- Generate Key produces the IFC8 communication key (documented prefix `FidCrypt0S|`).
- Enter the IFC8 IP address and port.
- Enter merchant name, city, state, country, currency.
- Map OPERA payment codes to card types.

OPERA-side configuration for OPI:
- `Configuration > Setup > Property Interfaces > Interface Configuration`.
- Create the EFT interface under `Configuration > Cashiering > Credit Card Interface`.
- Configure Chip and PIN (EMV) where applicable, the CC Vault, and the payment widget where used.
- Link the workstation to the OPI IFC8 instance.
- IFC8 authentication uses the key generated by OPI.

Token exchange settings: the Authentication User and Authentication Password set in the OPI
configuration tool must match the values configured on the OPERA EFT interface custom data tab.

Certificates: two sets are required, one for OPI to the payment service provider, and one for
OPERA to OPI.

## Business Events (v5.04 / v5.6)

Source: Oracle Help Center, OPERA 5 core help — Business Events Configuration.

Configuration path: `Configuration > Setup > Business Events > Configuration`

Available modules include: Activity, Availability, Block, Block Offsets, Cashiering, Catering
Notes, Configuration, Enrollment, Housekeeping, Night Audit, Owner Contract, Profile, Rate,
Reservation, Stay Records.

Key rules:
- Select the External System and Module before configuring events.
- The Business Event queue purges messages older than 96 hours, triggered by Night Audit.
- Processing XML messages from the queue is a vendor responsibility and must not run on OPERA
  servers.
- Where Conditions define when an event fires; multiple conditions combine with AND/OR logic.
- Clear Cache clears the business-event cache for the current session without an OPERA restart.

## Cashiering configuration (key topics)

`Configuration > Cashiering`
- Transaction Codes: revenue codes, tax generates, packages, payment methods.
- Tax Types: user-definable, attached to transaction codes.
- Folio Print Tasks: conditions that determine which folio type prints, each linked to a folio type
  and report file.
- Fiscal XML configuration and payment XML layout where fiscal folio printing applies.
- Foreign Currency: exchange rates per currency per date range.
- Cashier Numbers: per user; control access to cashier functions.
- Credit Limits: per payment method.
- Pre- and post-charging rules: charges before check-in and after check-out.

## Reservations configuration (key topics)

`Configuration > Reservations`
- Deposit Rules: advance deposit prepayment rules, attached to rate codes or reservation types.
- Cancellation Rules: penalty rules attached to rate codes, reservation types, or individual
  reservations.
- Reservation Types: booking source types (for example guaranteed, non-guaranteed).
- Traces: auto-attached based on preferences or reservation criteria.

## Miscellaneous operations

`Miscellaneous` covers operational tools including Reports / Report Scheduler, Interfaces, Data
Extraction, Track It, Awards, Enrollment, Merge Profiles, Print Tasks, User Activity Log,
Memberships, File Export, File Download, Police (guest registration data export for some countries),
Indicator Lamps, Log Book, OHDR Diagnosis and Repair Tool, and Vouchers.
