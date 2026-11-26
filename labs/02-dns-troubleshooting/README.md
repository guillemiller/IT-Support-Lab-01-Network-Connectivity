# IT Support Lab 02 – DNS Troubleshooting: Missing Internal A Record

## Overview

This project documents a simulated IT support incident involving an internal benefits portal that became unreachable by hostname while public websites remained accessible.

The investigation identified a missing internal DNS A record. The record had been deleted during a DNS cleanup change even though the web application was still responding at its known IP address.

**Environment:** JoeLearns Labs – simulated environment  
**Incident ID:** INC-1187  
**Client workstation:** HR-WS03  
**DNS server:** DC01  
**Web server:** WEB-INT01  
**Internal domain:** northstar.local

> **Disclaimer:** This is a simulated training exercise completed in JoeLearns Labs. It does not represent production DNS or Active Directory administration experience.

## 1. Incident Description

Employees could access public websites, but the internal benefits portal at `benefits.northstar.local` failed with a browser message indicating that the server IP address could not be found.

The web server team reported that the application was operational.

## 2. Investigation

### Step 1 – Verify client DNS configuration

On HR-WS03, `ipconfig /all` showed:

| Parameter | Result |
|---|---|
| Client IPv4 address | 192.168.30.63 |
| Default gateway | 192.168.30.1 |
| Configured DNS server | 192.168.30.10 (DC01) |

The client was already using the authorised internal DNS server. Changing the client to a public resolver was therefore neither necessary nor appropriate for an Active Directory internal hostname.

### Step 2 – Test name resolution

Executed:

```powershell
nslookup benefits.northstar.local
```

The DNS server returned **NXDOMAIN** (name does not exist).

A DNS resource-record query on DC01 found no matching `benefits` record in the `northstar.local` zone.

### Step 3 – Review the DNS change audit

The audit for change **CHG-7702** showed that `benefits.northstar.local` had been deleted with the address `192.168.30.45`.

The application inventory identified **WEB-INT01** as the owner of that hostname.

### Step 4 – Validate the application before restoring DNS

The WEB-INT01 application health inspection confirmed that the benefits portal was functioning and that HTTPS was responding at:

`192.168.30.45:443`

This established that the service was available by its known IP address and that the missing DNS record was the relevant fault.

## 3. Root Cause

During DNS cleanup change **CHG-7702**, the internal A record for `benefits.northstar.local` was mistakenly deleted.

The web application remained operational at `192.168.30.45`, but clients could not resolve its hostname through the authorised internal DNS server.

## 4. Resolution

1. Restored the authorised A record in the Active Directory-integrated `northstar.local` DNS zone:
   - Host: `benefits`
   - FQDN: `benefits.northstar.local`
   - Address: `192.168.30.45`
2. Flushed the DNS resolver cache on HR-WS03 (simulated action corresponding to `ipconfig /flushdns`).
3. Re-ran the DNS lookup from HR-WS03.

## 5. Validation

Executed:

```powershell
nslookup benefits.northstar.local
```

The lookup succeeded and returned:

| Parameter | Result |
|---|---|
| DNS server | dc01.northstar.local |
| DNS server address | 192.168.30.10 |
| Resolved hostname | benefits.northstar.local |
| Resolved IPv4 address | 192.168.30.45 |

JoeLearns' restoration checklist reached **3/3**, and the simulator confirmed that incident **INC-1187** was resolved.

## 6. Investigation Approach and Skills Practiced

- Windows client DNS configuration review (`ipconfig /all`)
- DNS name-resolution troubleshooting with `nslookup`
- Understanding NXDOMAIN responses
- Active Directory-integrated DNS concepts
- DNS A records and internal hostnames
- Reviewing change/audit evidence
- Validating application availability by IP and HTTPS port
- DNS resolver cache flushing
- Root-cause analysis and incident documentation
- Applying the minimum necessary change and validating service restoration

## 7. Key Takeaways

- When public websites work but an internal hostname fails, verify the client's configured DNS server before changing network settings.
- An NXDOMAIN response indicates that the queried name does not exist in the DNS response context; it does not, by itself, prove that the application server is offline.
- Validate the service at its known IP before restoring a DNS record.
- Use audit records and application ownership information to restore only the authorised record.
- Flush the client cache after correcting DNS, then verify resolution again.
- Avoid replacing an internal DNS resolver with a public resolver for Active Directory internal names.

## 8. Evidence

Screenshots from the JoeLearns simulation are organised in the `evidence/` folder:

- `01-incident-ticket.png` – initial incident and environment
- `02-client-dns-configuration.png` – HR-WS03 DNS configuration
- `03-nxdomain-result.png` – failed lookup for the internal hostname
- `04-dns-audit-review.png` – CHG-7702 audit evidence
- `05-web-application-health.png` – application responding at 192.168.30.45:443
- `06-a-record-restored.png` – authorised A record restored
- `07-client-dns-cache-flushed.png` – client resolver cache flushed
- `08-successful-nslookup.png` – hostname resolves to 192.168.30.45
- `09-incident-closed.png` – final incident resolution screen

