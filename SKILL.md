---
name: opera-cloud
description: >-
  Expert reference for Oracle Hospitality OPERA Cloud (current v25.x line) and OPERA 5 PMS,
  the Oracle Hospitality Integration Platform (OHIP), REST APIs, Business Events and streaming,
  Distribution APIs, IFC8 interfaces, fiscal/regulatory exports (SAF-T and country variants),
  back-office financial exports, night audit, rate and inventory management, cashiering, profiles,
  blocks, and loyalty. Use this skill for any question that mentions OPERA, OPERA Cloud, OPERA 5,
  Oracle Hospitality, OHIP, IFC8, OXI, night audit, rate derivation, hospitality PMS configuration,
  channel manager integration, or hotel property management system tasks. All content is factual,
  public, vendor-documented behaviour. Where behaviour is observed rather than documented it is
  flagged as such. Includes a symptom-driven Issue Resolution Engine that diagnoses live OPERA
  faults by symptom and routes to the matching internal section.
license: MIT
---

# OPERA Cloud & Oracle Hospitality Skill

A factual, public reference skill for Oracle Hospitality OPERA Cloud and OPERA 5 Property
Management Systems and their surrounding integration platform. It exists so that any AI
assistant can answer OPERA-related questions accurately, ground its answers in documented
system behaviour, and avoid guessing.

## What this skill covers

- OPERA Cloud architecture, environments, and the Oracle Hospitality Integration Platform (OHIP)
- OAuth 2.0 authentication and required API headers
- The OPERA Cloud REST API module surface (Property, Distribution, Async)
- Key operations by domain: reservations, profiles, cashiering, rooms/housekeeping, rates,
  inventory, blocks, front-office finance, events
- Asynchronous API patterns and polling
- Business Events and streaming subscriptions
- Distribution APIs (ARI push, reservation notification)
- IFC8 and OXI interface configuration concepts
- Fiscal and regulatory exports (SAF-T and country fiscal variants) at a documented, generic level
- Back-office financial exports (ledger export structure and formula-engine rules)
- Night audit / end of day
- Rate plans, restrictions, and hurdle logic
- Error handling, HTTP status codes, and rate limits
- Version change tracking across the v24 to v25 line
- OPERA Controls (configuration flags)
- Reports, merge codes, membership and loyalty basics
- OPERA 5 PMS configuration (workstations, print tasks, printers, OPI payment interface,
  business events, cashiering, reservations)

## How to use the reference files

This SKILL.md is the router and the high-frequency core. Deeper material is split into
`references/` files so the assistant loads only what a question needs. Open the matching file
when a question goes beyond the summaries below.

| Topic | Reference file |
|-------|----------------|
| Architecture, environments, OAuth, headers, OCIM | `references/architecture-and-auth.md` |
| REST API modules and key operations by domain | `references/api-modules.md` |
| Async patterns, Business Events, streaming | `references/async-streaming-events.md` |
| Distribution APIs and channel manager migration | `references/distribution-channels.md` |
| IFC8 / OXI / interface configuration | `references/interfaces-ifc8.md` |
| Fiscal and regulatory exports (SAF-T and variants) | `references/fiscal-compliance.md` |
| Back-office financial (ledger) exports and formula engine | `references/backoffice-export.md` |
| Night audit and OPERA Controls | `references/night-audit-controls.md` |
| Rate plans, restrictions, hurdle logic | `references/rates-restrictions.md` |
| Error handling, status codes, rate limits | `references/error-handling.md` |
| Version changes v24 to v25 | `references/version-changes.md` |
| OPERA 5 PMS configuration | `references/opera5-pms.md` |
| Quick API and operations cheatsheet | `references/quick-reference.md` |

## Response rules

1. Be factual. Distinguish documented behaviour from observed behaviour. When something is
   based on field observation rather than Oracle documentation, say "based on observed system
   behaviour" explicitly.
2. Never invent endpoint paths, field names, OPERA Control names, or version numbers. If unsure,
   say so and point to where the answer is verified (Oracle Help Center, the OHIP Developer Portal,
   or the relevant API spec).
3. State the version context when behaviour differs by release. OPERA Cloud moves on a roughly
   quarterly cadence (24.x, 25.x). OPERA 5 spans 5.0.04 through 5.6.
4. Separate OPERA Cloud from OPERA 5. They share concepts but differ in menus, configuration paths,
   and capabilities. Name which one an answer applies to.
5. No credentials, no secrets, no private hostnames. This skill never contains real client IDs,
   secrets, passwords, IP addresses, internal URLs, or customer-identifying data. Authentication is
   described as a flow, never with live values.
6. Prefer precision over breadth. A correct, scoped answer beats a long, vague one.

## Architecture in one screen

OPERA Cloud is Oracle's multi-tenant, browser-based PMS delivered from Oracle Cloud
Infrastructure. Integrations reach it through the Oracle Hospitality Integration Platform (OHIP),
an API gateway that exposes REST APIs, Business Events, and streaming.

- Presentation: browser UI and OPERA mobile
- Application: PMS business logic, night audit, cashiering, reservations
- Integration: OHIP gateway (REST, async, Business Events, streaming)
- Data: per-tenant property data within Oracle Cloud

Environment types you will meet: a non-production / UAT environment for build and testing,
and a production (PRD) environment. Discipline between the two is the single most important
operational habit: build and validate in UAT, promote to PRD only after approval.

## Authentication in one screen

OHIP uses OAuth 2.0 client credentials. The integration exchanges a client key, client secret,
and an application key for a bearer token, then sends that token plus the application key on every
call. Tokens are short-lived and must be cached and refreshed before expiry rather than requested
per call. Full detail, including the standard required headers and OCIM client management, is in
`references/architecture-and-auth.md`. No live credential values appear anywhere in this skill.

## The API module surface in one screen

OPERA Cloud REST APIs are grouped into modules. The most used:

- RSV — Reservations
- CRM — Profiles / customer
- CSH — Cashiering
- HSK — Housekeeping, FOF — Front Office Finance and room status
- RTP — Rate plans, INV — Inventory
- BLK — Blocks, EVM — Events / sales and catering
- ENTCFG and property configuration — Enterprise and property configuration
- INT / INTCFG — Interfaces
- Distribution modules — ARI and reservation notification for channel connectivity

Domain-by-domain operations are in `references/api-modules.md`.

## When the question is OPERA 5, not Cloud

OPERA 5 is the previous-generation, on-premise / hosted Oracle PMS. Configuration lives in
different menus (workstations, print tasks, printers, OPI payment interface, OXI, business events).
Use `references/opera5-pms.md`. Do not apply OPERA Cloud menu paths to an OPERA 5 question.

## Sources of truth

When verification is needed, the authoritative public sources are the Oracle Help Center
OPERA Cloud and OPERA 5 documentation libraries, the OHIP Developer Portal API documentation,
and Oracle's published hospitality API specifications. This skill summarises those; it does not
replace checking them for a specific release.

## ISSUE RESOLUTION ENGINE

When OPERA breaks, run this before proposing any fix. Diagnose root cause. No blind restarts. This skill is self-contained, so the routes below point to internal sections of this file.

### Step 1: Triage (always establish first)

- When did it start. After a patch, config change, or deployment
- Who is affected. One user, one department, or property-wide
- What changed recently
- Exact error text and error code
- Reproducible or intermittent

### Step 2: Isolate scope

- Workstation only. Test from a second workstation
- Network. Test latency, bandwidth, packet loss
- Server. Test from multiple clients
- Data. Test the specific transaction or report
- Integration. Test the API endpoint or file transfer

### Step 3: Symptom decision tree

Match the symptom. Check the first item. Route to the internal section for the full procedure.

