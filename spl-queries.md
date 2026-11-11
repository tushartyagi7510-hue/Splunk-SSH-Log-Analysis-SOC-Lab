# SPL Queries – SSH Log Analysis

> These searches use the source, host, and sourcetype filters visible in the submitted Splunk screenshots. Adjust the base search if your Splunk ingestion settings differ.

## 1. Count SSH event types

```spl
source="ssh_logs.json" host="splunkvm" sourcetype="_json"
| stats count by event_type
```

**What it does:** Groups matching events by `event_type` and counts the events in each category.

## 2. Count total matching SSH events

```spl
source="ssh_logs.json" host="splunkvm" sourcetype="_json"
| stats count as total_ssh_connections
```

**What it does:** Counts all events matching the base search and gives the result the field name `total_ssh_connections`.

**Important:** The alias is a chosen label. The query counts events; it does not deduplicate unique SSH sessions.

## 3. Find the top 10 source IPs with failed authentication

```spl
source="ssh_logs.json" host="splunkvm" sourcetype="_json" auth_success=false
| stats count by "id.orig_h"
| sort -count
| head 10
```

**What it does:**
- Filters for events where `auth_success=false`.
- Groups the matching events by originating IP address.
- Counts events per IP.
- Sorts the largest counts first.
- Returns the first 10 rows.

## SPL terms used

- `source`: filters by the input source name.
- `host`: filters by the host value assigned by Splunk.
- `sourcetype`: identifies the event parsing format.
- `stats`: calculates a summary from matching events.
- `count`: counts events.
- `by`: groups results by a field.
- `sort -count`: sorts the `count` field in descending order.
- `head 10`: returns the first 10 rows after sorting.
- `auth_success=false`: selects events whose authentication-success field is false.
- `id.orig_h`: Zeek-style field representing the originating host address.
- `event_type`: event category recorded in the dataset.
