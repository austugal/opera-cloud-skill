---
name: workstation-setup-guide
title: OPERA PMS Workstation Setup and Configuration
author: Tiago Fitas, Senior Oracle Opera Cloud, OPERA 5, Fidelio Software Engineer Specialist
date: 2026-06-07
credentials_removed: yes
---

# OPERA PMS Workstation Setup and Configuration

Complete guide for configuring Windows workstations to run OPERA PMS client applications (OPERA 5 and OPERA Cloud).

---

## Hardware Requirements

### Minimum Specifications
- **CPU:** Intel Core i5 (2.0 GHz) or equivalent
- **RAM:** 8 GB
- **Storage:** 200 MB free (for application and cache)
- **Display:** 1024x768 minimum (1920x1080 recommended)
- **Network:** Gigabit Ethernet or WiFi 802.11ac

### Peripherals
- USB ports for SFTP key devices or payment terminals
- Printer support (network or USB)
- Optional: Magnetic card reader for payment processing
- Optional: PIN pad for secure payment entry

---

## Operating System Requirements

### Supported Windows Versions
- Windows Server 2016 or later
- Windows 10 (21H2 or later)
- Windows 11

### Required OS Features
- Java Runtime Environment (JRE) 8u331 or later
- Internet Explorer 11 (for legacy support)
- Microsoft Edge (for modern web-based interfaces)
- Edge Administrative Templates (Group Policy)

---

## Software Installation & Configuration

### Automated Setup (Recommended)

**OPERA 5 Workstation Setup Utility**

1. Obtain the O5WSSetup folder package
2. Copy entire folder to target workstation
3. From elevated command prompt, run: `O5Setup.cmd`
4. Utility automatically installs:
   - JRE 8u331+ (if missing)
   - Internet Explorer 11 components
   - Microsoft Edge browser
   - Microsoft Edge Admin templates
   - Adobe Reader (checked and validated)

#### Setup Validation
The utility performs these checks:
- [ ] JRE version and installation path
- [ ] IE11 Windows feature enabled
- [ ] Edge browser installed
- [ ] Edge administrative template applied
- [ ] Adobe Reader installed (optional)

### Manual Setup Steps (if automated fails)

#### Step 1: Install Java Runtime Environment (JRE)
```
Download: JRE 8u331+ from Oracle
URL: https://www.oracle.com/java/technologies/downloads/
Installation:
  - Accept license terms
  - Install to default path: C:\Program Files\Java\jre1.8.0_331
  - Do NOT add to PATH (OPERA manages this)
  - Accept desktop shortcuts if offered
```

#### Step 2: Enable IE11 Features
```
Windows 10/11:
  - Control Panel > Programs > Programs and Features
  - Click "Turn Windows features on or off"
  - Check: "Internet Explorer 11"
  - Apply and reboot if prompted
```

#### Step 3: Configure Java Deployment
```
Global configuration for all users:
Path: C:\Windows\Sun\Java\Deployment

Required files:
  - java.policy (permissions)
  - deployment.properties (settings)
  - trusted.certs (certificates)

OPERA workstation setup configures these automatically.
```

#### Step 4: Add Application Server to Java Exception List
```
Purpose: Allow self-signed or internal CA certificates

File: C:\Windows\Sun\Java\Deployment\deployment.properties
Add: deployment.security.exception.sites=<property_application_server>

Example: deployment.security.exception.sites=opera.internal.company.com
```

#### Step 5: Configure Internet Explorer Security Zones
```
IE Settings > Security > Local Intranet > Sites

Add OPERA application server URLs:
  - http://<property_server>
  - https://<property_server>
  
Add to "Trusted Sites" if using self-signed certificates.
```

#### Step 6: Configure Microsoft Edge IEMode (if required)
```
Purpose: Route legacy applications to IE11 compatibility mode

File: C:\Program Files\Microsoft\Edge\Application\IEMode.xml
(or via Group Policy: Computer Configuration > Administrative Templates > Microsoft Edge > Configure IE Integration)

Example:
  <site url="opera.internal.company.com">
    <compat-mode>IE11</compat-mode>
  </site>
```

---

## Printer Configuration

### Network Printer Setup

1. **Add Printer to Windows:**
   ```
   Settings > Printers & scanners > Add a device
   Search for network printer by hostname or IP
   Complete wizard and set as default (if applicable)
   ```

2. **Configure in OPERA PMS:**
   - Workstation Setup menu
   - Add printer: Select from Windows printer list
   - Assign to output destinations:
     - Folio print job
     - Report output
     - Check-in/Check-out documents
   - Test print from OPERA

### Print Task Configuration

**OPERA 5 Print Tasks:**
- Folio header, body, footer templates
- Check-in/out document layout
- Report formatting
- Custom fields mapping

**OPERA Cloud Print Tasks:**
- Cloud Print API configuration
- Cloud Print device assignment
- Cloud-based template management

### Common Printer Issues

| Issue | Resolution |
|-------|-----------|
| Printer not visible in OPERA | Verify printer installed in Windows; restart OPERA client |
| Print jobs queued but not printing | Check printer driver; restart print spooler |
| Incorrect folio layout | Verify print task template in OPERA; test with sample folio |
| Font substitution on reports | Install required fonts; regenerate report |
| Slow print performance | Check network connectivity; upgrade printer firmware |

---

## User Account Permissions

### Workstation User Roles

| Role | Permissions | Use Case |
|------|-----------|----------|
| **Front Desk Agent** | Reservations, check-in, payments, profiles | Guest-facing operations |
| **Housekeeping Supervisor** | Room status, inspections, task management | Housekeeping operations |
| **Night Audit** | Night audit functions, end-of-day close, cashier | Nightly operations |
| **Manager** | Reports, configurations, user management | Management oversight |
| **System Admin** | System configuration, user accounts, backups | Workstation administration |