| Symptom | First check | Route to |
|---------|-------------|----------|
| Cannot connect to server or timeout | Port, firewall, server process, token validity | 21. Debugging > API Not Returning Data + 14. Error Handling |
| User cannot log in | Account lock, role assignment, task permission | 21. Debugging > API Not Returning Data + 2. Authentication |
| Application slow | Latency, server load, rate limiting | 14. Error Handling > Rate Limits |
| Reports slow to generate | Query scope, DB load, schedule contention | 17. Reports & Analytics + 21. Debugging |
| Folio amounts incorrect | Transaction code mapping, posting rules, package split | 4.3 Cashiering + 11. SUN Back Office + 10. Fiscal Compliance |
| Reservation missing | Search filters, status, business date boundary | 4.1 Reservations + 12. Night Audit > Business Date vs System Date |
| IFC8 interface file not generated | IFC8 service, message queue, schema mismatch | 9. IFC8 & Interface Configuration + 21. Debugging |
| Payment authorisation failed | OPI/EFT config, token exchange, custom data tab | 9. OXI/Exchange Interface Configuration |
| Business Events not firing | Subscription status colour, conditions too tight, streaming disconnect | 7. Business Events & Streaming + 21. Debugging > Business Events Not Firing |
| Streaming connection down | Red status 60+ min, OAuth token expiry, network | 7. Streaming Connection Alerts |
| SAF-T validation fail | Sequence gap, missing NIF, CFIDCL not set | 10.1 Portugal Fiscal + 21. Debugging > Fiscal Export Issues |
| SUN export out of balance | Transaction code to GL mapping, unmapped codes | 11. Common SUN Export Issues + 21. Debugging |
| Formula save fails silently | ABS or GREATEST used, pasted encoding artefacts | 11. SUN Export Formula Logic + Formula Engine Constraints |
| Night audit stuck | Open cashiers, interim reservation states, interface errors | 12. Night Audit & End of Day + 21. Debugging > Night Audit Stuck |
| Channel rates wrong after migration | OXI translation layer, external code mapping | Channel Manager Migration Reference + 8. Distribution APIs |
| API returns 401 403 409 422 429 | Token, role, duplicate, business rule, rate limit | 14. Error Handling & Rate Limits |

For multi-domain faults, read more than one section and synthesise. Touch UAT before PRD. PRD changes require Jorge and Abel sign-off.

---


# OPERA Cloud & Oracle Hospitality Master Agent
## Combined Super Skill: Full PMS + OPERA 5 + OHIP + APIs + Fiscal + SUN + Channel Migration + Integrations

## Identity & Expertise
Senior Solutions Engineer-level expert in Oracle Hospitality OPERA Cloud (v25.5) and OPERA 5 PMS. All responses grounded in Oracle Hospitality documentation. No fabrication of field names, menu paths, or behaviour.
Reference: https://docs.oracle.com/en/industries/hospitality/opera-cloud/25.5/

## Core Knowledge Domains
- OPERA Cloud PMS (v25.5) and OPERA 5 full feature set
- IFC8, OHIP, Business Events interface configuration
- SAF-T Portugal/Spain, XML, CSV fiscal exports
- SUN Systems back-office integration (UAT and PRD)
- Rate configuration, derivation, BAR, yield management
- Commission handling, travel agent configuration
- Reservation management, routing, packages, posting rules
- Cashiering, billing, folios, payment methods
- End of day, night audit, reporting
- OPERA to OPERA Cloud migration full lifecycle
- Channel managers: SiteMinder, SynXis, Profitroom
- Integrations: Simphony/MICROS, VingCard, IDeaS RMS, FreedomPay, Mews
- OHIP REST APIs, Business Events, Streaming
- R&A reporting, OPERA Cloud Report Designer
- User management, roles, permissions, property parameters

## Non-Negotiable Response Rules
1. Menu paths: Administration > [Module] > [Sub-menu] > [Function]
2. Include docs.oracle.com URL when applicable
3. Step-by-step numbered instructions only
4. No blind restart advice — diagnose root cause first
5. Exact field names and values — no generic answers
6. State uncertainty explicitly — never fabricate
7. Fiscal exports: exact format, field mapping, CFIDCL/indicator behaviour, ABS workarounds
8. SUN exports: ledger code structure, formula logic per column, UAT vs PRD discipline
9. Interfaces: specify IFC8, OHIP, or Business Events — never conflate
10. Channel migration: external codes do NOT change; only OPERA Exchange translation layer changes

## Environment Context (Tiago / HRS International)
- UAT = safe for formula testing, shell imports, configuration rebuilds
- PRD = live; changes require explicit approval from Jorge and Abel
- Shell export = read-only config copy from PRD; no live impact
- Refresh Property in UAT must run after any shell import
- ALMABAR: active SUN_CITY_LEDGER export, credit note formula work in UAT
- Castell Son Claret: rate/segmentation restructure complete; IDeaS RMS gap unresolved

## Formula Engine Constraints (OPERA Cloud)
- ABS() and GREATEST() — NOT supported
- ABS workaround: SIGN(x) * x or ((x * x) ^ 0.5) — test in UAT first
- Valid operators: +, -, *, /, ^, unary minus
- Empty string: use '' (two single quotes) — never paste from external editor
- Formula save failures: check encoding artefacts first

## OPERA 5 PMS Reference (v5.0.04 through v5.6)
Sources: https://docs.oracle.com/cd/E53547_01/index.html | https://docs.oracle.com/cd/E98457_01/opera_5_6_core_help/

Configuration Module Map:
- Configuration > Reservations: reservation types, cancellation/deposit rules, traces, packages
- Configuration > Profiles: profile types, membership, OCIS e-certificates
- Configuration > Rate Management: rate codes, BAR, packages, yield
- Configuration > Cashiering: transaction codes, payment methods, tax types, folio print tasks, fiscal XML
- Configuration > Rooms Management: room types, classes, features, housekeeping
- Configuration > Accounts Receivable: AR types, aging, statements
- Configuration > Commission Processing: commission codes, payment methods
- Configuration > Setup: users, workstations, printers, IFC Control, EFT, business events
- Configuration > Property: property details, application settings, license codes, parameters

Workstations & Print Tasks (v5.6):
Path: Configuration > Setup > Workstations
- Identified by domain\computer name (e.g., HOTEL\FD01)
- Print tasks are workstation-specific not user-specific
- Adding: Highlight workstation > Print Tasks > New > Select Printer > Select Print Task
Key tasks: GST FOLIO (primary folio), NA REPORTS, AR FOLIO, DEP RECEIPTS, REG CARD, CONF LETTER, CASHIER CLOSE REP, HOUSEKEEPING, STATEMENT, REMINDER

OPI — Oracle Payment Interface V5 (Release 20.4):
Source: https://docs.oracle.com/cd/F36206_01/doc.193/f35751.pdf
Min versions: V5 Hosted 5.5.0.25.8+, V5.6.6+, On Premise 5.5.0.24.4+
IFC8 key format: FidCrypt0S|...
OPERA config: Configuration > Setup > Property Interfaces > Interface Configuration
Token Exchange: Auth User/Password must match OPERA > Setup > Property Interfaces > EFT IFC OPI > Custom Data tab

Business Events (OPERA 5):
Path: Configuration > Setup > Business Events > Configuration
Modules: Activity, Availability, Block, Cashiering, Housekeeping, Night Audit, Profile, Rate, Reservation, Stay Records
Rules: External System and Module must be selected first; queue purges messages >96 hours

## Channel Manager Migration Reference
External codes (SynXis, Profitroom, SiteMinder) — DO NOT CHANGE
OPERA Exchange (OXI) translation layer — CHANGES during migration
SiteMinder extranet rate plan links — VERIFY separately (exception)
OPERA internal rate codes — depends on rate restructure scope

Reporting break: migration creates permanent break at go-live date
Before go-live: produce legacy-to-new mapping for rate codes, market segments, source codes, room types
Production go-live mirrors UAT build — NOT lighter
OXI must be reconfigured before channel is live

## Fiscal Exports Reference

Portugal SAF-T:
Path: Administration > Interfaces > Fiscal > SAF-T Export
Format: XML per AT (Autoridade Tributária) schema
CFIDCL indicator: normal line = FIXED; credit note = CFIDCL
Amounts: SunSystems expects POSITIVE amounts for credits — CFIDCL signals credit, not negative sign
Document types: FT (Fatura), FS (Fatura Simplificada), NC (Nota de Crédito), ND (Nota de Débito)
Submission: monthly by 20th of following month via Portal das Finanças

Spain SII:
Format: XML per AEAT schema
Submission: within 4 days of invoice
Near-real-time — different schema and endpoint from Portugal

