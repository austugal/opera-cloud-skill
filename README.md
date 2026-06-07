# opera-cloud-skill

A factual, public **Claude skill** for Oracle Hospitality OPERA Cloud and OPERA 5 Property
Management Systems.

It gives any AI assistant grounded, vendor-documented knowledge of OPERA Cloud, OPERA 5, the Oracle
Hospitality Integration Platform (OHIP), REST APIs, Business Events and streaming, Distribution
APIs, IFC8/OXI interfaces, fiscal and back-office exports, night audit, rate management, cashiering,
and configuration, so it can answer OPERA questions accurately instead of guessing.

## What a skill is

A skill is a folder containing a `SKILL.md` file plus optional `references/`. Compatible AI agents
(such as Claude) load `SKILL.md` when a task matches its description, then open the relevant
reference file on demand. This keeps answers accurate and focused without loading everything at once.

## Contents

```
opera-cloud-skill/
  SKILL.md                          Router + high-frequency core knowledge
  references/
    architecture-and-auth.md        Architecture, environments, OAuth, headers, OCIM
    api-modules.md                  REST API modules and key operations by domain
    async-streaming-events.md       Async APIs, Business Events, streaming
    distribution-channels.md        Distribution APIs and channel manager migration
    interfaces-ifc8.md              IFC8, OXI, interface configuration
    fiscal-compliance.md            SAF-T and country fiscal exports (generic)
    backoffice-export.md            Ledger exports and the formula engine
    night-audit-controls.md         Night audit / end of day and OPERA Controls
    rates-restrictions.md           Rate plans, restrictions, hurdle logic
    error-handling.md               Status codes, retries, rate limits
    version-changes.md              Release cadence and change tracking
    opera5-pms.md                   OPERA 5 PMS configuration
    quick-reference.md              Cheatsheet
```

## How to use it

### With Claude (Cowork / Claude Code / compatible agents)

Place this folder where your agent loads skills, or install it as part of a skills bundle. The
agent reads `SKILL.md` automatically when a request matches OPERA, OPERA Cloud, OPERA 5, OHIP,
IFC8, night audit, rate derivation, or related hospitality-PMS topics.

### As a plain reference

Every file is Markdown. Read them directly. Start with `SKILL.md`, then open the reference file that
matches your question.

## Scope and principles

- **Factual and public only.** Content reflects publicly documented OPERA behaviour. Where something
  is field-observed rather than documented, it is labelled as such.
- **No secrets.** No credentials, client IDs, secrets, passwords, private hostnames, IP addresses,
  internal URLs, or customer-identifying data appear anywhere in this skill.
- **Version-aware.** OPERA Cloud ships roughly quarterly. The skill tells the assistant to confirm
  version-specific behaviour against the running release rather than asserting it blindly.
- **Authoritative sources.** For verification, the skill points to the Oracle Help Center OPERA
  documentation, the OHIP Developer Portal, and Oracle's published hospitality API specifications.

## Disclaimer

This is an independent reference skill. It is not affiliated with, endorsed by, or an official
product of Oracle. OPERA, OPERA Cloud, and OHIP are Oracle products and trademarks of Oracle
Corporation. Always verify against official Oracle documentation for the exact release in use before
acting on production systems.

## Contributing

Corrections and additions are welcome by pull request, provided they are factual, public, and
contain no confidential or customer-identifying information. Cite the public source (Oracle
documentation reference) for any new factual claim, or label it clearly as observed behaviour.

## License

MIT. See `LICENSE`.
