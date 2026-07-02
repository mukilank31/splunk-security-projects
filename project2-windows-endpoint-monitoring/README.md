# Windows Endpoint Security Monitoring — Splunk SOC Project

## Objective
Built a SOC-style monitoring project using Splunk Enterprise to analyze 
real-time Windows Security Event Logs from own laptop, identifying 
credential access patterns, privilege usage, and authentication activity, 
mapped to the MITRE ATT&CK framework.

## Dataset
- Source: Windows Security Event Logs (own laptop, live collection)
- Total Events Analyzed: 3,399
- Unique EventCodes: 14
- Collection Period: June 30 – July 1, 2026

## Key Findings

### EventCode Distribution
| EventCode | Count | Description |
|---|---|---|
| 5379 | 2,928 | Credential Manager credentials read |
| 4624 | 120 | Successful logon |
| 4672 | 116 | Special privileges assigned to new logon |
| 4798 | 69 | Local group membership enumerated |
| 5058 | 63 | Key file operation |
| 5061 | 62 | Cryptographic operation |

EventCode 5379 (Credential Manager access) dominated activity at 86% of 
total events, indicating frequent credential store interaction — mapped 
to detection use case for credential access monitoring.

### Failed Logon Analysis
Identified 5 failed logon attempts on local account with "Unknown user 
name or bad password" errors — analyzed as routine authentication errors 
rather than brute-force indicators, based on frequency and pattern.

### Privilege Usage by Account
| Account | Count |
|---|---|
| SYSTEM | 111 |
| mukil | 4 |
| Splunkd | 1 |

### Credential Access Pattern (Time-based)
Identified activity spikes at specific hourly windows (e.g., 964 events 
at 13:30, 1,397 events at 11:30 on July 1), useful for baseline behavior 
analysis and anomaly detection.

## MITRE ATT&CK Mapping
- **T1555.004** — Credentials from Password Stores: Windows Credential 
  Manager (EventCode 5379)
- **T1069.001** — Permission Groups Discovery: Local Groups (EventCode 
  4798, 4799)

## What I Built
- Custom Splunk dashboard with EventCode breakdown, failed logon table, 
  privilege usage chart, and credential access timeline
- SPL search queries for each detection use case
- Scheduled alert for anomalous credential access volume
- PDF report export for documentation

## Tools Used
Splunk Enterprise, SPL (Search Processing Language), Windows Event 
Viewer, MITRE ATT&CK Navigator
