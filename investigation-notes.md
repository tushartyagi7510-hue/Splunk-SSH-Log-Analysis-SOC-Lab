# Investigation Notes – SSH Log Analysis

## Scope

Review the sample SSH-related events ingested into Splunk and summarise:
- Event categories (`event_type`)
- Total events returned by the base search
- Source IP addresses associated with `auth_success=false`

## Search context

- Source: `ssh_logs.json`
- Host filter: `splunkvm`
- Sourcetype: `_json`
- Events returned: 1,200

The exact filters depend on the Splunk upload configuration. The searches in this project reproduce the filters visible in the evidence screenshots.

## Analyst observations

### Event categories

| Event type | Count |
|---|---:|
| Connection Without Authentication | 286 |
| Failed SSH Login | 305 |
| Multiple Failed Authentication Attempts | 303 |
| Successful SSH Login | 306 |

The counts total 1,200. The labels are taken from the dataset's `event_type` field. A category label does not by itself establish the full sequence or context of an authentication attempt.

### Total event count

The aggregate search returned 1,200 matching events. The SPL aliases this count as `total_ssh_connections`; technically, it is a count of matching events, not necessarily a count of unique SSH sessions.

### Failed-authentication source IPs

The top visible source IP is `10.0.0.25` with 22 failed-authentication events. `10.0.0.46` and `10.0.0.48` each have 20.

These are private IPv4 addresses. In a real environment, the analyst should identify the assets and users associated with these addresses before assigning risk.

## Assessment

**Outcome:** Basic SSH authentication-log analysis completed.  
**Incident status:** Suspicious patterns for review; no confirmed brute-force attack or compromise established by these aggregations alone.

## Recommended follow-up

Inspect event timestamps, destination hosts, usernames, authentication sequences, and any successful logins after repeated failures. Correlate with host authentication logs and network telemetry before escalating.