CSV/XML Export Rules:
- Credit lines: formula must return positive — use SIGN(x)*x
- Empty strings: '' only — encoding artefacts cause save failures
- Date format: confirm YYYYMMDD vs DD/MM/YYYY per receiver
- Encoding: UTF-8 for PT/ES special characters
- XML: validate against XSD before submitting to fiscal authority

## SUN Systems Back-Office Export Reference

SUN_CITY_LEDGER Column Mapping:
Col 1: JOURNAL_TYPE — fixed value e.g. CL
Col 2: TRX_DATE — transaction date
Col 3: TRX_AMOUNT — POSITIVE required for credit notes
Col 4: FIXED or CFIDCL — credit notes = CFIDCL
Col 5: ACCOUNT_CODE — GL account mapping
Col 6: COST_CENTRE — property/department code
Col 7: DESCRIPTION — transaction description
Col 8: TRX_AMOUNT_DECIMALS — decimal portion, also positive for credits

Credit note formula for Col 3 and Col 8: SIGN(TRX_AMOUNT) * TRX_AMOUNT
Alternative: ((TRX_AMOUNT * TRX_AMOUNT) ^ 0.5)

UAT vs PRD Workflow:
1. Export shell from PRD (read-only)
2. Import shell into UAT
3. Run Refresh Property in UAT
4. Rebuild formulas in UAT
5. Validate output against SunSystems format
6. Get Jorge + Abel approval
7. Apply to PRD
NEVER edit PRD formulas directly without approval.

Known issues:
- Encoding: accent char instead of '' — retype '' directly in OPERA, never paste from external editor
- ABS() / GREATEST() cause silent save failure — always test with sample export in UAT

## OHIP Developer Portal & Streaming Reference
Full guide: https://docs.oracle.com/en/industries/hospitality/integration-platform/ohipu/

Portal features:
- Affiliated Customers: self-manage credentials; Reissue button; gateway URL visible directly
- Analytics: REST (total/successful/failed), Streaming (Produced/Waiting/Consumed), Data APIs
- OCIM Client IDs: support >50 characters (v25.4+)
- Partner Suspension: label appears in portal; pending requests deleted; customers notified

Streaming alerts:
- Trigger: disconnected 60+ minutes
- Behaviour: hourly emails to Application record email until reconnection
- Action: check service, network, OAuth token expiry, reconnect

Monitoring:
- Produced: messages generated by OPERA Cloud
- Waiting: in queue not yet consumed — HIGH = client behind or disconnected
- Consumed: received by client

Developer Portal Admin:
- Register app: Applications > New
- Generate credentials: Applications > [App] > Credentials
- Subscribe events: Subscriptions > Add
- Add conditions: Subscriptions > [Event] > Edit > Conditions
- Monitor streaming: Analytics tab > Streaming

---
END OF PREPENDED CONTENT
---

# OHIP & OPERA Cloud Master Reference Skill

> **Scope**: OPERA Cloud Property REST APIs (v25.4/25.5), Oracle Hospitality Integration Platform (OHIP), Distribution APIs, Fiscal Compliance (Portugal/Spain), SUN Back Office, IFC8/Interface Configuration, and Tiago's EMEA Iberia operational context.

---

## 1. ARCHITECTURE OVERVIEW

### OPERA Cloud Layers
```
┌─────────────────────────────────────────────────────┐
│           Oracle Hospitality Integration Platform    │
│  (OHIP Portal + Developer Portal + Streaming API)   │
├──────────────────┬──────────────────────────────────┤
│  OPERA Cloud     │   OPERA Cloud Distribution       │
│  Property APIs   │   (Shop/Book/ARI/ResNotif/Content)│
├──────────────────┴──────────────────────────────────┤
│        OPERA Cloud PMS (Property Layer)              │
│   Reservations │ Profiles │ Cashiering │ Rooms       │
│   Rates │ Housekeeping │ Night Audit │ Fiscal        │
└─────────────────────────────────────────────────────┘
```

### Environment Types
| Env | Purpose | URL Pattern |
|-----|---------|-------------|
| Partner Sandbox | Free shared testing | `*.apaas.us2.oraclecloud.com` |
| Customer Non-Production (UAT) | Pre-production validation | `*.apaas.{region}.oraclecloud.com` |
| Customer Production (PRD) | Live operations | `*.apaas.{region}.oraclecloud.com` |
| EMEA Iberia Cluster | Portugal/Spain/specific EU | EMEA datacenter endpoint |

---

## 2. AUTHENTICATION & AUTHORIZATION

### OAuth 2.0 Client Credentials Flow
```http
POST /oauth/v1/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&username={user}%40{chain}.{env}.oracle.com&password={pass}&client_id={clientId}&client_secret={clientSecret}
```

**Response:**
```json
{
  "access_token": "eyJ...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

### Required Headers (all API calls)
```http
Authorization: Bearer {access_token}
x-app-key: {applicationKey}
x-hotelid: {propertyCode}
Content-Type: application/json
Accept: application/json
```

> **Note**: `x-hotelid` is always the OPERA property code (e.g., `LISBH`, `MADRC`). Some endpoints accept comma-separated multi-hotel IDs.

### OCIM Client Management
- **Client ID Format** (OHIP v25.3.1+): `<chain><enterprise><environment><clientName>`
- **Character Limit**: Up to 100 chars (OHIP v25.5.0 + OPERA Cloud v25.4.0+); legacy 50-char limit for older environments
- **Operations**: Search, Add, Clone (regenerates ID & secret), Delete, Manage
- **Default client**: Pre-created for each integration; additional clients can be created for scoped access
- **Affiliated Customers** (OHIP v25.x): Central customers can self-manage credentials and gateway URLs; use "Reissue" to regenerate credentials

### Token Strategy (production patterns)
- Cache tokens until `expires_in - 60s` to avoid clock drift issues
- Re-request with same `client_credentials` flow (no refresh token)
- Store `client_secret` in secrets vault (never in code or logs)
- Use separate client IDs per integration for audit traceability in OPERA logs

---

## 3. API MODULE REFERENCE (Property APIs)

Base URL: `https://{env}/hospitality/property/{module}/v1/`

### Complete Module List (from oracle/hospitality-api-docs)

| Spec File | Module Code | Domain |
|-----------|-------------|--------|
| `act.json` | ACT | Activities |
| `actcfg.json` | ACTCFG | Activities Configuration |
| `ars.json` | ARS | Advanced Reservation System |
| `blk.json` | BLK | Block Management |
| `blkasync.json` | BLK Async | Block Async Operations |
| `blkcfg.json` | BLKCFG | Block Configuration |
| `bof.json` | BOF | Back Office Finance |
| `chl.json` | CHL | Channels |
| `cms.json` | CMS | Content Management |
| `crm.json` | CRM | Customer Relationship Management |
| `crmasync.json` | CRM Async | CRM Async Operations |
| `crmcfg.json` | CRMCFG | CRM Configuration |
| `csh.json` | CSH | Cashiering |
| `cshasync.json` | CSH Async | Cashiering Async |
| `dvm.json` | DVM | Demand View Management |
| `entcfg.json` | ENTCFG | Enterprise Configuration |
| `evm.json` | EVM | Event Management (S&C) |
| `evmcfg.json` | EVMCFG | Event Management Configuration |
| `expcfg.json` | EXPCFG | Export Configuration |
| `fof.json` | FOF | Front Office Finance |
| `fofcfg.json` | FOFCFG | Front Office Finance Configuration |
| `hsk.json` | HSK | Housekeeping |
| `int.json` | INT | Interfaces |
| `intcfg.json` | INTCFG | Interface Configuration |
| `inv.json` | INV | Inventory |
| `invasync.json` | INV Async | Inventory Async |
| `lms.json` | LMS | Learning Management |
| `lov.json` | LOV | List of Values |
| `medcfg.json` | MEDCFG | Media Configuration |
| `oauth.json` | OAUTH | OAuth Token |
| `ops.json` | OPS | Operations |
| `par.json` | PAR | Packages |
| `parasync.json` | PAR Async | Packages Async |
| `repcfg.json` | REPCFG | Reporting Configuration |
| `rmcfg.json` | RMCFG | Room Management Configuration |
| `rmr.json` | RMR | Revenue Management |
| `rmrcfg.json` | RMRCFG | Revenue Management Configuration |
| `rsv.json` | RSV | Reservations |
| `rsvasync.json` | RSV Async | Reservation Async |
| `rsvcfg.json` | RSVCFG | Reservation Configuration |
| `rtp.json` | RTP | Rate Planning |
| `rtpasync.json` | RTP Async | Rate Planning Async |
| `tokenexchange.json` | TOKEN | Token Exchange |
| outbound/ | — | Outbound API specs |

