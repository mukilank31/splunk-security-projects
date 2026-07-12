# E-Commerce Web Security Monitoring using Splunk

A Splunk-based Security Operations (SOC) project that simulates real-world web application monitoring for an e-commerce platform, using the **Buttercup Games** dataset (Splunk's official tutorial data).

## 📌 Project Overview

This project demonstrates how a SOC Analyst uses Splunk to ingest, search, and analyze web server logs to detect suspicious activity, monitor application health, and build actionable alerting for an e-commerce environment.

- **Data Source:** Buttercup Games tutorial dataset (`tutorialdata.zip`) — web access logs from `www1`, `www2`, `www3` servers
- **Total Events Indexed:** 109,864
- **Tool Used:** Splunk Enterprise
- **Focus Areas:** Web security monitoring, anomaly detection, dashboarding, alerting

## 🎯 Objectives

- Ingest and index raw web access logs into Splunk
- Write SPL (Search Processing Language) queries to detect security-relevant patterns
- Build a dashboard to visualize key metrics in real time
- Configure an automated hourly alert for time-sensitive detection

## 🛠️ Setup

1. Downloaded and extracted `tutorialdata.zip` (Splunk's official Buttercup Games sample dataset)
2. Indexed the data into Splunk Enterprise
3. Verified event count and field extraction (109,864 events across `www1`, `www2`, `www3` access logs)
4. Built searches, dashboard panels, and an alert on top of the indexed data

## 🔍 Detection Use Cases (SPL Queries)

### 1. Top 10 Traffic Sources by Client IP
```spl
index=main sourcetype=access_combined
| top limit=10 clientip
```
**Purpose:** Identifies the most active client IPs hitting the web servers, helping surface potential bots, scrapers, or reconnaissance activity hiding in normal traffic volume.

**Sample Output:**

| clientip | count |
|---|---|
| 87.194.216.51 | 1036 |
| 211.166.11.101 | 736 |
| 128.241.220.82 | 597 |
| 194.215.205.19 | 487 |
| 188.138.40.166 | 429 |

![Top 10 Traffic Sources](/top10-traffic-sources.png)

---

### 2. Abnormal Purchase Volume by IP
```spl
index=main sourcetype=access_combined action=purchase
| stats count by clientip
| sort -count
```
**Purpose:** Flags client IPs making an unusually high number of purchase actions — a pattern consistent with automated checkout abuse, carding attempts, or bot-driven fraud rather than genuine customer behavior.

**Sample Output:**

| clientip | count |
|---|---|
| 87.194.216.51 | 134 |
| 128.241.220.82 | 95 |
| 211.166.11.101 | 91 |
| 107.3.146.207 | 72 |

![Abnormal Purchase Volume](docs/abnormal-purchase-volume.png)

---

### 3. Error Distribution by Server Source
```spl
index=main sourcetype=access_combined status>=400
| stats count by status source
| sort -count
```
**Purpose:** Breaks down HTTP error codes (`404`, `500`, `503`) by the originating server source (`www1`, `www2`, `www3`), helping quickly spot whether errors are isolated to one server (possible targeted attack/misconfiguration) or spread across the environment (possible broader outage or scan).

**Sample Output:**

| status | source | count |
|---|---|---|
| 503 | tutorialdata.zip:.\www3\access.log | 329 |
| 503 | tutorialdata.zip:.\www1\access.log | 324 |
| 503 | tutorialdata.zip:.\www2\access.log | 299 |
| 500 | tutorialdata.zip:.\www2\access.log | 262 |
| 404 | tutorialdata.zip:.\www1\access.log | 244 |

![Error Distribution by Server Source](docs/error-distribution.png)

---

### 4. Failed/Error Status by Client IP
```spl
index=main sourcetype=access_combined status>=400
| stats count by clientip
| sort -count
```
**Purpose:** Correlates HTTP failures (4xx/5xx) back to the client IP generating them, highlighting IPs that are repeatedly hitting broken or restricted endpoints — a common signature of vulnerability scanning or brute-force probing.

**Sample Output:**

| clientip | count |
|---|---|
| 87.194.216.51 | 142 |
| 211.166.11.101 | 94 |
| 128.241.220.82 | 78 |
| 194.215.205.19 | 71 |

## 📊 Dashboard

All four use cases above were combined into a single **Web Security Monitoring Dashboard** for quick SOC triage — giving an analyst a single-pane view of traffic sources, purchase anomalies, error trends, and top offending IPs.

![Web Security Monitoring Dashboard](docs/dashboard-overview.png)

## 🚨 Alerting

An **hourly scheduled alert** was configured in Splunk to automatically flag suspicious activity as it occurs, rather than relying on manual searches.

- **Base Search:** `index=main sourcetype=access_combined status>=400 | stats count by clientip | where count > 50`
- **Alert Frequency:** Every 1 hour (cron: `0 * * * *`)
- **Trigger Condition:** Number of results > 0 (i.e., any client IP crossing the error-count threshold in the last hour)
- **Alert Action:** Logged in Splunk's Triggered Alerts / notification

## 🧠 Key Learnings

- Hands-on experience with Splunk data ingestion and index management
- Writing SPL queries (`stats`, `top`, `sort`, field filtering) for real security detection scenarios
- Building analyst-ready dashboards for faster triage
- Configuring time-based alerting for proactive monitoring rather than reactive searching

## 🗂️ Repository Structure

```
splunk-security-projects/
└── ecommerce-web-security-monitoring/
    ├── README.md
    └── docs/
        ├── top10-traffic-sources.png
        ├── abnormal-purchase-volume.png
        ├── error-distribution.png
        └── dashboard-overview.png
```

## 👤 Author

**Mukilan K**
B.E. Computer Science Engineering | Aspiring SOC Analyst / VAPT Engineer
[LinkedIn](https://linkedin.com/in/mukilan-k-a14790326) | mukilank31@gmail.com