### Local User Account Setup
```
Windows:
  - Create local admin account for OPERA application installation
  - Create standard user accounts for daily operations
  - Apply Group Policy for password complexity and timeout

OPERA:
  - Map each user account to OPERA user with appropriate role
  - Set workstation-level default values (printer, format, etc.)
```

---

## Network Configuration

### Connectivity Requirements

**For OPERA 5 (On-Premises):**
- Direct network connectivity to OPERA 5 application server
- Typical port: TCP 3050 (Oracle Tuxedo communication)
- Network latency: <100ms recommended
- Bandwidth: 1 Mbps minimum (10 Mbps+ recommended)

**For OPERA Cloud:**
- Outbound HTTPS (port 443) to OHIP infrastructure
- No inbound port requirements
- Network latency: <500ms acceptable
- Bandwidth: 2 Mbps+ recommended for real-time sync

### Proxy/Firewall Configuration

**If behind corporate proxy:**
```
Proxy Configuration (OPERA Cloud):
  - Proxy protocol: HTTP CONNECT
  - Authentication: Basic or NTLM
  - Configure in browser settings or system proxy

Java proxy settings:
  File: C:\Windows\Sun\Java\Deployment\deployment.properties
  deployment.proxy.type=manual
  deployment.proxy.http=<proxy_server>
  deployment.proxy.http.port=<port>
```

### SFTP Key Generation (for integrations)

Many OPERA integrations (IFC8, exports) require SFTP key authentication:

1. **Generate Key Pair:**
   ```
   Tool: PuTTYgen or OpenSSH
   Key type: RSA (2048-bit minimum)
   Passphrase: <strong_password>
   Save private key (PuTTY format or OpenSSH)
   Export public key
   ```

2. **Deploy Public Key:**
   - Provide public key to IT/integration team
   - Deploy to SFTP server
   - Test connection with private key

3. **Secure Storage:**
   - Store private key on encrypted drive
   - Use Windows Credential Manager (optional)
   - Never transmit over email or insecure channels

---

## Group Policy Configuration (Enterprise)

### Recommended Policies for OPERA Workstations

| Policy | Setting | Purpose |
|--------|---------|---------|
| Password Policy | Min 12 chars, complexity required | Security |
| Account Lockout | 5 attempts, 30-minute lockout | Prevent brute-force |
| Session Timeout | 30 minutes inactivity | Security compliance |
| Windows Update | Auto-install, reboot 2am weekdays | Patch management |
| IE Security Zones | Add OPERA servers to Trusted Sites | Reduce security warnings |
| Edge IEMode | Route legacy apps to IE11 | Application compatibility |
| Printer Deployment | Deploy shared printers via GPO | Standardized setup |

### Applying Policies
```
Domain-joined workstations:
  - Create OPERA Workstations OU in Active Directory
  - Create Group Policy Object (GPO)
  - Link GPO to OPERA Workstations OU
  - Configure computer and user policies
  - Run: gpupdate /force (on workstation)
  - Verify: gpresult /h report.html
```

---

## Troubleshooting & Validation

### Pre-Launch Checklist
- [ ] JRE installed and version verified (8u331+)
- [ ] IE11 enabled and functional
- [ ] Edge browser installed
- [ ] Java deployment files in place
- [ ] Application server added to exception list
- [ ] Network connectivity to app server tested (ping, TCP 3050)
- [ ] Printer installed and tested
- [ ] User account created with proper role assignment
- [ ] Desktop shortcut created and working

### Connection Testing
```
Verify OPERA application server is reachable:
  ping <property_server>  (should respond, <100ms)
  telnet <property_server> 3050  (should connect)

Verify network latency:
  pathping <property_server>  (check hop count, latency)
```

### Log File Locations (OPERA 5)
```
Client logs: C:\Opera5\logs\
Server logs: <OPERA5_HOME>\logs\
```

### Common Issues & Resolutions

| Issue | Cause | Resolution |
|-------|-------|-----------|
| "Java not found" | JRE not installed | Run O5Setup.cmd or manual JRE install |
| "Cannot connect to server" | Network issue, server down | Verify connectivity, check server status |
| "Print job fails silently" | Printer not configured | Add printer in Windows, assign in OPERA |
| "Slow application response" | Network latency, server load | Check latency <100ms, monitor server |
| "Self-signed cert warning" | Default Java security | Add server to Java exception list |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 5.6.20.8 | May 2022 | IE11/Edge admin template fixes, JRE 8u333 |
| 5.6.20.7 | May 2022 | Admin template installation fix |
| 5.6.20.6 | May 2022 | Adobe Reader validation, Edge policy updates |
| 5.6.20.5 | May 2022 | JRE 8u331 update, GUI refresh |

---

## Security Best Practices

1. **Minimize Privileges:** Use standard user accounts for daily operations; admin only for setup
2. **Network Isolation:** Separate OPERA network segment if possible
3. **Access Audit:** Monitor and log all workstation access
4. **Encryption:** Use VPN for remote access, TLS 1.2+ for all connections
5. **Patching:** Apply Windows/Java/browser updates monthly
6. **Physical Security:** Lock workstations when unattended
7. **Password Management:** Enforce complexity, regular rotation, no sharing
8. **Incident Response:** Document procedure for workstation compromise or malware

---

## Related Documentation
- OPERA 5 Administration Guide
- OPERA Cloud Mobile Configuration
- Network & Security Architecture
- Disaster Recovery Procedures

---

**Status:** Operational reference guide  
**Last Updated:** 2026-06-07  
**Attribution:** Tiago Fitas, Senior Oracle Opera Cloud, OPERA 5, Fidelio Software Engineer Specialist