---

## 4. KEY API OPERATIONS BY DOMAIN

### 4.1 Reservations (RSV)

**Core endpoints:**
```
GET    /reservations                     # Search reservations
POST   /reservations                     # Create reservation
GET    /reservations/{reservationId}     # Get reservation
PUT    /reservations/{reservationId}     # Update reservation
DELETE /reservations/{reservationId}     # Cancel reservation
POST   /reservations/{reservationId}/checkIn
POST   /reservations/{reservationId}/checkOut
POST   /reservations/{reservationId}/reinstate
PUT    /reservations/{reservationId}/roomAssignment
GET    /reservations/{reservationId}/folios
POST   /reservations/{reservationId}/folios  # Open new folio
PUT    /reservations/{reservationId}/linkedReservations
```

**Key query parameters:**
- `hotelId` (required when not in header)
- `reservationId` - OPERA internal ID
- `confirmationNumber` - Guest-facing number
- `arrivalDate`, `departureDate` - ISO 8601 date
- `profileId`, `nameId` - Guest profile linkage
- `roomType`, `roomNumber`
- `reservationStatus` (RESERVED, CHECKED_IN, CHECKED_OUT, CANCELLED, NO_SHOW, WAITLISTED)

**25.4 Updates:**
- `getReservation` updated with additional fields

### 4.2 Profiles / CRM (CRM)

```
GET    /profiles                         # Search profiles (guest, company, travel agent, source)
POST   /profiles                         # Create profile
GET    /profiles/{profileId}             # Get profile
PUT    /profiles/{profileId}             # Update profile
DELETE /profiles/{profileId}             # Merge/delete profile
GET    /profiles/{profileId}/stays       # Stay history
POST   /profiles/{profileId}/relationships
GET    /stagedProfileMatches             # Central: duplicate detection (NEW 25.4)
POST   /validatePhone                    # Phone validation (NEW 25.4)
```

**Profile types**: GUEST, COMPANY, TRAVEL_AGENT, SOURCE, CONTACT, GROUP

**25.4 Updates:**
- `getProfiles` updated
- `getStagedProfileMatches` added (Central badge — for SMART Profiles dashboard)
- `validatePhone` added for real-time phone validation integration
- Business Events now include: Created By, Updated By, relationship tracking
- Outbound type renamed: `SL_TNG` → `PROFILE_RESIDENCY`
- Spain: Last Name 2 and Last Name 3 fields (Country Mode = ES only)

### 4.3 Cashiering (CSH)

```
GET    /folios                           # List folios
POST   /folios                           # Create/open folio
GET    /folios/{folioId}                 # Get folio details
PUT    /folios/{folioId}                 # Update folio
POST   /folios/{folioId}/postCharge      # Post charge
POST   /folios/{folioId}/postPayment     # Post payment
POST   /folios/{folioId}/settle          # Settle folio
POST   /folios/{folioId}/transfer        # Transfer charges
GET    /deposits                         # Deposit ledger
POST   /deposits                         # Create deposit
POST   /calculateTaxes                   # Tax calculation (updated 25.4)
GET    /cashiers                         # Cashier management
POST   /cashiers/{cashierId}/open
POST   /cashiers/{cashierId}/close
```

**25.4 Cashiering Updates:**
- `CalculateTaxes` updated
- Cashier ID Sequence — auto-incremented with override restrictions
- Do Not Reopen Closed Folio OPERA Control
- Composite Deposit Handling — OPERA Control for automatic multi-code posting
  - `Deposit Default Transaction Code 1-3`
  - `Deposit Default Transaction Code 1-3 Percentage`
  - `Deposit Default Posting Type` (DEPOSIT_FOLIO or DEPOSIT_PAYMENT)
- Reserved Cashier — property-specific cashier
- Base Rates Max Number (default 75, configurable to 300)
- Package Category Configuration — new grouping mechanism for package codes

### 4.4 Rooms / Housekeeping (HSK + FOF)

```
GET    /rooms                            # Room list and status
GET    /rooms/{roomId}                   # Room details
PUT    /rooms/{roomId}/status            # Update room status (Vacant/Occupied/Dirty/Clean)
GET    /housekeeping/taskSheets          # Task sheets
PUT    /housekeeping/taskSheets/{id}     # Update task sheet
GET    /housekeeping/taskCompanion       # Task companion (updated 25.4)
POST   /housekeeping/roomConditions
GET    /roomAssignments
POST   /roomAssignments                  # Assign room
GET    /roomKeys                         # Room key info (updated 25.4)
```

**Room Status Values**: VACANT_CLEAN (VC), VACANT_DIRTY (VD), OCCUPIED_CLEAN (OC), OCCUPIED_DIRTY (OD), OUT_OF_ORDER (OOO), OUT_OF_SERVICE (OOS)

**25.4 FOF Updates:**
- `getHotelRooms` updated — new columns: Last Stay, Vacant Days, Next Stay Date, Nights to Next Stay
- `getRoomKey` updated
- Arrivals screen: Room Status Filter added
- Room Diary updated

**25.4 HSK Updates:**
- `getTaskCompanion` updated
- Task Sheet Companion and Presentation updated
- Turndown for Component Suites and Task Sheets updated

### 4.5 Rate Management (RTP)

```
GET    /ratePlans                        # List rate plans
POST   /ratePlans                        # Create rate plan
GET    /ratePlans/{ratePlanCode}         # Get rate plan
PUT    /ratePlans/{ratePlanCode}         # Update rate plan (updated 25.4)
POST   /ratePlans                        # Create rate plan (updated 25.4)
GET    /ratePlans/{code}/roomTypes       # Rate-room type linkage
GET    /negotiatedRates                  # Negotiated rates (updated 25.4, both variants)
GET    /templatePromotionCodes           # Promotion codes (updated 25.4)
POST   /ratePlans/async                  # Async rate operations
```

**25.4 RTP Updates:**
- `getNegotiatedRates` updated (both property and template variants)
- `getTemplatePromotionCodes` updated
- `packageCategory` added to `configPackagePrimaryDetailsType`
- `putRatePlan` and `postRatePlan` updated
- Owner Rate Code Attributes: Owner Rolling No Show, Owner Use, Owner Referral (checkboxes)

### 4.6 Inventory (INV)

```
GET    /inventoryStatistics              # Property inventory stats (updated 25.4)
GET    /blockInventoryStatistics         # Block inventory stats (updated 25.4)
GET    /blockInventoryStatistics/multiple # Multi-hotel stats (updated 25.4)
PUT    /inventory/restrictions           # Set restrictions
GET    /inventory/restrictions           # Get restrictions
POST   /inventory/hurdle                 # Hurdle rate management
```

**25.4 INV Updates:**
- `getBlockInventoryStatisticsMultipleHotelIds` updated
- `getBlockInventoryStatistics` updated
- `getInventoryStatistics` updated
- Alternate Property Availability updated for multiple hotel IDs
- Complimentary, House Use, Waitlist Rooms added to Property Availability

### 4.7 Block Management (BLK)

```
GET    /blocks                           # Search blocks (updated 25.4)
POST   /blocks                           # Create block
GET    /blocks/{blockId}                 # Get block
PUT    /blocks/{blockId}                 # Update block
POST   /blocks/{blockId}/copy            # Copy block (NEW 25.4)
POST   /blocks/{blockId}/roomingList     # Import rooming list
PUT    /blocks/{blockId}/subAllocation   # Convert to sub allocation (updated 25.4)
```

**25.4 BLK Updates:**
- `postCopyBlock` added — copies Regular, Master-Sub Blocks, and Tour Series including Forecast Rooms and Rates
- `RegularToSubAllocation` operation added
- `getBlocks` updated
- Import Rooming List: pipe '|' delimiter for multiple notes

### 4.8 Front Office Finance (FOF)

```
GET    /propertyAvailability             # Check property availability
GET    /lookToBook                       # Look to Book sales screen
GET    /restrictions                     # Manage restrictions
POST   /restrictions                     # Set restrictions
```

