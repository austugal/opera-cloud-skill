---
name: support-troubleshooting-guide
title: OPERA PMS Support and Troubleshooting Guide
author: Tiago Fitas, Senior Oracle Opera Cloud, OPERA 5, Fidelio Software Engineer Specialist
date: 2026-06-07
credentials_removed: yes
---

# OPERA PMS Support and Troubleshooting Guide

Operational guide for diagnosing and resolving common OPERA PMS issues.

---

## Support Escalation Path

### Level 1: Front-Line Support (Hotel Operations)
**Time to resolve:** 15-30 minutes  
**Issues:** User errors, simple resets, connectivity

- Check application and server status
- Restart OPERA client application
- Clear application cache
- Verify user permissions
- Check network connectivity
- Review recent changes or system updates

If issue persists → **Escalate to Level 2**

### Level 2: Technical Support (IT Department)
**Time to resolve:** 30 minutes - 2 hours  
**Issues:** Configuration, integration, performance

- Review application and system logs
- Check database connectivity
- Verify server resources (CPU, memory, disk)
- Test network latency and bandwidth
- Verify recent deployments or patches
- Check for known issues in release notes
- Reproduce issue in test environment

If issue persists → **Escalate to Level 3**

### Level 3: Engineering Support (Oracle)
**Time to resolve:** 2-24 hours  
**Issues:** Bugs, data corruption, system-level failures

- Document complete issue reproduction steps
- Collect diagnostic bundles (logs, traces)
- Open Oracle support case with SR number
- Provide server configuration and version info
- Attach error messages and log excerpts
- Describe business impact and urgency

---

## Troubleshooting Methodology

### 1. Gather Information
- **When did issue start?** (date, time, after changes?)
- **Who is affected?** (1 user, department, property-wide?)
- **What changed recently?** (patches, config, deployment?)
- **Error messages?** (exact text, error codes)
- **Reproducible?** (consistent or intermittent?)

### 2. Isolate the Scope
- **Workstation issue?** (test from different workstation)
- **Network issue?** (test connectivity, latency, bandwidth)
- **Server issue?** (test from multiple clients)
- **Data issue?** (test specific transaction or report)
- **Integration issue?** (test API endpoint, file transfer)

