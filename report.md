# SOC Lab Report – Splunk SSH Log Analysis

## Executive Summary

A sample set of 1,200 JSON-formatted Zeek-style SSH events was searched in Splunk Enterprise 9.3.0. Three SPL searches were used to review event categories, count matching events, and identify the top source IP addresses associated with failed authentication.

The exercise demonstrated basic SSH authentication-log analysis and source-IP triage. The aggregate results identify activity worth contextual review but do not independently confirm a brute-force attack or compromised account.

## Key Results

| Analysis | Result |
|---|---|
| Matching events | 1,200 |
| Failed SSH Login | 305 |
| Multiple Failed Authentication Attempts | 303 |
| Successful SSH Login | 306 |
| Connection Without Authentication | 286 |
| Highest failed-auth source count | `10.0.0.25` – 22 events |

## Analysis

The dataset contains both successful and unsuccessful SSH-related event categories. The failed-authentication search shows several source IPs with repeated failures. This can be relevant to brute-force triage, but the available screenshots do not show a time sequence, targeted usernames, destination systems, or whether a successful login followed the failures.

## Limitations

- The evidence consists of aggregate counts rather than a full timeline.
- No account compromise or successful brute-force attack was confirmed.
- The total-count SPL counts matching events, not necessarily unique sessions.
- The screenshots represent a sample lab dataset, not production telemetry.

## Recommended Follow-up

Review the raw events for timestamps, usernames, destination hosts, and sequences of failures followed by success. Correlate with Linux authentication logs and other network or endpoint telemetry. Validate any suspicious source IPs before escalation.

## Conclusion

This lab strengthened practical familiarity with Splunk search, SSH authentication fields, SPL aggregation, and basic SOC investigation reporting.