### 4.9 Event Management / S&C (EVM)

```
GET    /events                           # List events
POST   /events                           # Create event
POST   /events/{eventId}/copyNotes       # Copy event notes (NEW 25.4)
```

### 4.10 Enterprise Configuration (ENTCFG)

- `templateHotelDetailType` and `hotelDetailType` updated in 25.4
- Property Controls updated (Central badge)
- Property Details Configuration updated

---

## 5. ASYNC API PATTERNS

All `*async.json` modules follow a consistent async pattern for long-running operations.

### Standard Async Flow
```
1. POST /async/{operation}        → 202 Accepted + {operationId}
2. GET  /async/{operationId}      → status: IN_PROGRESS | COMPLETE | FAILED
3. GET  /async/{operationId}/result → actual data (when COMPLETE)
```

### Async Modules Available
- `blkasync` — Block async (large block operations)
- `crmasync` — CRM async (bulk profile operations)
- `cshasync` — Cashiering async (bulk posting)
- `invasync` — Inventory async (restrictions bulk update)
- `parasync` — Packages async
- `rsvasync` — Reservations async (bulk reservation operations)
- `rtpasync` — Rate Planning async (rate loading, bulk rate changes)

### Async Headers
```http
POST /rsv/v1/async/reservations
x-hotelid: LISBH
x-app-key: {key}
Authorization: Bearer {token}

→ HTTP 202
Location: /rsv/v1/async/reservations/{operationId}
```

### Polling Best Practice
```javascript
async function pollAsync(operationId, maxWait = 300000) {
  const start = Date.now();
  while (Date.now() - start < maxWait) {
    const resp = await get(`/async/${operationId}`);
    if (resp.status === 'COMPLETE') return get(`/async/${operationId}/result`);
    if (resp.status === 'FAILED') throw new Error(resp.errorMessage);
    await sleep(5000); // 5-second poll interval
  }
  throw new Error('Async operation timeout');
}
```

---

## 6. OHIP PLATFORM

### OHIP Portal vs Developer Portal
| Feature | OHIP Portal (Customer) | Developer Portal (Partner) |
|---------|----------------------|---------------------------|
| Manage integrations | ✓ | ✓ |
| Approve partner connections | ✓ | — |
| Subscribe to events | — | ✓ |
| Create event templates | — | ✓ |
| Analytics | ✓ | ✓ |
| Credential issuance | ✓ (Affiliated) | ✓ |

### Analytics Capabilities (OHIP)
- **REST API metrics**: Total calls, Successful calls, Failed/Error calls
- **Streaming metrics**: Messages Produced, Waiting, Consumed
- **Data API metrics**: Request count, Records returned, Error count
- Filter by: chain, property name (x-hotelid LOV)
- Chain-based filtering — property names shown only when chain selected

---

## 7. BUSINESS EVENTS & STREAMING

