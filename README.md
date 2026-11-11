# Splunk SSH Log Analysis | SOC Lab

**Category:** Security Operations / SIEM / Authentication Log Analysis  
**Tool:** Splunk Enterprise 9.3.0  
**Data:** JSON-formatted Zeek-style SSH logs  
**Events analysed:** 1,200  
**Author:** Tushar Tyagi

## Project Overview

This project demonstrates a basic SOC investigation workflow using SSH-related logs in Splunk. I ingested a JSON dataset, searched the events with Splunk Search Processing Language (SPL), reviewed authentication event categories, counted total SSH events, and identified source IP addresses associated with failed authentication attempts.

The objective was to practise authentication-log analysis and learn how an analyst can identify patterns that may deserve further investigation.

## Objectives

- Ingest and search SSH logs in Splunk.
- Review successful and failed SSH authentication events.
- Identify source IP addresses with the highest number of failed attempts.
- Understand basic SPL aggregation and sorting.
- Document findings without treating suspicious patterns as confirmed compromise.

## Lab Environment

| Component | Details |
|---|---|
| SIEM | Splunk Enterprise 9.3.0 |
| Log format | JSON |
| Dataset | Zeek-style SSH logs |
| Events returned | 1,200 |
| Source filter used in screenshots | `ssh_logs.json` |
| Host filter used in screenshots | `splunkvm` |
| Sourcetype used in screenshots | `_json` |

The lab handout suggests an index such as `ssh_lab` and sourcetype `json`. My screenshots show the uploaded data searched with `source="ssh_logs.json"`, `host="splunkvm"`, and `sourcetype="_json"`. Use the filters that match your own Splunk ingestion settings.

## Investigation Workflow

1. Uploaded the JSON SSH log file through **Splunk Web → Settings → Add Data → Upload**.
2. Confirmed that Splunk returned 1,200 events.
3. Grouped events by `event_type` to understand the authentication event categories.
4. Counted all returned SSH events.
5. Filtered for `auth_success=false`, grouped by originating IP, sorted the counts, and reviewed the top 10.
6. Saved screenshots of the SPL searches and results as evidence.

## SPL Queries

The following searches reproduce the searches visible in the submitted screenshots.

### 1. Count SSH event types

```spl
source="ssh_logs.json" host="splunkvm" sourcetype="_json"
| stats count by event_type
```

**Observed results:**

| Event type | Count |
|---|---:|
| Connection Without Authentication | 286 |
| Failed SSH Login | 305 |
| Multiple Failed Authentication Attempts | 303 |
| Successful SSH Login | 306 |
| **Total** | **1,200** |

### 2. Count total SSH events

```spl
source="ssh_logs.json" host="splunkvm" sourcetype="_json"
| stats count as total_ssh_connections
```

**Observed result:** 1,200 events.

Note: this query counts the events returned by the base search. It is labelled `total_ssh_connections` in the SPL, but the result is technically a count of matching log events. Do not assume it represents 1,200 unique network sessions unless the dataset's event structure confirms that.

### 3. Top 10 source IPs for failed authentication

```spl
source="ssh_logs.json" host="splunkvm" sourcetype="_json" auth_success=false
| stats count by "id.orig_h"
| sort -count
| head 10
```

**Top results visible in the screenshot:**

| Source IP | Failed-auth events |
|---|---:|
| 10.0.0.25 | 22 |
| 10.0.0.46 | 20 |
| 10.0.0.48 | 20 |
| 10.0.0.21 | 19 |
| 10.0.0.22 | 19 |
| 10.0.0.24 | 17 |
| 10.0.0.33 | 17 |
| 10.0.0.18 | 16 |
| 10.0.0.16 | 15 |
| 10.0.0.30 | 15 |

## Findings and SOC Interpretation

- The search returned 1,200 SSH-related events.
- The event-type breakdown includes 305 failed SSH login events and 303 events labelled as multiple failed authentication attempts.
- The highest source-IP count in the failed-authentication search is `10.0.0.25`, with 22 events.
- The top 10 results provide a shortlist of source IPs for further review.

Repeated failed logins can be consistent with password guessing or brute-force activity, but counts alone do not prove an attack. An analyst should inspect timestamps, usernames, target hosts, source reputation, and whether a successful login followed the failures.

## Further Investigation as a SOC Analyst

If this were a live environment, I would:

- Review failed attempts over time to identify bursts or repeated patterns.
- Identify the destination host and targeted usernames for each source IP.
- Check whether any successful login occurred after repeated failures from the same source.
- Correlate SSH activity with Linux authentication logs, endpoint telemetry, firewall records, and asset ownership.
- Validate suspicious source addresses using approved threat-intelligence sources.
- Escalate only when the combined evidence supports it.

These are recommended next steps, not detections completed in this lab.

## Evidence

Screenshots are stored in the [`screenshots/`](screenshots/) directory.

1. [SSH event type breakdown](screenshots/01-ssh-event-type-breakdown.png)
2. [Total SSH events](screenshots/02-total-ssh-events.png)
3. [Top source IPs for failed logins](screenshots/03-top-source-ips-failed-logins.png)

## Dataset Attribution

The lab instructions reference this sample dataset:

[SSH logs JSON – 30 Days SOC Challenge Beginner](https://raw.githubusercontent.com/0xrajneesh/30-Days-SOC-Challenge-Beginner/refs/heads/main/ssh_logs.json)

The dataset source is credited here. This repository contains my own lab notes, SPL searches, interpretation, and screenshots.

## Skills Demonstrated

- Splunk Enterprise fundamentals
- JSON log ingestion and searching
- SSH authentication event analysis
- SPL: `stats`, `count`, `sort`, and `head`
- Failed-login source IP investigation
- Basic SOC triage and evidence documentation

## Disclaimer

This is an educational lab using a sample dataset. The results are not evidence of a real-world attack or confirmed compromise. The IP addresses shown are from the supplied lab data.