### 3. Check Status & Logs
- Application logs: `<OPERA_HOME>\logs\`
- Server logs: `<SERVER_HOME>\logs\`
- System event log: Event Viewer
- Database logs: `<DB_HOME>\logs\`
- Network trace if needed: Wireshark

### 4. Apply Solution
- Start with least impactful change
- Document each change made
- Test solution with reproduction steps
- Verify no side effects on other functions

### 5. Document & Close
- Record solution in knowledge base
- Update runbook if applicable
- Schedule monitoring for 24-48 hours
- Close with root cause analysis

---

## Common Issues & Resolutions

### Connectivity & Access Issues

#### "Cannot Connect to Server"
**Symptoms:** Application shows server unreachable, timeout error  
**Root Causes:**
- Server is down or not responding
- Network connectivity broken
- Firewall blocking connection
- Application server port incorrect

**Troubleshooting:**
1. Ping server: `ping <server_hostname>` (check <100ms response)
2. Verify port: `telnet <server> 3050` for OPERA 5
3. Check firewall: Verify port 3050 (or configured port) is open
4. Check server status: Log in to server console, verify OPERA process
5. Review network trace if needed

**Resolution:**
- If server down: Restart OPERA service (Level 2+)
- If firewall blocking: Add exception for workstation (IT)
- If network issue: Contact network team, check switch/router
- If configuration wrong: Update OPERA connection settings

---

#### "Connection Timeout"
**Symptoms:** Application hangs for 30+ seconds, then fails  
**Root Causes:**
- Network latency >500ms
- Server resources exhausted (CPU, memory)
- Connection pool exhausted
- Network packet loss >5%

**Troubleshooting:**
1. Test latency: `ping -c 100 <server>` (check average)
2. Check server resources: Monitor CPU, memory, disk on server
3. Check connection pool: Review server logs for "Max connections exceeded"
4. Monitor network: Test bandwidth with `iperf` or similar

**Resolution:**
- If latency high: Contact network team for optimization
- If server resources low: Add capacity or optimize queries (Level 2+)
- If connection pool exhausted: Tune connection settings in OPERA config
- If network loss: Check switch/router configuration

---

#### "User Cannot Log In"
**Symptoms:** Login prompt appears but password rejected  
**Root Causes:**
- User account inactive or locked
- Password expired
- User does not have OPERA role assigned
- Database connection issue

**Troubleshooting:**
1. Verify user account in Active Directory (domain-joined)
2. Check user role assignment in OPERA database
3. Check account lockout status (failed login attempts)
4. Verify database connectivity to OPERA database

**Resolution:**
- If account locked: Unlock in AD or OPERA user admin
- If role missing: Assign appropriate role (Night Audit, Front Desk, etc.)
- If password expired: Reset in AD
- If DB issue: Check database connectivity (Level 2+)

---

### Performance Issues

#### "Application Responds Slowly"
**Symptoms:** Slow mouse/keyboard response, delayed screen refresh  
**Root Causes:**
- Server resources exhausted (CPU, memory)
- Slow network (latency >200ms)
- Database queries slow
- Client workstation underpowered

**Troubleshooting:**
1. Check server: CPU, memory, disk usage (should be <80%)
2. Check network: `ping`, latency should be <100ms
3. Run slow query log: Identify slow SQL queries
4. Check workstation: CPU, memory usage (should be <70%)

**Resolution:**
- If server overloaded: Scale up resources or load-balance queries
- If network slow: Contact network team
- If slow queries: Optimize SQL, add indexes (Database team)
- If workstation slow: Upgrade hardware or close background apps

---

#### "Reports Take Too Long to Generate"
**Symptoms:** Report request hangs for minutes before result  
**Root Causes:**
- Report queries large dataset
- Database missing indexes
- Server resources low
- Aggregation/sorting in memory inefficient

**Troubleshooting:**
1. Check date range: Is report running on weeks/months of data?
2. Check row count: How many rows in result set? (100k+ is slow)
3. Check server resources during report run
4. Enable query logging: Identify slow SQL statements

**Resolution:**
- If large date range: Suggest shorter period or filtered report
- If many rows: Add indexes or database optimization
- If server overloaded: Run reports at off-peak hours
- If query slow: Refactor SQL or use summary tables (Database team)

---

### Data & Integrity Issues

#### "Folio Amounts Incorrect"
**Symptoms:** Folio total doesn't match manual calculation, charges missing  
**Root Causes:**
- Pending transactions not posted
- Rounding errors on multiple items
- Tax calculation incorrect
- Payment not recorded

**Troubleshooting:**
1. Check reservation: Verify room rate and dates
2. Check charges: Review folio line items (room, tax, fees, charges)
3. Check payments: Verify payment records
4. Check tax config: Verify tax rate and application
5. Review night audit log: Check if folio was adjusted

**Resolution:**
- If pending transaction: Post or cancel transaction
- If rounding error: Verify tax rate configuration
- If tax incorrect: Update tax setup (Night Audit)
- If payment missing: Review payment record (cashier)

---

#### "Reservation Missing from System"
**Symptoms:** Reservation created but cannot find it, no confirmation  
**Root Causes:**
- Reservation created in test property (wrong system)
- Reservation created but not committed
- Reservation creation failed silently
- Reservation deleted

**Troubleshooting:**
1. Check property: Verify you're looking in correct property
2. Check creation: Verify creation date in search
3. Check status: Look for all statuses (Active, Cancelled, No-Show)
4. Check logs: Review application logs for creation errors
5. Check database: Query reservation table directly (Level 2+)

**Resolution:**
- If wrong property: Recreate in correct property
- If creation failed: Verify error message and retry
- If data corrupted: Restore from backup (Level 2+)

---

### Integration Issues

#### "IFC8 Interface File Not Generated"
**Symptoms:** Export time passes but no file in drop directory  
**Root Causes:**
- IFC8 configuration not active
- Scheduled export job failed
- Insufficient permissions on file directory
- Interface server down

**Troubleshooting:**
1. Check IFC8 config: Verify export is scheduled and enabled
2. Check directory: Verify file drop location is accessible
3. Check logs: Review OPERA logs for IFC8 export errors
4. Check interface server: Verify back-office system is running

**Resolution:**
- If config wrong: Update IFC8 interface configuration
- If permissions issue: Fix directory ACLs (IT team)
- If server down: Start interface server (Level 2+)
- If file format issue: Verify expected file format matches config

---

#### "Payment Authorization Failed"
**Symptoms:** Payment form displays error, guest cannot check in  
**Root Causes:**
- Payment terminal offline
- PCI encryption not working
- Payment processor unreachable
- Card details rejected

**Troubleshooting:**
1. Check terminal: Verify payment terminal is connected and responsive
2. Check network: Verify connection to payment processor
3. Check SSL: Verify SSL/TLS certificate is valid
4. Check PCI compliance: Verify card data encrypted

**Resolution:**
- If terminal offline: Power-cycle terminal, check network
- If network issue: Contact network team for outbound HTTPS
- If SSL issue: Install correct certificate (IT team)
- If processor issue: Contact payment provider (Level 2+)

---

## Log File Analysis

### OPERA 5 Logs

**Location:** `C:\Opera5\logs\`

**Key Files:**
- `OPERA.log` - Main application log (DEBUG, INFO, WARN, ERROR)
- `OPERA_database.log` - Database connectivity
- `OPERA_interface.log` - IFC8, integration events
- `OPERA_server.log` - Server-side events (if available)

**Reading Logs:**
```
Tail log in real-time: tail -f OPERA.log
Search for errors: grep ERROR OPERA.log
Find warnings: grep WARN OPERA.log
Get context: grep -B5 -A5 "error_message" OPERA.log
```

### Common Log Patterns

| Pattern | Meaning | Action |
|---------|---------|--------|
| `ERROR.*Connection refused` | Server unavailable | Restart server or check connectivity |
| `ERROR.*Timeout` | Operation took too long | Check server resources |
| `WARN.*Transaction rollback` | Database conflict | Check concurrent access |
| `ERROR.*Permission denied` | User lacks access | Verify user role |
| `ERROR.*Out of memory` | JVM memory exhausted | Increase JVM heap size |

---

## Performance Tuning

### Application Tuning
- **JVM heap:** `-Xmx512m -Xms256m` (adjust based on available RAM)
- **Connection pool:** Tune based on concurrent users
- **Cache settings:** Increase for frequently accessed data
- **Query timeout:** Balance responsiveness with report completion

### Database Tuning
- **Index creation:** Add indexes on frequently searched columns
- **Statistics:** Update table statistics regularly
- **Query optimization:** Rewrite slow queries
- **Partitioning:** For large tables (Level 2+)

### Network Optimization
- **QoS:** Prioritize OPERA traffic on network
- **Compression:** Enable OPERA protocol compression
- **Caching:** Local caching at workstation level
- **Bandwidth:** Monitor and allocate sufficient capacity

---

## Disaster Recovery

### Backup & Recovery Procedure

**Daily Backups:**
- OPERA application files
- OPERA database (full + incremental)
- Configuration files
- Integration files

**Recovery Steps:**
1. Assess damage and recovery point objective
2. Restore database from backup (to point in time)
3. Restore application files if corrupted
4. Restart OPERA services
5. Verify data integrity and business continuity

**Recovery Time Objective (RTO):** <4 hours  
**Recovery Point Objective (RPO):** <1 hour

---

## Escalation Contacts

| Issue Type | Contact | Availability |
|-----------|---------|--------------|
| Workstation/User | Hotel IT Manager | Mon-Fri 7am-7pm |
| Server/Network | Data Center Team | 24/7 |
| Database | Database Administrator | 24/7 |
| OPERA Issues | Oracle Support | See SLA |
| Integration | Integration Team | Mon-Fri 8am-6pm |

---

**Status:** Operational support reference  
**Last Updated:** 2026-06-07  
**Attribution:** Tiago Fitas, Senior Oracle Opera Cloud, OPERA 5, Fidelio Software Engineer Specialist