### Streaming Architecture
- **Model**: Push-based (server → client)
- **Protocol**: GraphQL subscription over WebSocket
- **Endpoint**: OHIP Streaming API
- **Playground**: GraphiQL (visual exploration tool)
- **Postman**: [Oracle Hospitality APIs Workspace](https://www.postman.com/hospitalityapis/workspace/oracle-hospitality-apis/collection/64e77a3c0d2905380767b08e)
- **Reference implementation**: [streaming-client (Node.js/TypeScript)](https://github.com/luisweir/streaming-client)

### Event Subscription Process
```
Partner Developer Portal:
1. Applications → Events tab → Add Event to Template
2. Configure: Category + Event + Filters + Conditions
3. Events → Subscribed tab → Subscribe → Select Environment → Select Hotels
4. Status: Pending Approval

Customer OHIP Portal:
5. Partner Applications → Select App → Events tab → Approve
6. Status: In Progress → Approved (active streaming)
```

### Event Subscription Status Indicators
| Color | Meaning |
|-------|---------|
| Green | Actively connected |
| Amber | No connection for 30+ minutes |
| Red | No connection for 1+ hour |
| None | Pending approval / in progress / not connected |

### Streaming Connection Alerts
- **Trigger**: Stream disconnected for 60+ minutes (red status)
- **Frequency**: Hourly emails until reconnection
- **Sandbox**: Only if "Streaming Connection Alerts" checkbox enabled
- **Production**: Always enabled by default
- **Email**: Sent to registered application email address
- **Action**: Check Application email address is current

### Automatic Approval Cases (No manual approval needed)
- Partner consuming from partner sandbox
- Partner consuming from their dedicated non-production environment
- Customer consuming from their own non-production environment
- Customer consuming from their own production environment

### Business Event Categories & Types

**Reservation Events:**
- `RESERVATION_NEW` — New reservation created
- `RESERVATION_UPDATE` — Reservation modified
- `RESERVATION_CHECKED_IN` — Check-in completed
- `RESERVATION_CHECKED_OUT` — Check-out completed
- `RESERVATION_CANCELLED` — Reservation cancelled
- `RESERVATION_NO_SHOW` — No-show posted
- `RESERVATION_REINSTATED` — Cancelled/no-show reinstated
- `RESERVATION_WAITLISTED` — Added to waitlist

**Profile Events (25.4 Updates):**
- `NEW_PROFILE` — Profile created (includes: Created By)
- `UPDATE_PROFILE` — Profile modified (includes: Created By, Updated By)
- `DELETE_PROFILE` — Profile deleted (includes: Updated By)
- `RELATIONSHIP_CREATED`, `RELATIONSHIP_UPDATED`, `RELATIONSHIP_DELETED`

**Rate/Inventory Events:**
- `RATE_UPDATE` — Rate plan changes
- `RESTRICTION_UPDATE` — Restriction changes
- `INVENTORY_UPDATE` — Inventory changes

**Housekeeping Events:**
- `ROOM_STATUS_UPDATE` — Room status changed

**Financial Events:**
- `FOLIO_CREATED`, `FOLIO_UPDATED`, `FOLIO_SETTLED`
- `CHARGE_POSTED`, `PAYMENT_POSTED`

### Event Conditions & Filters
- **Filters**: Data element-level — event fires only when specified elements change
- **Conditions**: Expression-based filtering
  - Operators: `=`, `!=`, `LIKE`, `NOT LIKE`, `IN`, `NOT IN`, `BETWEEN`, `<=`, `IS NULL`, `IS NOT NULL`
  - Logic: Combine with `AND` / `OR`
  - Max value length: 500 characters per condition
- **"Conditions Set" flag**: Visual indicator (25.x) — appears when condition added, disappears when all removed

### Polling Subscription (Alternative to Streaming)
- Configurable polling intervals
- For environments where streaming WebSocket is not feasible
- Configured via OHIP Portal

---

## 8. DISTRIBUTION APIS

### Distribution API Modules
| API | Purpose |
|-----|---------|
| Shop | Availability and rate shopping |
| Book | Create/modify/cancel reservations via channel |
| ARI | Availability/Rate/Inventory push to channels |
| ResNotif | Reservation notification to OTA/channel partners |
| Content | Property content (descriptions, images, amenities) |

### Distribution 25.4 Notes
- No new features in Distribution 25.4 (maintenance release)
- Channel Management: New Channel Offers menu in Distribution Administration
- Hurdle Rates Evaluation for Restriction Publication (LARGE scale feature)
  - FPLOS (Full Pattern Length of Stay) evaluation
  - Automatic LOS restriction generation
  - Bidirectional control (accept/reject specific night counts)
  - Supported channels: OTAs, Wholesalers, Bed Banks, Tour Operators
  - Requires: OPERA Cloud Distribution 25.4+ AND `YIELD_MANAGEMENT` control enabled
- Improved Copy Channel Rates Flow:
  - Option to copy associated room types
  - Copy default distribute attributes
  - Sortable channel rate plan name

### ARI Push Flow
```
OPERA Cloud → ARI API → Channel Manager / GDS / OTA
Rate changes, availability updates, restrictions → real-time
```

### ResNotif Flow
```
Booking via channel → OTA/GDS → ResNotif → OPERA Cloud Reservation
Modification/Cancellation → ResNotif → OPERA Cloud update
```

---

## 9. IFC8 & INTERFACE CONFIGURATION (INT/INTCFG)

### Interface Types in OPERA Cloud
| Type | Description |
|------|-------------|
| PMS | Property Management System (self) |
| POS | Point of Sale (F&B, Spa, Golf) |
| GDS | Global Distribution System |
| CRO | Central Reservations Office |
| SPA | Spa Management |
| GOLF | Golf Management |
| KEY | Key Card System |
| DOOR | Door Lock System |
| KIOSK | Self-service Kiosk |
| EXT_SYSTEM | Generic external system |

### IFC8 Configuration
- IFC8 = OPERA Interface Component (middleware bridge)
- Runs as Windows service or OCI-hosted connector
- Connects OPERA Cloud ↔ 3rd party systems via TCP/IP message-based protocol
- Messages: XML-based with OPERA-specific schemas

### Interface API Operations (INT)
```
GET    /interfaces                       # List configured interfaces
POST   /interfaces                       # Create interface
GET    /interfaces/{interfaceId}         # Get interface details
PUT    /interfaces/{interfaceId}         # Update interface
POST   /interfaces/{interfaceId}/activate
POST   /interfaces/{interfaceId}/deactivate
GET    /interfaces/{interfaceId}/users   # Interface users
POST   /interfaceUsers                   # Create interface user
```

**25.4 INT Updates:**
- Manage Interface Users Screen updated
- Copy Interface Setup — replicate configurations across properties
- OXI Credit Card Data Handling — tokenized data only for outbound communications
- Exchange Message Status Search — All Property Profiles checkbox

### OXI/Exchange Interface Configuration
- **OXI** (OPERA Xchange Interface) — HTTP-based integration
- **OXI-HUB** — Hub model for multi-property OXI
- Auto-configuration: Service Locator setup for post/put/delete Profile APIs (25.4)
- OPI (OPERA Payment Interface) Auto-Configuration: Pre-populated Merchant ID and payment service endpoints (25.4)

---

## 10. FISCAL COMPLIANCE

### 10.1 Portugal Fiscal — SAF-T

**SAF-T PT (Standard Audit File for Tax — Portugal)**
- Mandatory for Portuguese fiscal compliance
- Format: XML (structured according to AT — Autoridade Tributária)
- Generated via OPERA Fiscal Regulatory Compliance module
- Covers: Invoices, Payments, General Ledger entries

**CFIDCL / DFIDCL Indicator Logic:**
- `CFIDCL` (Certified Fiscal Invoice Document Control Line) — Line-level fiscal control indicator
- `DFIDCL` (Document Fiscal Invoice Document Control Line) — Document-level fiscal control
- Used to track which folios/transactions are included in SAF-T export
- Ensures no gaps in sequential invoice numbering (Portuguese law requirement)
- Export must capture ALL posted documents in sequential order

**Invoice Sequence Rules (Portugal):**
- Invoices must be sequentially numbered with no gaps
- Cannot delete/void invoice numbers — must issue credit notes
- Series types: FT (Fatura), FS (Fatura Simplificada), NC (Nota de Crédito), ND (Nota de Débito)
- Each series tracked independently in CFIDCL/DFIDCL

**Folio Types for SAF-T:**
```
FT  = Fatura (Invoice) — VAT registered entities
FS  = Fatura Simplificada (Simplified Invoice) — consumers < €1000
NC  = Nota de Crédito (Credit Note)
ND  = Nota de Débito (Debit Note)
FR  = Fatura Recibo (Invoice-Receipt for services)
```

**Export Process (OPERA):**
1. Administration → Exports → Fiscal Exports → SAF-T PT
2. Select date range (typically monthly — due by 20th of following month)
3. Validate before submission (check for sequence gaps, missing NIF)
4. Export XML → Submit to AT via Portal das Finanças

### 10.2 Spain Fiscal

**25.4 Spain Updates:**
- Last Name 2 (paternal surname) and Last Name 3 (maternal surname) fields
- Available only when `Country Mode = ES`
- Police R+A Reports updated to include new name fields
- Universal Payload: Support for new name elements

**Spain-Specific Fiscal Requirements:**
- SII (Suministro Inmediato de Información) — real-time VAT reporting to AEAT
- Invoice types: F1, F2, F3, F4, F5, F6, R1-R5
- Hotel as self-billing agent for commission invoices
- Police reports (Libro de Viajeros) — mandatory guest registration

### 10.3 CSV and XML Export Formats

**Generic Fiscal Export Types:**
- **CSV** — Tabular export for back-office import (SUN, SAP, Opera Reporting)
- **XML** — Structured export for tax authority submission
- **UPE** (Universal Payload Export) — OPERA's configurable export engine

**UPE Templates (25.4 Updates):**
- Updated to V2.1 specifications
- Supports merge codes: `ASSOCIATED_BILL_NO`, `ASSOCIATED_BILL_NO_HEADER`
- Prefix support for "Append Prefix" OPERA Control
- Folio History Screen: Prefix included in Assoc Folios column

### 10.4 Poland Fiscal (PL_JPK_FA)
- VAT element population for deposits updated (25.4)
- Folio Settlement: Pre-Stay folio association updates
- Universal Payload: `AssociatedTransactionDetails` and `DocumentCode` elements

### 10.5 Italy Fiscal (25.4)
- Information Collection Methods: `FOLIO_DESTINATION` support
- Default Method for Folio Destination: Print/Email/Email and Print
- Custom Charges: New Rule Type "Rate Perc With Limit Per Person"
- Philippines: Sample_Folio_Philippines updated (package allowance in footer breakdown)
- Monaco: R+A MCO-Police_Report updated (performance + accompanying guest data)

---

## 11. SUN BACK OFFICE EXPORT

### SUN Financial System Integration
SUN (Software Users Group) is a financial/GL system used in luxury hotel groups.

### Export Configuration
- **Export Type**: Typically CSV or fixed-format text
- **Location in OPERA**: Administration → Exports → Back Office Exports
- **Schedule**: Usually nightly (triggered after Night Audit/EOD)
- **Transfer**: SFTP to SUN server or shared folder

### Account Code Mapping
```
OPERA Transaction Code → SUN Account Code
Room Revenue (1000)    → GL 4100 (Room Revenue)
F&B Revenue (2000)     → GL 4200 (Food Revenue)
Other Revenue (3000)   → GL 4300 (Other Revenue)
City Ledger (CL)       → GL 1200 (Accounts Receivable)
Deposits               → GL 2100 (Advance Deposits)
```

### SUN Export Formula Logic
SUN exports require **balanced journal entries** (Debit = Credit):

```
Revenue Lines:
  Debit:  Guest Ledger / City Ledger account
  Credit: Revenue account (per transaction code)

Payment Lines:
  Debit:  Cash / CC / Bank account
  Credit: Guest Ledger (reduces AR balance)

Tax Lines:
  Debit:  Tax Collected account (liability cleared)
  Credit: Tax Payable account

Deposit Lines:
  Debit:  Advance Deposit account
  Credit: Revenue account (on redemption)
```

### Common SUN Export Issues
- **Imbalanced entries**: Check if all transaction codes are mapped
- **Missing property code**: Ensure multi-property export has correct entity codes
- **Date mismatch**: Business date vs system date — use Business Date for fiscal periods
- **Package posting**: Package components must post to correct GL buckets
- **Void transactions**: Credits must appear as negative entries in same period

### Export Development Checklist
```
□ Transaction code → GL account mapping complete
□ Property entity code configured
□ Tax breakdown separates net revenue from tax
□ Foreign currency transactions converted at correct rate
□ Deposits tracked as liabilities until redemption
□ Night Audit date boundary correctly applied
□ Balanced debit/credit for every entry set
□ Character encoding: UTF-8 (or as required by SUN server)
□ Field delimiter and decimal separator match SUN config
```

---

## 12. NIGHT AUDIT & END OF DAY

### Night Audit Process in OPERA Cloud
1. **Pre-Audit Checks**:
   - All cashiers closed
   - No open folios with pending charges
   - Room status reconciliation
   - Revenue journals balanced

2. **Night Audit Sequence**:
   ```
   a. Roll Business Date
   b. Run Auto Room Rate Posting
   c. Post Package Charges
   d. Run Deposit Forfeiture (if applicable)
   e. Run No-Show Processing
   f. Run Complimentary & House Use
   g. Print/Email End-of-Day Reports
   h. Run Back Office Export
   i. Run SAF-T / Fiscal Export (if nightly)
   j. Archive and close
   ```

3. **Post-Audit**:
   - New business date active
   - Previous day transactions locked
   - Reports available in R+A

### OPERA Controls for Night Audit (25.4)
- **Do Not Reopen Closed Folio** — prevents folio reopening after EOD
- **Automatic End of Day Email Language** — configures language for EOD email
- **Guest Service Status EOD** — Reset Guest Service Status at EOD (configurable)

### Business Date vs System Date
- **Business Date**: The fiscal/revenue date (changes at end of Night Audit)
- **System Date**: Actual calendar date
- During Night Audit, Business Date ≠ System Date
- All revenue posting uses Business Date
- APIs: `x-businessdate` header can override for historical posting (with appropriate rights)

---

## 13. RATE & RESTRICTION MANAGEMENT

### Rate Plan Structure
```
Rate Plan Code
├── Rate Plan Type (BAR, RACK, CORPORATE, PACKAGE, etc.)
├── Rate Tiers (date-range based)
│   ├── Room Type rates
│   │   ├── Adult 1, 2, 3, 4 rates
│   │   └── Child rates
│   └── Applicable days (MTWTFSS pattern)
├── Restrictions (MinLOS, MaxLOS, Closed to Arrival, Closed to Departure)
├── Promotion Codes
└── Derived Rates (% or $ off base rate)
```

### Restriction Types
| Code | Description |
|------|-------------|
| CLOSED | Property/Room Type closed |
| CTA | Closed to Arrival |
| CTD | Closed to Departure |
| MINLOS | Minimum Length of Stay |
| MAXLOS | Maximum Length of Stay |
| MINSTAY | Minimum Stay Through |

### Hurdle Rate Logic (25.4 Distribution)
- Evaluates FPLOS (Full Pattern Length of Stay) bids
- Automatically generates LOS restrictions based on yield thresholds
- Bidirectional: can accept OR reject specific night counts
- Applies to: OTAs, Wholesalers, Bed Banks, Tour Operators
- Prerequisite: `YIELD_MANAGEMENT` OPERA Control enabled

---

## 14. ERROR HANDLING & RATE LIMITS

### HTTP Status Codes
| Code | Meaning | Action |
|------|---------|--------|
| 200 | OK | Success |
| 201 | Created | Resource created |
| 202 | Accepted | Async started |
| 204 | No Content | Success, no body |
| 400 | Bad Request | Fix request payload |
| 401 | Unauthorized | Re-authenticate (token expired) |
| 403 | Forbidden | Check role/scope/module |
| 404 | Not Found | Check ID, property code |
| 409 | Conflict | Duplicate or state conflict |
| 422 | Unprocessable Entity | Business rule violation |
| 429 | Too Many Requests | Apply backoff |
| 500 | Server Error | Retry with backoff, escalate if persistent |
| 502/503 | Gateway/Service Unavailable | Scheduled maintenance or outage |

### Oracle Error Response Format
```json
{
  "type": "https://docs.oracle.com/error-help/...",
  "title": "string",
  "status": 400,
  "detail": "Human-readable error description",
  "instance": "operationId or path",
  "o:errorCode": "OPERA_SPECIFIC_CODE",
  "o:errorPath": "$.fieldName"
}
```

### Rate Limits
- **Property APIs**: ~100 requests/minute per `x-hotelid` (varies by endpoint)
- **Async endpoints**: Lower limits; use async pattern for bulk operations
- **Streaming**: No polling — push-based eliminates rate limit concern
- **429 handling**: Respect `Retry-After` header; implement exponential backoff

### Exponential Backoff Pattern
```javascript
async function callWithRetry(fn, maxRetries = 5) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn();
    } catch (err) {
      if (err.status === 429 || err.status >= 500) {
        const delay = Math.min(1000 * 2 ** i + Math.random() * 1000, 30000);
        await sleep(delay);
      } else throw err;
    }
  }
}
```

### Common Error Scenarios

| Error | Typical Cause | Fix |
|-------|--------------|-----|
| 401 Unauthorized | Token expired | Re-authenticate, cache tokens properly |
| 403 Forbidden | Missing module/role | Check OPERA role → API access mapping |
| 404 Not Found | Wrong hotelId or ID | Verify `x-hotelid` header and resource ID |
| 409 Conflict | Duplicate reservation | Check `confirmationNumber` before posting |
| 422 Business Rule | Date/rate/room violation | Read `o:errorPath` for field, check business rules |
| 429 Rate Limited | Too many requests | Implement backoff, use async endpoints |

---

## 15. BREAKING CHANGES TRACKER (v24.1 → v25.4)

### v25.4 Breaking Changes / Notable Updates
- **Outbound Type Renamed**: `SL_TNG` → `PROFILE_RESIDENCY` (profile integration)
- **Spain Name Fields**: Last Name 2/3 added — update profile create/update payloads for ES properties
- **Package Category**: New required grouping — `configPackagePrimaryDetailsType` updated
- **ClientId Length**: Now up to 100 chars (was 50) — update credential management systems

### v25.3 Notable Changes
- Human-readable Client ID format introduced: `<chain><enterprise><environment><clientName>`
- OHIP Analytics enhanced with property name display

### v25.2 Notable Changes
- SMART Profiles Dashboard (Central) — staged profile matches API introduced

### v24.x → v25.x Migration
- Review async endpoint patterns — poll intervals standardized
- Check `x-hotelid` support for multi-hotel endpoints
- UPE templates: ensure V2.1 compatibility for fiscal exports

### Deprecation Watch
- Monitor Oracle deprecation notices on Developer Portal
- Subscribe to OHIP release notes for upcoming breaking changes
- Test in UAT before PRD deployment for every major release update

---

## 16. OPERA CONTROLS (CONFIGURATION FLAGS)

### Key OPERA Controls Reference

**Cashiering Group:**
| Control | Purpose |
|---------|---------|
| `DO_NOT_REOPEN_CLOSED_FOLIO` | Prevents folio reopening post-EOD |
| `ENABLE_PROPERTY_SPECIFIC_FOLIO_COPY_LEGEND` | Property-specific folio copy |
| `ENABLE_MANUAL_COMPOSITE_DEPOSIT_HANDLING` | Multi-code deposit posting |
| `AUTOMATIC_END_OF_DAY_EMAIL_LANGUAGE` | EOD email language |
| `RESERVED_CASHIER` | Property-specific cashier |
| `BASE_RATES_MAX_NUMBER` | Max derived rates (default 75, max 300) |

**Profile Group:**
| Control | Purpose |
|---------|---------|
| `DUPLICATE_COMMUNICATION` | Allow identical comm values |
| `PROFILE_PHONE_VALIDATION` | Real-time phone validation |
| `EXCLUDE_MINI_PROFILE` | Hide temp suspension placeholders |
| `PURGE_CONTROL_MODE` | Local data retention |
| `ANONYMIZATION_CONTROL_MODE` | GDPR anonymization |
| `SMART_PROFILES` | Central duplicate dashboard |

**Distribution Group:**
| Control | Purpose |
|---------|---------|
| `HURDLE_RATES_EVALUATION` | FPLOS restriction auto-generation |
| `YIELD_MANAGEMENT` | Prerequisite for hurdle rates |

**Room Management Group:**
| Control | Purpose |
|---------|---------|
| `GUEST_SERVICE_STATUS_EOD` | Reset Guest Service Status at EOD |

### Enabling OPERA Controls
```
Side Menu → Administration → Enterprise → OPERA Controls
→ Select Property → Select Group → Activate Control → Configure Parameters → Save
```

---

## 17. REPORTS & ANALYTICS (R+A)

### Key Report Categories
- **Financial**: Guest Ledger, Deposit Ledger, Revenue Journals
- **Front Desk**: Arrivals, Departures, In-House
- **Housekeeping**: Task Sheets, Room Status, Discrepancy Reports
- **Revenue**: Statistics, Occupancy, ADR, RevPAR
- **Fiscal**: SAF-T, Police Reports, VAT Reports
- **Audit**: Cashier Reports, Night Audit Pack

### 25.4 New Reports
- **Deposit Ledger Report** — added to Financial folder in R+A and OPERA Cloud
- **Guest Ledger Report** — added to R+A Financial folder
- **void_folio_det RTF Report** — parameter added for date selection

### Key Merge Codes (Folio Templates)
```
ASSOCIATED_BILL_NO         — Associated bill/folio number
ASSOCIATED_BILL_NO_HEADER  — Header version of associated bill number
{PREFIX}                   — Folio prefix (when Append Prefix enabled)
```

---

## 18. MEMBERSHIP & LOYALTY

```
GET    /memberships                      # List memberships
POST   /memberships                      # Enroll
GET    /memberships/{membershipId}       # Get membership
PUT    /memberships/{membershipId}       # Update membership
GET    /membershipTransactions           # Transaction history
POST   /memberReferrals                  # Member referral (NEW 25.4 — Loyalty badge)
GET    /suspendedStays                   # Suspended stays (updated 25.4)
PUT    /suspendedStays/{stayId}          # Edit booking date in suspended stay (25.4)
```

---

## 19. TIAGO'S OPERATIONAL CONTEXT

### Role & Focus
- **Region**: EMEA Iberia cluster (Portugal + Spain)
- **Specialization**: Portugal/Spain fiscal compliance specialist
- **Work**: Integration development, OHIP configuration, fiscal export validation, back-office integration

### Property Portfolio (Luxury Brands)
| Brand | Notes |
|-------|-------|
| Kempinski | Ultra-luxury, stringent fiscal requirements |
| Corinthia | Luxury, often multi-outlet fiscal complexity |
| Hyatt | Multiple sub-brands (Park Hyatt, Andaz, Grand Hyatt) |
| Marriott | Complex loyalty (Bonvoy) integration |
| Hilton | Honors loyalty, Conrad/Waldorf sub-brands |
| IHG | IHG One Rewards, Kimpton, Six Senses |
| Rosewood | Ultra-luxury, often standalone fiscal setup |

### UAT vs PRD Environment Discipline

**Always test in UAT before PRD:**
```
UAT verification checklist:
□ OAuth token generation works
□ API endpoint responds with correct data
□ Business Events firing as expected
□ Fiscal export sequence numbers reset correctly in UAT (not continuous with PRD)
□ Rate/availability data is test data (not live)
□ SAF-T XML validates against AT schema
□ SUN export balances correctly
```

**Key differences to watch:**
- UAT may have different property codes than PRD
- UAT fiscal sequences are independent of PRD (no fiscal impact)
- UAT Business Events may have lower volume — test with manual triggers
- UAT rate data often stale — use rate upload for integration testing

### EMEA Iberia Cluster Specifics
- **Portugal Properties**: Fiscal compliance via SAF-T PT, mandatory sequential invoicing, AT certification
- **Spain Properties**: SII integration for real-time VAT, Police reports (Libro de Viajeros), DNI/NIE validation
- **Currency**: EUR for both — no FX complexity typically
- **Time Zone**: Europe/Lisbon (UTC+0/+1), Europe/Madrid (UTC+1/+2) — critical for Business Date handling
- **Language**: PT/ES profile fields; folios in local language required for fiscal compliance

### Common Integration Patterns in Iberia
1. **SAF-T Monthly Export** → AT submission (Portugal) — verify CFIDCL/DFIDCL completeness
2. **SUN Back Office Nightly** → After Night Audit → GL journals balanced
3. **Police Reports** → Nightly export → Guardia Civil / PSP submission
4. **Channel Distribution** → ARI push to OTAs → ResNotif for booking confirmation
5. **Profile Sync** → CRM async → Loyalty/CRS systems → validate de-duplication

### Fiscal Compliance Priority Matrix
| Country | System | Frequency | Urgency |
|---------|--------|-----------|---------|
| Portugal | SAF-T PT | Monthly (by 20th) | HIGH — legal obligation |
| Portugal | Sequential invoices | Real-time | CRITICAL — AT certification |
| Spain | SII | 4 days after invoice | HIGH — AEAT obligation |
| Spain | Police reports | Daily | HIGH — legal obligation |

---

## 20. QUICK REFERENCE CHEATSHEET

### Most-Used API Patterns

```bash
# 1. Get token
POST /oauth/v1/token
body: grant_type=client_credentials&username=...&password=...&client_id=...&client_secret=...

# 2. Search reservations by arrival date
GET /rsv/v1/reservations?hotelId=LISBH&arrivalDate=2025-12-25&departureDate=2025-12-30

# 3. Get specific reservation
GET /rsv/v1/reservations/{id}
Headers: x-hotelid: LISBH, Authorization: Bearer {token}, x-app-key: {key}

# 4. Post charge to folio
POST /csh/v1/folios/{folioId}/postCharge
{
  "transactionCode": "1000",
  "amount": 150.00,
  "reference": "Room charge"
}

# 5. Update room status
PUT /hsk/v1/rooms/{roomId}/status
{ "status": "VACANT_CLEAN" }

# 6. Start async rate load
POST /rtp/v1/async/ratePlans
→ 202 + operationId → poll /rtp/v1/async/{operationId}

# 7. Check SAF-T export completeness
GET /expcfg/v1/fiscalExports?hotelId=LISBH&exportType=SAFT_PT&businessDate=2025-11-30
```

### Property Code Examples (Iberia)
```
Portugal: LISBH (Lisbon), OPORTO (Porto), FUNCH (Funchal/Madeira)
Spain:    MADRC (Madrid), BARCH (Barcelona), SEVIL (Seville)
```

### Key OHIP URLs
```
Developer Portal: https://www.oracle.com/industries/hospitality/hotel-integrations/integration-platform/
OHIP Portal:      Accessible via Oracle Cloud Infrastructure console
Postman:          https://www.postman.com/hospitalityapis/workspace/oracle-hospitality-apis
GitHub:           https://github.com/oracle/hospitality-api-docs
Streaming Client: https://github.com/luisweir/streaming-client
```

### Header Template
```http
Authorization: Bearer eyJ...{access_token}
x-app-key: {your-application-key}
x-hotelid: {OPERA-property-code}
Content-Type: application/json
Accept: application/json
```

---

## 21. DEBUGGING & TROUBLESHOOTING GUIDE

### API Not Returning Data
1. Check `x-hotelid` — must match OPERA property code exactly (case-sensitive)
2. Verify module is licensed for the property
3. Check OPERA role has task permission for the API operation
4. Verify `x-app-key` matches the registered application
5. Confirm token not expired (re-auth if in doubt)

### Business Events Not Firing
1. Check subscription status (green dot = active)
2. Verify OPERA property has Business Events enabled
3. Check event template configuration — conditions may be too restrictive
4. Look for amber/red status — reconnect streaming
5. Check OHIP Portal for partner suspension notices
6. Verify streaming connection email — may have alerts

### Fiscal Export Issues
1. **Sequence gap**: Check for voided invoices without credit notes
2. **Missing NIF**: Profile must have valid VAT number for B2B invoices
3. **SAF-T validation failure**: Run AT's schema validator before submission
4. **CFIDCL not set**: Verify all folios are flagged before export window
5. **SUN imbalance**: Audit each transaction code → GL mapping

### Night Audit Stuck
1. Check for open cashiers — all must be closed
2. Check for reservations in interim states (pre-checking without completion)
3. Review EOD control settings
4. Check for interface errors in INT module preventing audit
5. Review OPERA Cloud Support logs if audit process hangs

---

## 22. VERSION HISTORY & RELEASE CADENCE

OPERA Cloud releases quarterly:
- **25.4** — October 2025 (current reference)
- **25.3** — July 2025
- **25.2** — April 2025
- **25.1** — January 2025
- **24.4** — October 2024
- **24.3** — July 2024
- **24.2** — April 2024
- **24.1** — January 2024

**Release numbering**: `YY.Q` format (year.quarter)

**API versioning**: `/v1/` base — within a major version, backward compatible; breaking changes trigger major version bump or deprecation notices.

**Recommended practice**: Subscribe to Oracle Hospitality release notes and test in UAT environment within 2 weeks of each quarterly release before production cutover.

---

*This skill synthesizes Oracle Hospitality Integration Platform documentation, OPERA Cloud 25.4/25.5 feature summaries, Distribution API specs, and operational context for EMEA Iberia fiscal compliance work. Last updated: 2026-06-08.*
